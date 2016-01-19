# Type Definitions
By default the provider responses are translated to a unified filesystem implementation. This is done via translation files contained in each adapter plugin.
Response translation can be disabled globally or a per request basis. The original response is always accessable via the _raw property

## File

 A file transformation may look something like this

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
A folder transformation may look something like this

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
 An account transformation may look something like this

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
 A quota transformation may look something like this

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

