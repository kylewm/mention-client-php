Webmention Client
=================

Client library for sending [webmention](http://indiewebcamp.com/webmention) and [pingback](http://indiewebcamp.com/pingback) notifications.

[![Build Status](https://travis-ci.org/indieweb/mention-client-php.png?branch=master)](http://travis-ci.org/indieweb/mention-client-php)

Usage
-----

### Basic Usage

Given one of your source URLs, this function will find external links on the page,
discover the webmention and pingback endpoints for each, and send mentions for any
it discovers.

```php
<?php
$client = new IndieWeb\MentionClient();
$client->debug(true);
$sent = $client->sendMentions($sourceURL);

echo "Sent $sent mentions\n";
?>
```

The library will fetch the source URL, parse it, and look for the first h-entry,
h-card or h-event. It will then attempt to send webmentions (and pingbacks) to
all URLs found in the object, as either properties or inside the "content" HTML.

The library attempts to find the endpoints by doing a HEAD request to the target URL
and looking at the headers, and if none are found, then it makes a GET request
and searches the body of the page for the rel values.

After finding either pingback or webmention endpoints, the request is sent to each.

Alternatively, you can pass in HTML as the second parameter and the library will
look for ALL absolute links in the HTML instead of fetching your post contents and
looking for the microformats object.

```php
<?php
$client = new IndieWeb\MentionClient();
$client->debug(true);
$sent = $client->sendMentions($sourceURL, $sourceHTML);

echo "Sent $sent mentions\n";
?>
```

### Discovering the Webmention endpoint

Given a target URL, this function will return its webmention endpoint
if found, otherwise it will return false.

```php
<?php
$client = new IndieWeb\MentionClient();
$client->debug(true);
$endpoint = $client->discoverWebmentionEndpoint($targetURL);
$endpoint = $client->discoverPingbackEndpoint($targetURL);
?>
```

### Sending a Webmention

To send a webmention to a target URL, you can use the function below. This will
first discover the webmention endpoint of the target, and if found, will then
send the webmention payload to it. You can pass an additional parameter to include
other properties in the payload.

```php
<?php
$client = new IndieWeb\MentionClient();
$client->debug(true);

$response = $client->sendWebmention($sourceURL, $targetURL);
$response = $client->sendWebmention($sourceURL, $targetURL, ['vouch'=>$vouch]);
?>
```

If no webmention endpoint was found at the target, the function will return false.
See the function below for an example of the response when the webmention is successful.


### Sending a Pingback

```php
<?php
$client = new IndieWeb\MentionClient();
$client->debug(true);

$response = $client->sendPingback($sourceURL, $targetURL);
?>
```

### Sending the Webmention or Pingback directly

To send the actual webmention or pingback payload, you can use the static functions below.
You can pass additional properties for the webmention request in an array if needed.

```php
<?php
$response = IndieWeb\MentionClient::sendWebmention($endpoint, $source, $target);
$response = IndieWeb\MentionClient::sendWebmention($endpoint, $source, $target, ['vouch'=>$vouch]);

$response = IndieWeb\MentionClient::sendPingback($endpoint, $source, $target);
?>
```

The response is an array containing the HTTP status code, HTTP headers, and the response body:

```json
{
  "code": 202,
  "headers": {
    "Content-type: text/plain"
  },
  "body": "Webmention is processing"
}
```

You can check if the webmention was accepted by testing if the response code is 200 or 202.


About Webmention
----------------

To learn more about Webmention, see [webmention.net](http://webmention.net).

The [webmention.io](http://webmention.io/) service can also act as a pingback->webmention
proxy which will allow you to accept pingbacks as if they were sent as webmentions.


About Pingback
--------------

If you want to accept pingbacks on your site, check out [webmention.io](http://webmention.io/#use-it)
which handles accepting the XMLRPC request and exposes the data via an API.


License
-------

Copyright 2013 by Aaron Parecki

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
