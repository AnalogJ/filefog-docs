# Filefog Base Methods
## use

Registers a storage provider adapter with optional configuration

	//use(name, provider_definition, config)
	filefog.use('dropbox', require('filefog-dropbox'), {client_key : 'sl47p7pijvtp73h',client_secret : 'j6vluc5yq7dxnj6'});;

__Arguments__

- name (`String`) - __required__ -  Identifies the provider you want to register eg. "box", "dropbox", "google", "skydrive", etc
- provider_definition (`Object`) - __required__ - Filefog storage provider adapter

__Returns__
- (`self`) - Returns `self` for chaining. 


## provider

Method that will return an instance of a wrapped Provider when given `provider` name,
	
	//provider(name, default_filefog_options)
	var dropboxProvider = FileFog.provider('dropbox');

__Arguments__

- name (`String`) - __required__ -  This is an string specifing the adapter you want to initialize as a provider
- default_filefog_options (`Object`) - Optional filefog options

__Returns__
- (`Provider`) - Returns an instance of Provider

## client
Direct method to instantiate a Client, if you already have the credentials needed to use it

	//client(name, credentials, default_filefog_options)
	var dropboxClient = FileFog.client('dropbox', {
     access_token: 'ya29.1.AADtN_W5vWUjYklaE_ZNkJBd2y9Ot60jF4SewLivnLgLHDYhmiadzbPPZntHmL4nceXs0w',
     refresh_token: '1/IMcWJR9KJPVguuKLUrHzMjRl-XECLf6mK2YoUYnbQaU'
    });

__Arguments__

- name (`String`) - __required__ - name of the provider
- credentials (`Object`) - Credentials used to authenticate user against storage provider
- default_filefog_options (`Object`) - Optional filefog options

__Returns__

- (`Client`) - Returns an instance of Client

#Provider Methods
##getConfig()
Gets the configuration for this provider (Oauth client keys, etc)

__Returns__

- (`Object`) - Configuration Object for this Provider




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


# Client Methods