# Multisite Bootstrap - 3.8.1

Standard bootstrap process

 * `/index.php`
 * `. /wp-blog-header.php`
	* `. /wp-load.php`
		* `ABSPATH` is defined
		* `ABSPATH . wp-config.php` (or one level up)
			* DB constants, `MULTISITE`, `SUBDOMAIN_INSTALL`, etc are defined
			* `ABSPATH . wp-settings.php`
				* `WPINC` is defined (wp-includes)
				* `ABSPATH . WPINC . /load.php`
				* `ABSPATH . WPINC . /default-constants.php`
				* `global $wp_version, $wp_db_version, $tinymce_version, $required_php_version, $required_mysql_version`
				* `ABSPATH . WPINC . /version.php` sets version globals
				* `wp_initial_constants()` defines initial constants
				* `timer_start()` sets up the global `$timestart`
				* if `WP_CACHE`, include the advanced cache drop-in
					* `WP_CONTENT_DIR . /advanced-cache.php`
				* `ABSPATH . WPINC . /compat.php`
				* `ABSPATH . WPINC . /functions.php`
					* `ABSPATH . WPINC . /option.php`
				* `ABSPATH . WPINC . /class-wp.php` - WP class is available, not instantiated
				* `ABSPATH . WPINC . /class-wp-error.php` - WP Error class is available, not instantiated
				* `ABSPATH . WPINC . /plugin.php`
					* `global $wp_filter, $wp_actions, $merged_filters, $wp_current_filter`
				* `ABSPATH . WPINC . /pomo/mo.php`
					* `ABSPATH . WPINC . /pomo/translations.php`
						* `ABSPATH . WPINC . /pomo/entry.php` - Translation_Entry
					* `ABSPATH . WPINC . /pomo/streams.php`
				* `require_wp_db()`
					* `ABSPATH . WPINC . /wp-db.php`
					* if `WP_CONTENT_DIR . /db.php` exists
						* `WP_CONTENT_DIR . /db.php`
					* `$wpdb = new wpdb()`
				* `wp_start_object_cache()`
				* `ABSPATH . WPINC . /default-filters.php`

Multisite bootstrap begins here, continuing under the load of `ABSPATH . wp-settings.php` if `MULTISITE`, `SUBDOMAIN_INSTALL`, `VHOST`, or `SUNRISE` are truthy.

* `ABSPATH . WPINC . /ms-blogs.php`
* `ABSPATH . WPINC . /ms-settings.php`
	* `ABSPATH . WPINC . /ms-load.php`
	* `ABSPATH . WPINC . /ms-default-constants.php`
	* if `SUNRISE`
		* `WP_CONTENT_DIR . /sunrise.php`
	* `ms_subdomain_constants()`, un-conflicts `SUBDOMAIN_INSTALL` and `VHOST`

Much of the following sections is skipped if `$current_site` and `$current_blog` are setup properly by `sunrise.php`.

* if `! isset $current_site || ! isset $current_blog`
	* `$domain = SERVER['HTTP_HOST']`
	* `$domain` = strip ports 80, 443 | report error if other port (???)
	* `$domain`= rtrim .
	* `$cookie_domain = $domain`
	* `$cookie_domain = strip www.`
	* `$path = SERVER['REQUEST_URI']` | preg_replace php
	* `$path` = str_replace wp-admin
	* `$path` = preg_replace after first /path/ entry of URI
	* `$current_site = wpmu_current_site()`

## Finding the Network

A process to set the `$current_site` global is triggered in `wpmu_current_site()`:

* if defined `DOMAIN_CURRENT_SITE` and `PATH_CURRENT_SITE` (from `wp-config.php`)
	* `$current_site->id` = `SITE_ID_CURRENT_SITE` or 1
	* `$current_site->domain` = `DOMAIN_CURRENT_SITE`
	* `$current_site->path = $path = PATH_CURRENT_SITE`
	* if defined `BLOG_ID_CURRENT_SITE`
		* `$current_site->blog_id = BLOG_ID_CURRENT_SITE`
	* if defined `BLOGID_CURRENT_SITE` (deprecated)
		* `$current_site->blog_id = BLOGID_CURRENT_SITE`
	* if `DOMAIN_CURRENT_SITE == $domain`
		* `$current_site->cookie_domain = $cooke_domain`
		* else
			* `$current_site->cookie_domain = $current_site->domain` and strip `www.`
	* `return`

So if `DOMAIN_CURRENT_SITE` and `PATH_CURRENT_SITE` are defined before `wpmu_current_site()` is called, these will be used to set the current network's information. If not, we continue.

