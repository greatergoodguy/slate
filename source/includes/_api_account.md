# Account

<!--===================================================================-->
## Overview

The account service allows managing accounts by superusers and account_superusers.

<!--===================================================================-->
## Get Account

> Request

```shell
```

> Json Response

```json
```

Returns account object by Id

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/account`

Parameter 	| Data Type   | Description | Is Required
--------- 	| ----------- | ----------- | -----------
**id**  	| string      | Account Id 	| true

<!--===================================================================-->
## Create Account

> Request

```shell
```

> Json Response

```json
```

Creates a new Account

### HTTP Request

`PUT https://login.eagleeyenetworks.com/g/account`

Parameter 				| Data Type   	| Description 	| Is Required
--------- 				| ----------- 	| ----------- 	| -----------
**name**  				| string  		| Name of Account	| true
owner_account_id  		| string      	| ID of parent account. If omitted, parent is set to the account of the creating user. | 
**contact_first_name**	| string      	| First name of primary contact for account | true
**contact_last_name**	| string      	| Last name of primary contact for account | true
**contact_email**		| string      	| Email of primary contact for account | true
contact_street			| array[string] | Array of strings representing 1 or more parts of a street address of primary contact for account |
contact_city			| string      	| City of primary contact for account.
contact_state			| string      	| State/province of primary contact for account.
contact_postal_code		| string      	| Zip/postal code of primary contact for account.
contact_country			| string      	| Country of primary contact for account.
timezone				| string, enum  | Timezone of Account. Defaults to US/Pacific. <br><br>enum: "US/Alaska", "US/Arizona", "US/Central", "US/Pacific", "US/Eastern", "US/Mountain", "US/Hawaii", "UTC"
status					| array[string] | Account status. This can only be edited by superusers and account_superusers of the parent/owner account. 'realm_root' can only be set by superusers.
access_restriction		| array[string] | Each entry in the list contains a restriction. Possible values: 'enable_mobile' = If present this account can has access to mobile clients. 'enable_ip_restrictions' = if present, and if allowable_ip_address_ranges has been specified, limits logins to the address ranges specified.
allowable_ip_address_ranges | ???      	| Each entry in the list specifies one address range. Entries use the ‘/’ format. For example, to limit access to 192.168.123.0-192.168.123.255, the entry would be 192.168.123.0/24. If no entries are present, 0.0.0.0/0 i s implied.
session_duration		| int      		| Session duration in minutes. Session duration of 0 means 'stay logged in forever'
holiday					| array[string] | List of dates that during which holidays are observed. Format for dates is YYYYMMDD
work_days				| array[string] | String of length 7. Each position in the string corresponds to a day of the week. Monday is position 0, Tuesday is position 1, etc... Each character in the string can have a value of 1 or 0. 1 means that this day is a work day.
work_hours				| array[string] | Two entries. Each entry contains a time expressed in local time. The first entry in the list is the work day start time . The second entry in the ist is the stop time. Times are represented using a 24 hour clock and are formatted as HHMM. For example, 8AM would be 0800 and 5PM would be 1700.
alert_mode 				| array[string] | List of possible alert modes as defined for this account.
active_alert_mode 		| string      	| Must be blank or one of the values defined in alert_mode list.
default_colo			| string      	| Name of the colo in which this account data for this account will be stored by default.
default_camera_passwords| string      	| Comma-delimited string of default camera passwords
is_without_initial_user	| int      		| Indicates whether you want to NOT create an initial user with the new account. Defaults to 0, meaning an initial user (with is_account_superuser=1) will be created using the info from “contact_first_name/contact_last_name/contact_email” arguments of this new account. If this is set to 1, NO initial user will be created.
is_initial_user_not_admin| int      	| Indicates whether you want do NOT want the initial user to be an admin.

<!--===================================================================-->
## Update Account

> Request

```shell
```

> Json Response

```json
```

<!--===================================================================-->
## Delete Account

> Request

```shell
```

> Json Response

```json
```

<!--===================================================================-->
## Get List of Accounts

> Request

```shell
```

> Json Response

```json
```