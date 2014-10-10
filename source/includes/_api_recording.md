# Recording

<!--===================================================================-->
## Overview

This service is used to retrieve and update recording info for recordings that were started/stopped using the "action/recordnow" and "action/recordoff" services.

<!--===================================================================-->
## Get Recording Object

> Request

```shell
```

> Json Response

```json
```

Returns recording object by recording_key.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/recording`

Parameter       	| Data Type   	| Description
---------       	| ----------- 	| -----------
**recording_key**   | string      	| Recording Key

<!--===================================================================-->
## Update Recording Object

> Request

```shell
```

> Json Response

```json
```

Update a Recording

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/recording`

Parameter       	| Data Type   	| Description
---------       	| ----------- 	| -----------
**recording_key**   | string      	| Unique identifier (within an account) of a recording
meta 				| object 		| Meta data. This is meant to be a generic object that can store any data that is needed, so the properties are not predefined.