* if `$current_site = wp_cache_get( 'current_site', 'site-options' );`
	* `return`

If a previous attempt stored the result in cache, and we're able to retrieve that. This will be used for the `$current_site` global. If not, we continue.

* `$sites = SELECT * FROM $wpdb->site`

We first query for all of the networks in the site table. Most likely there is only one, because most installations are not multi-network. This one will be used to fill in the info we need.

* if 1 == count `$sites`
	* `$current_site = $sites[0]`
	* `$path = $current_site->path`
	* `$current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path`
	* `$current_site = get_current_site_name( $current_site )`
		* `$current_site->site_name = wp_cache_get`
		* if `! $current_site->site_name`
			* `$current_site->site_name = meta_value FROM $wpdb-sitemeta WHERE site_id = $current_site->id AND meta_key = 'site_name'`
			* if `! $current_site->site_name`
				* `$current_site->site_name = ucfirst $current_site->domain`
	* if `www . $current_site_domain`
		* `$current_site->cookie_domain` = strip www. from `$current_site->domain`
	* `return`

With only 1 network available, we fill in the global with that information, then use the matching record from the blogs table to specify the primary site the network is managed on.

In the rare case that more than one network exists, we continue.

* `$path` = first `/path/` of `$_SERVER['REQUEST_URI']`

The `$path` is captured as the first subfolder of the requested path. This means we can only support networks that are one folder deep in core directly.

* if `$domain == $cookie_domain`
	* `$current_site = $wpdb->site WHERE domain = $domain AND path = $path`
	* else
		* `$current_site = $wpdb->site WHERE domain In ( $domain, $cookie_domain ) AND path = $path`

For some reason I'm not entirely versed on yet, we don't really allow a network to have separate `www.domain.com` and `domain.com` sites, or at least we make it pretty confusing. The cookie domain is definitely `domain.com`, which makes it so that general authentication cookies can be used across subdomains.

With the above logic, we ask the sites table for the network that matches this exact domain and path.

* if ! `$current_site`
	* if `$domain` == `$current_domain`
		* `$current_site = $wpdb->site WHERE domain = $domain AND path = /`
		* else
			* `$wpdb->site WHERE domain IN ( $domain, $cookie_domain ) AND path = '/'`

In the above, we look for a network that uses `/` as the root path, matching only the domain. At this point, we're starting to assume that the requested URL is probably for an individual site on the network and not the main network itself.

* if `$current_site`
	* `$path = $current_site->path`
	* `$current_site->cookie_domain = $cookie_domain`
	* `return`

If one is found using this method, we probably can assume it's a subfolder multisite install. We've matched a domain to the network, we've assigned the `$path` global correctly as such. We've setup the expected cookie domain, and we're ready to go find the individual site.

If nothing has shown up yet, then we move into subdomain territory.

* if `is_subdomain_install()`
	* `$sitedomain` = strip $domain of first foo.
	* `$current_site = SELECT * FROM $wpdb->site WHERE domain = $sitedomain AND path = $path`
	* if `$current_site`
		* `$current_site->cookie_domain = $current_site->domain`
		* `return`

We found the domain and the path amongst our networks, which makes for an interesting URL structure such as this:

* `http://site1.network1.com/common/`
* `http://site2.network1.com/common/`

The two sites share the network's path even though they live on different subdomains.

* `$current_site = SELECT * FROM $wpdb->site WHERE domain = $sitedomain AND path = /`
* if `$current_site || defined WP_INSTALLING`
	* `$path = /`
	* `return`

In the more common scenario, we look for the domain stripped of the subdomain and a path of `/`. If a site is found, we set the network's path accordingly and move on to the site level. This makes for a URL structure such as:

* `http://site1.network1.com/`
* `http://site2.network1.com`

And if we can't find a network with the requested information, we die with an error message.

* `wp_die()`

## Finding the SiteI

And `$current_site` has been established through `wpmu_current_site()`. Time to figure out `$current_blog`.

* if ! isset $current_site->blog_id
	* $current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path
* if is_subdomain_install()
	* $current_blog = wp_cache_get current_blog_$domain site-options
	* if ! $current_blog
		* $current_blog = get_blog_details domain = $domain
		* if $current_blog
			* wp_cache_set current_blog_domain $current_blog site-options
	* if $current_blog && $current_blog->site_id != $current_site->id
		* $current_site = $wpdb->site WHERE id = $current_blog->site_id
		* if ! isset $current_site->blog_id
			* $current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path
	* else
		* $blogname = strip first sub from $domain
