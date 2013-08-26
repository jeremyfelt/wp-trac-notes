# Component: Multisite

## Action Ready

### Likely Commit

* #25030 [link](http://core.trac.wordpress.org/ticket/25030) - add is_main_network()
* #19451 [link](http://core.trac.wordpress.org/ticket/19451) - add stage of signup to existing filter
* #20075 [link](http://core.trac.wordpress.org/ticket/20075) - add hooks to wp-activate
* #25020 [link](http://core.trac.wordpress.org/ticket/25020) - sitemeta filter in populate_network()
* #20651 [link](http://core.trac.wordpress.org/ticket/20651) - pre-deletion hook for site options
* #20793 [link](http://core.trac.wordpress.org/ticket/20793) - fix notice in wpmu_create_blog()
* #25046 [link](http://core.trac.wordpress.org/ticket/25046) - is_email_address_unsafe should be case insensitive

### Likely Close

* #20861 [link](http://core.trac.wordpress.org/ticket/20861) - accessing custom permastructs across sites
* #19879 [link](http://core.trac.wordpress.org/ticket/19879) - better caching for get_dirsize()

### Discussion / Review

* #21662 [link](http://core.trac.wordpress.org/ticket/21662) - filter signup text
* #24434 [link](http://core.trac.wordpress.org/ticket/24434) - add mp4 as a default allowed filetype
* #24061 [link](http://core.trac.wordpress.org/ticket/24061) - introduce blog-archived.php and blog-spam.php [Related #17164](http://core.trac.wordpress.org/ticket/17164)
* #20825 [link](http://core.trac.wordpress.org/ticket/20825) - User activation in multisite should happen in the main blog
* #17397 [link](http://core.trac.wordpress.org/ticket/17397) - Inconsistency in allowed site addresses
* #12832 [link](http://core.trac.wordpress.org/ticket/12832) - Schema change for site status
* #23709 [link](http://core.trac.wordpress.org/ticket/23709) - populate_network() tweaks related to wildcards

### Needs Work

* #16201 [link](http://core.trac.wordpress.org/ticket/16201) - when updating a multisite blog subblogs getting 404 error
	* #20171 [link](http://core.trac.wordpress.org/ticket/20171) - Categories, tag links broken multisite subfolder install
* #21352 [link](http://core.trac.wordpress.org/ticket/21352) - wp_lostpassword_url() tweaks
* #15706 [link](http://core.trac.wordpress.org/ticket/15706) - Allow wildcarded domains in multisite limited email domains

### Long Term / Refactor

* #21143 [link](http://core.trac.wordpress.org/ticket/21143) - In which @nacin mentions rewriting ms-settings.php

## Closed

### Committed

* #23650 [link](http://core.trac.wordpress.org/ticket/23650) - make get_space_allowed filterable
* #19753 [link](http://core.trac.wordpress.org/ticket/19753) - show network name in network admin page titles
* #18186 [link](http://core.trac.wordpress.org/ticket/18186) - default to 'none' for the registration site option

### Not Committed

* #20220 [link](http://core.trac.wordpress.org/ticket/20220) - add check for sunrise, basic commit or close
* #22160 [link](http://core.trac.wordpress.org/ticket/22160) - eTags in ms-files
* #20854 [link](http://core.trac.wordpress.org/ticket/20854) - prevent notice by using absint()