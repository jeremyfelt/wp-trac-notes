# Component: Multisite

## Action Ready

### Likely Commit

* #20171 [link](http://core.trac.wordpress.org/ticket/20171) - Categories, tag links broken multisite subfolder install
* #25478 [link](http://core.trac.wordpress.org/ticket/25478) - Add style class to wp-activate and wp-signup
* #25584 [link](http://core.trac.wordpress.org/ticket/25584) - Add action to `wpmu_delete_blog()`

### Likely Close

* #20825 [link](http://core.trac.wordpress.org/ticket/20825) - User activation in multisite should happen in the main blog
* #25901 [link](http://core.trac.wordpress.org/ticket/25901) - Redirect after activation not working.

### Early 3.9

* #13554 [link](http://core.trac.wordpress.org/ticket/13554) - weird canonical redirects in MS
* #15706 [link](http://core.trac.wordpress.org/ticket/15706) - Allow wildcarded domains in multisite limited email domains
* #17630 [link](http://core.trac.wordpress.org/ticket/17630) - Redirect when expected on wp-signup.php
* #16293 [link](http://core.trac.wordpress.org/ticket/16293) - Users with ID 1/2 cannot be deleted
* #18117 [link](http://core.trac.wordpress.org/ticket/18117) - slash on site-info.php path
* #19879 [link](http://core.trac.wordpress.org/ticket/19879) - better caching for get_dirsize()
* #21352 [link](http://core.trac.wordpress.org/ticket/21352) - wp_lostpassword_url() tweaks
* #21573 [link](http://core.trac.wordpress.org/ticket/21573) - NOBLOGREDIRECT
* #22277 [link](http://core.trac.wordpress.org/ticket/22277) - Admin add user without confirmation email
* #26317 [link](http://core.trac.wordpress.org/ticket/26317) - Filter new site notification email

### Punted from 3.8, review in 3.9

* #17904 [link](http://core.trac.wordpress.org/ticket/17904) - Multisite has more restrictions on user login character set
* #18934 [link](http://core.trac.wordpress.org/ticket/18934) - Remove old WPMU code around caps
* #23197 [link](http://core.trac.wordpress.org/ticket/23197) - Load plugins during wp-activate.php

### Discussion / Review

* #15801 [link](http://core.trac.wordpress.org/ticket/15801) - Deactivate vs Deleted
* #17376 [link](http://core.trac.wordpress.org/ticket/17376) - Multisite subfolders and wp-admin issues
* #17397 [link](http://core.trac.wordpress.org/ticket/17397) - Inconsistency in allowed site addresses
	* #20019 [link](http://core.trac.wordpress.org/ticket/20019) - Allow '.' and '-' in blog names
* #20774 [link](http://core.trac.wordpress.org/ticket/20774) - Flagging user/site as spam
* #23709 [link](http://core.trac.wordpress.org/ticket/23709) - populate_network() tweaks related to wildcards
* #24434 [link](http://core.trac.wordpress.org/ticket/24434) - add mp4 as a default allowed filetype
* #25178 [link](http://core.trac.wordpress.org/ticket/25178) - Huge users select lists when deleting a user

### Long Term / Refactor

* #12002 [link](http://core.trac.wordpress.org/ticket/12002) - Collision detection with main site prefix
* #17948 [link](http://core.trac.wordpress.org/ticket/17948) - refactor login/registration
	* #20075 [link](http://core.trac.wordpress.org/ticket/20075) - add hooks to wp-activate
* #21143 [link](http://core.trac.wordpress.org/ticket/21143) - In which @nacin mentions rewriting ms-settings.php
