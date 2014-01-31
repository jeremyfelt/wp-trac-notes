/index.php
require . /wp-blog-header.php
	require . /wp-load.php
		define ABSPATH
		require ABSPATH . wp-config.php | or one dir up
			define DB, MULTISITE, SUBDOMAIN_INSTALL, 
			require ABSPATH . wp-settings.php
				define WPINC | wp-includes
				require ABSPATH . WPINC . /load.php | loads functions
				require ABSPATH . WPINC . /default-constants.php | loads functions
				global $wp_version, $wp_db_version, $tinymce_version, $required_php_version, $required_mysql_version
				require ABSPATH . WPINC . /version.php
					set $wp_version, $wp_db_version, $tinymce_version, $required_php_version, $required_mysql_version
				wp_initial_constants()
					DEFINE ...
				timer_start()
					global $timestart
				if WP_CACHE
					include WP_CONTENT_DIR . /advanced-cache.php
				require ABSPATH . WPINC . /compat.php
				require ABSPATH . WPINC . /functions.php
					require ABSPATH . WPINC . /option.php
				require ABSPATH . WPINC . /class-wp.php | WP class (not instantiated)
				require ABSPATH . WPINC . /class-wp-error.php | WP Error (not instantiated)
				require ABSPATH . WPINC . /plugin.php
					global $wp_filter, $wp_actions, $merged_filters, $wp_current_filter
				require ABSPATH . WPINC . /pomo/mo.php
					require ABSPATH . WPINC . /pomo/translations.php
						require ABSPATH . WPINC . /pomo/entry.php | Translation_Entry
					require ABSPATH . WPINC . /pomo/streams.php
				require_wp_db()
					require ABSPATH . WPINC . /wp-db.php
					if file_exists WP_CONTENT_DIR . /db.php
						require WP_CONTENT_DIR . /db.php
					$wpdb = new wpdb()
				wp_start_object_cache()
				require ABSPATH . WPINC . /default-filters.php
				if MULTISITE | SUBDOMAIN_INSTALL | VHOST | SUNRISE
					require ABSPATH . WPINC . /ms-blogs.php
					require ABSPATH . WPINC . /ms-settings.php
						require ABSPATH . WPINC . /ms-load.php
						require ABSPATH . WPINC . /ms-default-constants.php
						if SUNRISE
							require WP_CONTENT_DIR . /sunrise.php
						ms_subdomain_constants | un-conflict SUBDOMAIN_INSTALL & VHOST
						if ! isset $current_site || ! isset $current_blog
							$domain = SERVER['HTTP_HOST']
							$domain = strip ports 80, 443 | report error if other port (???)
							$domain = rtrim .
							$cookie_domain = $domain
							$cookie_domain = strip www.
							$path = SERVER['REQUEST_URI'] | preg_replace php
							$path = str_replace wp-admin
							$path = preg_replace after first /path/ entry of URI
							$current_site = wpmu_current_site()
								if defined DOMAIN_CURRENT_SITE && PATH_CURRENT_SITE
									$current_site->id = SITE_ID_CURRENT_SITE or 1
									$current_site->domain = DOMAIN_CURRENT_SITE
									$current_site->path = $path = PATH_CURRENT_SITE
									if defined BLOG_ID_CURRENT_SITE
										$current_site->blog_id = BLOG_ID_CURRENT_SITE
									if defined BLOGID_CURRENT_SITE | deprecated
										$current_site->blog_id = BLOGID_CURRENT_SITE
									if DOMAIN_CURRENT_SITE == $domain
										$current_site->cookie_domain = $cooke_domain
										else
											$current_site->cookie_domain = $current_site->domain | strip www.
									return
								if $current_site = wp_cache_get( 'current_site', 'site-options' );
									return
								$sites = SELECT * FROM $wpdb->site
								if 1 == count $sites
									$current_site = $sites[0]
									$path = $current_site->path
									$current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path
									$current_site = get_current_site_name( $current_site )
										$current_site->site_name = wp_cache_get
										if ! $current_site->site_name
											$current_site->site_name = meta_value FROM $wpdb-sitemeta WHERE site_id = $current_site->id AND meta_key = 'site_name'
											if ! $current_site->site_name
												$current_site->site_name = ucfirst $current_site->domain
									if www . $current_site_domain
										$current_site->cookie_domain = strip www. from $current_site->domain
									return
								$path = first path of $_SERVER['REQUEST_URI']
								if $domain == $cookie_domain
									$current_site = $wpdb->site WHERE domain = $domain AND path = $path
									else
										$current_site = $wpdb->site WHERE domain In ( $domain, $cookie_domain ) AND path = $path
								if ! $current_site
									if $domain == $current_domain
										$current_site = $wpdb->site WHERE domain = $domain AND path = /
										else
											$wpdb->site WHERE domain IN ( $domain, $cookie_domain ) AND path = '/'
								if $current_site
									$path = $current_site->path
									$current_site->cookie_domain = $cookie_domain
									return
								if is_subdomain_install
									$sitedomain = strip $domain of first foo.
									$current_site = SELECT * FROM $wpdb->site WHERE domain = $sitedomain AND path = $path
									if $current_site
										$current_site->cookie_domain = $current_site->domain
										return
									$current_site = SELECT * FROM $wpdb->site WHERE domain = $sitedomain AND path = /
								if $current_site || defined WP_INSTALLING
									$path = /
									return
								wp_die()
							### $current_site established ###
							if ! isset $current_site->blog_id
								$current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path
							if is_subdomain_install()
								$current_blog = wp_cache_get current_blog_$domain site-options
								if ! $current_blog
									$current_blog = get_blog_details domain = $domain
									if $current_blog
										wp_cache_set current_blog_domain $current_blog site-options
								if $current_blog && $current_blog->site_id != $current_site->id
									$current_site = $wpdb->site WHERE id = $current_blog->site_id
									if ! isset $current_site->blog_id
										$current_site->blog_id = $wpdb->blogs WHERE domain = $current_site->domain AND path = $current_site->path
								else
									$blogname = strip first sub from $domain
							else
								$blogname = stripped version of $path
								$blogname = strip /
								$blogname = strip ?query_vars=after
								$reserved_blognames = page, comments, blog, wp-admin, wp-includes, wp-content, files, feed
								if $blogname


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