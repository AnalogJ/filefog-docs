# Welcome to Filefog Docs

[![npm version](https://badge.fury.io/js/filefog.svg)](https://badge.fury.io/js/filefog) [![wercker status](https://app.wercker.com/status/e3b6989233fa01c9c5673f3dec84dc15/s "wercker status")](https://app.wercker.com/project/bykey/e3b6989233fa01c9c5673f3dec84dc15)

There are many cloud storage options for consumers. However each provider has their own API and API design philosophy, making it harder for developers to easily integrate their applications.

The idea behind FileFog is to provide a single consistent, promise-based API for developers to access user uploaded data, without having to worry about differing OAuth implementations, base urls or data parsing.

The FileFog philosophy is as follows:

- __Don't reinvent the wheel__ - if a native library is available for NodeJS, use it. All we need to do then is wrap the required methods to create a consistent user experience, and let the API designers themselves deal with most fo the implementation details.
- __Avoid Callback Hell__ - Javascript and most libraries built on-top of it heavily make use of callbacks. While this is perfectly fine for some, I prefer the Promise design pattern for its reliability, simplicity and synchronous feel when working with pseudo-filesystems.
- __Implementation Agnostic API__ - The API endpoints and API responses will both be wrapped so that developers should be able to swap out providers with little to no code changes.
- __Plugin Architecture__ - Cloud Storage Adapters can be added as needed with minimal configuration, as needed.

# Storage Agnostic

On some storage providers, like Google Drive and Microsoft OneDrive, folders are treated more like tags, where the same file can exist in multiple folders at the same time. On other platforms, like Dropbox, Amazon S3 and your local filesystem, the folder path is inherent in the identity of the file. 
Each storage provider has its own distinct features, storage paradigm, authentication system, permissions system, storage limits and API's. Integrating with a provider involves a fair amount of overhead, and garners an unsettling level of vendor lock-in to a specific platform; e.g. if your app uses performs a bunch of file operations against Dropbox, it can be confusing to duplicate the same logic using Google Drive, OneDrive or iCloud. 

Filefog abstracts all that complexity, focusing on creating a standardized way to create, find, delete, and update files. No matter what storage adapter you're using, the usage is exactly the same. That means you can switch your app's storage from Dropbox, to Google Drive, to Amazon S3, to iCloud, to local storage and back again - without changing any code. For the times when you need low-level, storage provider-specific functionality, Filefog provides an interface that allows you to talk directly to your provider's underlying library. 

# Prerequisites
Filefog has been tested with NodeJS 0.10.29+. 

# Quick Start
To install
    
    npm install filefog
    
    #each storage provider adapter will need to be installed separately
    npm install filefog-dropbox 
    
Now register an adapter to use, providing any storage provider application specific authentication. This registration only needs to be done once.
    
    var filefog = require('filefog')
    filefog.use('dropbox', require('filefog-dropbox'), {client_key : '...',client_secret : '...'});;

Next create an user authenicated provider previously saved access_tokens. 

    var dropboxProvider = filefog.provider("dropbox")
    //call dropboxProvider.oAuthGetAuthorizeUrl() and redirect the user if you do not have an access_token already.
    //dropboxProvider.oAuthGetAuthorizeUrl()
    
Finally, start using your client to manipulate files on behalf of the logged in user:

    dropboxClient = dropboxProvider.getClient({
        access_token: '...',
        refresh_token: '...'
    })
    .then(function (client) {
        return client.getFolderInformation();
    }).then(function (response) {
        //Dropbox folder metadata, in a standardized format. See Type Definitions for more info.
    })
    
# Contributing
- Find something you would like to work on.
    - Look for anything you can help with in the [issue tracker](https://github.com/filefog/filefog/issues).
- Fork the project and do your work in a topic branch.
- Add [tests](https://github.com/filefog/filefog-provider-tests) to prove your code works and run all the tests using npm test.
- Rebase your branch against master to make sure everything is up to date.
- Commit your changes and send a pull request.

# Resources

- [Fog](http://fog.io/)
- [Apache Libcloud](https://libcloud.apache.org/)

# Copyright
[MIT](https://github.com/filefog/filefog/blob/master/LICENSE)