* else
	* $blogname = stripped version of $path
	* $blogname = strip /
	* $blogname = strip ?query_vars=after
	* $reserved_blognames = page, comments, blog, wp-admin, wp-includes, wp-content, files, feed
	* if $blogname

Still processing

	if ( is_subdomain_install() ) {
		$current_blog = wp_cache_get( 'current_blog_' . $domain, 'site-options' );
		if ( !$current_blog ) {
			$current_blog = get_blog_details( array( 'domain' => $domain ), false );
			if ( $current_blog )
				wp_cache_set( 'current_blog_' . $domain, $current_blog, 'site-options' );
		}
		if ( $current_blog && $current_blog->site_id != $current_site->id ) {
			$current_site = $wpdb->get_row( $wpdb->prepare( "SELECT * FROM $wpdb->site WHERE id = %d", $current_blog->site_id ) );
			if ( ! isset( $current_site->blog_id ) )
				$current_site->blog_id = $wpdb->get_var( $wpdb->prepare( "SELECT blog_id FROM $wpdb->blogs WHERE domain = %s AND path = %s", $current_site->domain, $current_site->path ) );
		} else
			$blogname = substr( $domain, 0, strpos( $domain, '.' ) );
	} else {
		$blogname = htmlspecialchars( substr( $_SERVER[ 'REQUEST_URI' ], strlen( $path ) ) );
		if ( false !== strpos( $blogname, '/' ) )
			$blogname = substr( $blogname, 0, strpos( $blogname, '/' ) );
		if ( false !== strpos( $blogname, '?' ) )
			$blogname = substr( $blogname, 0, strpos( $blogname, '?' ) );
		$reserved_blognames = array( 'page', 'comments', 'blog', 'wp-admin', 'wp-includes', 'wp-content', 'files', 'feed' );
		if ( $blogname != '' && ! in_array( $blogname, $reserved_blognames ) && ! is_file( $blogname ) )
			$path .= $blogname . '/';
		$current_blog = wp_cache_get( 'current_blog_' . $domain . $path, 'site-options' );
		if ( ! $current_blog ) {
			$current_blog = get_blog_details( array( 'domain' => $domain, 'path' => $path ), false );
			if ( $current_blog )
				wp_cache_set( 'current_blog_' . $domain . $path, $current_blog, 'site-options' );
		}
		unset($reserved_blognames);
	}

	if ( ! defined( 'WP_INSTALLING' ) && is_subdomain_install() && ! is_object( $current_blog ) ) {
		if ( defined( 'NOBLOGREDIRECT' ) ) {
			$destination = NOBLOGREDIRECT;
			if ( '%siteurl%' == $destination )
				$destination = "http://" . $current_site->domain . $current_site->path;
		} else {
			$destination = 'http://' . $current_site->domain . $current_site->path . 'wp-signup.php?new=' . str_replace( '.' . $current_site->domain, '', $domain );
		}
		header( 'Location: ' . $destination );
		die();
	}

	if ( ! defined( 'WP_INSTALLING' ) ) {
		if ( $current_site && ! $current_blog ) {
			if ( $current_site->domain != $_SERVER[ 'HTTP_HOST' ] ) {
				header( 'Location: http://' . $current_site->domain . $current_site->path );
				exit;
			}
			$current_blog = get_blog_details( array( 'domain' => $current_site->domain, 'path' => $current_site->path ), false );
		}
		if ( ! $current_blog || ! $current_site )
			ms_not_installed();
	}

	$blog_id = $current_blog->blog_id;
	$public  = $current_blog->public;

	if ( empty( $current_blog->site_id ) )
		$current_blog->site_id = 1;
	$site_id = $current_blog->site_id;

	$current_site = get_current_site_name( $current_site );

	if ( ! $blog_id ) {
		if ( defined( 'WP_INSTALLING' ) ) {
			$current_blog->blog_id = $blog_id = 1;
		} else {
			wp_load_translations_early();
			$msg = ! $wpdb->get_var( "SHOW TABLES LIKE '$wpdb->site'" ) ? ' ' . __( 'Database tables are missing.' ) : '';
			wp_die( __( 'No site by that name on this system.' ) . $msg );
		}
	}
}
$wpdb->set_prefix( $table_prefix, false ); // $table_prefix can be set in sunrise.php
$wpdb->set_blog_id( $current_blog->blog_id, $current_blog->site_id );
$table_prefix = $wpdb->get_blog_prefix();
$_wp_switched_stack = array();
$switched = false;

// need to init cache again after blog_id is set
wp_start_object_cache();

// Define upload directory constants
ms_upload_constants();



		wp();

	require ABSPATH . WPINC . /template-loader.php