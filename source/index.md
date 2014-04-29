---
title: API Reference

language_tabs:
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

---

# Introduction


There are many cloud storage options for consumers. However each provider has their own API and API design philosophy, making it harder for developers to easily integrate their applications.

The idea behind filefog is to provide a single consistent, promise-based API for developers to access user uploaded data, without having to worry about differing OAuth implementations, base urls or data parsing.

The filefog philosophy is as follows:

- __Don't reinvent the wheel__ - if a native library is available for NodeJS, use it. All we need  to do then is wrap the required methods to create a consistent user experience, and let the API designers themselves deal with most fo the implementation details.
- __Avoid Callback Hell__ - Javascript and most libraries built on-top of it heavily make use of callbacks. While this is perfectly fine for some, I prefer the Promise design pattern for its reliability, simplicity and synchronous feel when working with pseudo-filesystems.
- __Implementation Agnostic API__ - The API endpoints and API responses will both be wrapped so that developers should be able to swap out providers with little to no code changes.
- __Plugin Architecture__ - API Providers can be added as needed with minimal configuration, as needed.

<aside class="warning">
WARNING: Please note that this package is under heavy development, and the unified API may change unexpectedly. Once the API is stable, a version 1.0 will be released on NPM.

</aside>

# Providers

There are a number of cloud service providers, and filefog creates a unified API to communicate with many of the most popular.
filefog supports a plugin architecture and you can mix and match providers as you need them.
By default only the `Local filesystem` provider is included with filefog, but the following service providers are already available as plugins for filefog:

- Dropbox
- Google Drive
- Microsoft OneDrive
- Box.net

The following provider plugins are planned for development

- Bittorrent Sync
- SpiderOak
- AnyCloud

# Quick Start

> To use, just require `filefog`

```javascript
var filefog = require('filefog')
```

> This configuration should only be run once during setup

```javascript
filefog.setConfig({

        skydrive : {
          client_key : '',
          client_secret : '',
          client_scope : "wl.basic wl.emails wl.offline_access wl.skydrive_update"
        },
        box : {
          client_key : 'cch3sssk23ueqsbdh2k2zlv2i7rz06lp',
          client_secret : '6v7ywbCdut5FRdIjDeREofrFGc2ymGmA'
        },
        dropbox : {
          client_key : 'sl47p7pijvtp73h',
          client_secret : 'j6vluc5yq7dxnj6'
        }
    });
```

> Create a provider of of your choice

```javascript
var googleProvider = filefog.provider('google');
```
> If you do not have access_tokens for the current user, generate a Authorize Url to redirect the user to (for OAuth flows)

```javascript
googleProvider.oAuthGetAuthorizeUrl()
```

> Or intialize a client using previously saved access_tokens

```javascript
googleProvider.CreateClient({
  access_token: 'ya29.1.AADtN_W5vWUjYklaE_ZNkJBd2y9Ot60jF4SewLivnLgLHDYhmiadzbPPZntHmL4nceXs0w',
  refresh_token: '1/IMcWJR9KJPVguuKLUrHzMjRl-XECLf6mK2YoUYnbQaU'
})
.then(function (client) {
  return client.GetFolderInformation();
})
.then(function (response) {
  assert.equal(response.mimeType, 'application/vnd.google-apps.folder');
  assert.equal(response.title, 'My Drive');
})
```

# FileSystem Definitions
By default the provider responses are translated to a unified filesystem implementation. This is done via translation files contained in each provider plugin.
Response translation can be disabled globally or a per request basis. The original response is always accessable via the _raw property

## File

> A file transformation may look something like this

```javascript
transform.is_file = true;
transform.is_folder = false;
transform.etag = file_response.etag;
transform.identifier = file_response.id;
transform.parent_identifier = file_response.parent.id;
transform.mimetype = ""
transform.created_date = new Date(file_response.created_at);
transform.modified_date = new Date(file_response.modified_at);
transform.name = file_response.name;
transform.description = file_response.description;
//transform.extension = file_response.name.split('.')
transform.checksum = file_response.sha1;
transform.file_size = file_response.size;
transform._raw = file_response;
```

The unified file implementation has the following properties (if available)


Property Name | Type | Example | Description
------------- | ---- | ------- | --------------
is_file | Boolean | true | Determines if the object is a file
is_folder | Boolean | false | Determines if object is a folder
etag | String | e3432d343 | Specifies the files etag
identifier | String | '/public/folder_name/test file.txt' or '123d34343' | Specifies the file identifier for the provider
parent_identifier | String | '/public/folder_name' or '123232ddd' | Specifies the identifier of the parent of the current file
mimetype | String | 'text/plain' | The mimetype of the file if available
created_date | Date | March 6, 2014 10:00:02 PST | The created date for the file
modified_date | Date | March 7, 2014, 11:00:00 PST | The modified/updated at date for the file
name | String | 'test file.txt' | The filename including extension
description | String | 'Contains test data for file upload' | The description of the file
extension | String | '.txt' | The extension of the file including period `.`
checksum | String | '595f44fec1e92a71d3e9e77456ba80d1' | Used to verify the integrity of the cloud file
_raw | Object | {} | Raw response as a Javascript object
## Folder
> A folder transformation may look something like this

```javascript
transform.is_file = false;
transform.is_folder = true;
transform.etag = folder_response.etag;
transform.identifier = folder_response.id;
transform.parent_identifier = folder_response.parent.id;
transform.created_date = new Date(folder_response.created_at);
transform.modified_date = new Date(folder_response.modified_at);
transform.name = folder_response.name;
transform.description = folder_response.description;
transform._raw = folder_response;
```

