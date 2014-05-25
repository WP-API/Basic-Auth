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
