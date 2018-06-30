# Basic Authentication handler
This plugin adds Basic Authentication to a WordPress site.

Note that this plugin requires sending your username and password with every
request, and should only be used for development and testing. We strongly
recommend using the [OAuth 1.0a][oauth] authentication handler for production.

## Installing
1. Download the plugin into your plugins directory
2. Enable in the WordPress admin

## Using
This plugin adds support for Basic Authentication, as specified in [RFC2617][].
Most HTTP clients will allow you to use this authentication natively. Some
examples are listed below.

### cURL

```sh
curl --user admin:password http://example.com/wp-json/
```

### WP_Http

```php
$args = array(
	'headers' => array(
		'Authorization' => 'Basic ' . base64_encode( $username . ':' . $password ),
	),
);
```

[oauth]: https://github.com/WP-API/OAuth1
[RFC2617]: https://tools.ietf.org/html/rfc2617

--- 

## Forcing all API requests to require authorisation

A few notes on making this work on servers with php-cgi.

### .htaccess
If you're using php-cgi you'll need to tweak the .htaccess file slightly. (more info about the issue: https://github.com/LearningLocker/learninglocker/issues/131)

Change the Wordpress block in .htaccess in the root folder to: 

```
# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [E=HTTP_AUTHORIZATION:%{HTTP:Authorization},L]
</IfModule>

# END WordPress
```

Then you'll want to stop Wordpress from overwriting this if you save permalinks. 

Add this to your functions.php file: (You'll still be able to update your permalinks don't worry...)

```
// Stop WordPress from modifying .htaccess permalink rules
add_filter('flush_rewrite_rules_hard','__return_false');
```

### Check for authorisation

Now we're able to get the right headers when running php-cgi, add this to your functions.php file: 

What we're doing here is returning the result if they are logging in, or returning an error if they are not logged in. 

```
// Check user is logged in before returning API results
add_filter('rest_pre_dispatch', function ($result) {

    global $DI;

    if (is_user_logged_in()) {
        return $result;
    } else {
        return [
            'error_code' => 'rest_requires_authentication',
            'message'    => 'Using REST requires authentication.',
            'status'     => '403'
        ];
    }
});


```