The unified folder implementation has the following properties (if available)


Property Name | Type | Example | Description
------------- | ---- | ------- | --------------
is_file | Boolean | false | Determines if the object is a file
is_folder | Boolean | true | Determines if object is a folder
etag | String | e3432d343 | Specifies the files etag
identifier | String | '/public/folder_name' or '123d34343' | Specifies the folder identifier for the provider
parent_identifier | String | '/public' or '123232ddd' | Specifies the identifier of the parent of the current folder
created_date | Date | March 6, 2014 10:00:02 PST | The created date for the folder
modified_date | Date | March 7, 2014, 11:00:00 PST | The modified/updated at date for the folder
name | String | 'folder_name' | The folder name
description | String | 'Test folder' | The description of the folder
_raw | Object | {} | Raw response as a Javascript object

## Account
> An account transformation may look something like this

```javascript
transform.name = account_response.name;
transform.email = account_response.login;
transform.avatar_url = account_response.avatar_url;
transform.created_date = new Date(account_response.created_at);
transform.modified_date = new Date(account_response.modified_at);
transform.id = account_response.id;
transform._raw = account_response;
```

The unified account implementation has the following properties (if available)


Property Name | Type | Example | Description
------------- | ---- | ------- | --------------
name | String | 'Jason Kulatunga' | The full name/nickname of the account owner
email | String | 'test@example.com' | Email address of account owner
avatar_url | String | 'http://www.example.com/img/avatar.png' | The avatar/profile image url for the account holder.
created_date | Date | March 6, 2014 10:00:02 PST | The created date for the account
modified_date | Date | March 7, 2014, 11:00:00 PST | The modified/updated at date for the account
id | String | '12e32a32ddd' | The identifier for the account
_raw | Object | {} | Raw response as a Javascript object

## Quota
> A quota transformation may look something like this

```javascript
transform.total_bytes = quota_response.space_amount; //
transform.used_bytes = quota_response.space_used; //bytes used.
transform.limits= {
    upload_size : quota_response.max_upload_size
}
```

The unified account implementation has the following properties (if available)


Property Name | Type | Example | Description
------------- | ---- | ------- | --------------
total_bytes | String | '32000000' | Total space allocated to the account holder in bytes
used_bytes | String | '5000' | Total space used by the account holder in bytes
limits | Object | {} | Any limits specified by the provider
limits.upload_size | String | 5000000 | The maximum size of any file uploaded to the provider
_raw | Object | {} | Raw response as a Javascript object


# filefog Methods
## register

```javascript
var filefog = require('filefog');
filefog.register('google', new googleProvider({
    client_key : '777041726477-a5o1tp6f3i9m1me3tj5vhpnrn1jge43c.apps.googleusercontent.com',
    client_secret : 'mWURYHmMKZxr6aeR7DTjRu-q',
    client_scope : "https://www.googleapis.com/auth/drive",
    callback_url : "http://www.mywebsite.com/auth/google"
}));


var googleProvider = new filefog.provider('google');
```

Registers a cloud provider handler

###Arguments
Name | Type | Required | Description
------------- | ---- | ------- | --------------
service | String | __yes__ |  This is an string specifing the provider you want to register eg. "box", "dropbox", "google", "skydrive", etc
provider_options | Object | no | One or more of the options below:
provider.redirect_url | String | no | Overrides the redirect url for this cloud service._

###Returns
Type | Description
------------- | --------------
Constructor | Returns a constructor for the specified provider.


## provider

```javascript
var filefog = require('filefog');
var googleProvider = new filefog.provider('google');
```

Returns an instance of the specified cloud provider.

###Arguments
Name | Type | Required | Description
------------- | ---- | ------- | --------------
service | String | __yes__ |  This is an string specifing the provider you want to create eg. "box", "dropbox", "google", "skydrive", etc
provider_options | Object | no | One or more of the options below:
provider.redirect_url | String | no | Overrides the redirect url for this cloud service._

###Returns
Type | Description
------------- | --------------
Constructor | Returns a constructor for the specified provider.


#Provider Methods
##oAuthGetAuthorizeUrl
Returns the URL of the third party service’s authorization page. This is the URL that your application should forward the user to in first leg of  OAuth

###Arguments
__none__

###Returns
Type | Description
------------- | --------------
String | The authorization URL that the user must visit to start the OAuth flow.


##oAuthGetAccessToken
Used to swap the code provided by the OAuth redirect webhook into an access token (and refresh token if applicable)

###Arguments
Name | Type | Required | Description
------------- | ---- | ------- | --------------
oauth_resp | Object | __yes__ |  An authorization code or verifier you retrieved in the first leg of OAuth 2
oauth_resp.code | String | __maybe__ | If you are completing OAuth 2 flow, this option should be specified.
oauth_resp.verifier | String | __maybe__ | If you are completing an OAuth 1 flow, this option should be specified.


###Returns
Type | Description
------------- | --------------
Promise |  A promise that will successfully resolve into an `Object` with the following parameters

Name | Type | Description
------------- | ---- | --------------
access_token | String | OAuth access token
refresh_token | String | OAuth refresh token, if available
access_token_secret | String | OAuth access token secret, if available
_raw | Object | the raw OAuth response, containing any additional data sent with the access_token

## Authentication













# Test
```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace `meowmeowmeow` with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Isis",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/3"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Isis",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">If you're not using an administrator API key, note that some kittens will return 403 Forbidden if they are hidden for admins only.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the cat to retrieve

