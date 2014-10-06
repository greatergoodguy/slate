# User

<!--===================================================================-->
## Overview
If a user isn't requesting their own user record, these apis requires SuperUser or AccountSuperUser (if accounts match) permissions. If no id is passed in the request, then it will attempt to get the data for the user that is authenticated and making the call.

<!--===================================================================-->
## Get User

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -G https://login.eagleeyenetworks.com/g/user

or

curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -G https://login.eagleeyenetworks.com/g/user -d id=[USER_ID]
```

> Json Response

```json
{
    "is_sms_include_picture": 0,
    "last_name": "",
    "uid": " ",
    "is_export_video": 1,
    "layouts": [
        "217f0fd4-450f-11e4-a983-ca8312ea370c"
    ],
    "account_map_lines": null,
    "postal_code": null,
    "is_account_superuser": 1,
    "timezone": "US/Pacific",
    "active_brand_subdomain": "login",
    "sms_phone": null,
    "city": null,
    "first_name": null,
    "is_notify_enable": 1,
    "owner_account_id": "00004206",
    "json": "{}",
    "id": "ca0e1cf2",
    "is_superuser": 0,
    "state": null,
    "last_login": "20141006173752.672",
    "is_recorded_video": 1,
    "camera_access": [
        [
            "1005f2ed",
            "r"
        ],
        [
            "100bd708",
            "r"
        ]
    ],
    "notify_period": [
        "0-0000-2359",
        "1-0000-2359",
        "2-0000-2359",
        "3-0000-2359",
        "4-0000-2359",
        "5-0000-2359",
        "6-0000-2359"
    ],
    "email": "john.doe@fakeemail.com",
    "utc_offset": -25200,
    "mobile_phone": null,
    "street": [],
    "notify_rule": [
        "one-email-0"
    ],
    "is_active": 1,
    "is_user_admin": 1,
    "phone": null,
    "is_layout_admin": 1,
    "is_live_video": 1,
    "is_device_admin": 1,
    "is_branded": 1,
    "alternate_email": null,
    "active_account_id": "00004206",
    "access_period": [],
    "is_staff": 0,
    "country": "US",
    "is_master": 0,
    "is_pending": 0
}
```

Returns user object by ID. Not passing an ID will return the current authorized user.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/user`

Parameter | Data Type   | Is Required
--------- | ----------- | -----------
id        | string      | false

<!--===================================================================-->
## Create User

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -X PUT -v -H "content-type: application/json" https://login.eagleeyenetworks.com/g/user -d '{"first_name": "[FIRST_NAME]", "last_name": "[LAST_NAME]", "email": "[EMAIL]"}'
```

> Json Response

```json
{
    "id": "ca0ffa8c"
}
```

Creates a new User

### HTTP Request

`PUT https://login.eagleeyenetworks.com/g/user`

Parameter         | Data Type   | Description   | Is Required
---------         | ----------- | -----------   | -----------
**first_name**    | string      | First Name    | true
**last_name**     | string      | Last Name     | true
**email**         | string      | Email Address | true
phone             | string      | Phone Number |
mobile_phone      | string      | Mobile Phone Number |
uid               | string      | An identifier of the user. Only Super Users can set this. |
owner_account_id  | string      | ID of owner account. Defaults to account of the user creating it. Must be an account the user has access to. For SuperUsers, it can be any account, for Account SuperUsers, it can be theirs or a child account. |
street        | string  | Street Address |
city          | string  | City |
state         | string  | State |
country       | string  | Country |
postal_code   | string  | Postal Code |
json          | string  | JSON formatted data representing various user settings. |
is_staff              | int     | 1 or 0 indicating the user has Staff permission. Only Super Users can set this. | 
is_superuser          | int     | 1 or 0 indicating the user has Super User permission. Only Super Users can set this. |
is_account_superuser  | int     | 1 or 0 indicating the user as Account Super User permission. Only Super Users and Account Super Users can set this. |
is_layout_admin       | int     | 1 or 0 indicating whether the user is a layout admin or not. |
is_device_admin       | int     | 1 or 0 indicating whether the user is a device admin or not. |
camera_access         | array   | Array of arrays, one per device for which the uer has permissions. Each sub array contains two elements. The first field is a device id, and the second field is a string of 1 or more chacterse indicating permissions for the user, for example: [‘cafedead’,’RWS’] = user can view, change, delete this device. [‘cafe0001’,’RW’] = user can view this layout and change this device. Permissions include: 'R' - user has access to view images and video for this camera. 'A' - user is an admin for this camera. 'S' - user can share this camera in a group share. Only superusers or account_superusers can edit this field. |
sms_phone             | string  | Phone number to be used for SMS messaging. |
is_sms_include_picture| int     | 1 or 0. If 1, use MMS messaging to include a picture w with alert messages sent to the sms_phone number. |
alternate_email       | string  | Email address to be used for alert notifications. |
timezone              | string  | User timezone. Defaults to US/Pacific. |
access_period         | array   | Contains the time periods during which the user has access to the account. Each element of the array contains three field separated by dashes. The first field is the day of the week where Monday is 0. The second element is the start time. The third element is the end time. If empty, user has no time restrictions for access to the account. All times are expressed in local time and use a 24 hour clock formatted as HHMM. |
notify_period         | array   | Contains the time periods during which the user will receive alert notifications.. Each element of the array contains three field separated by dashes. The first field is the day of the week where Monday is 0. The second element is the start time. The third element is the end time. If empty, user will not receive any alert notifications. All times are expressed in local time and use a 24 hour clock formatted as HHMM. |
is_notify_enable      | int     | 1 or 0. If 1, user will receive alert notifications as specified in notify_period. |
notify_rule           | array   | Contains alert notification rules Each rule contains three fields separated by dashes And takes the form: Alert_Label-Notification_Method-Delay. Alert_Label: a name defined by the user. Notification_Method: Valid values: email, sms, gui. Delay: the amount of time, in minutes between between notifications. |

<!--===================================================================-->
## Update User

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -X POST -v -H "content-type: application/json" https://login.eagleeyenetworks.com/g/user -d '{"id": "[USER_ID]", "first_name": "[FIRST_NAME]"}'
```

> Json Response

```json
{
    "id": "ca0ffa8c"
}
```

Updates a user

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/user`

Parameter         | Data Type   | Description   | Is Required
---------         | ----------- | -----------   | -----------
**id**            | string      | User Id       | true
first_name        | string      | First Name    |
last_name         | string      | Last Name     |
email             | string      | Email Address |
phone             | string      | Phone Number |
mobile_phone      | string      | Mobile Phone Number |
uid               | string      | An identifier of the user. Only Super Users can set this. |
owner_account_id  | string      | ID of owner account. Defaults to account of the user creating it. Must be an account the user has access to. For SuperUsers, it can be any account, for Account SuperUsers, it can be theirs or a child account. |
street        | string  | Street Address |
city          | string  | City |
state         | string  | State |
country       | string  | Country |
postal_code   | string  | Postal Code |
json          | string  | JSON formatted data representing various user settings. |
is_staff              | int     | 1 or 0 indicating the user has Staff permission. Only Super Users can set this. | 
is_superuser          | int     | 1 or 0 indicating the user has Super User permission. Only Super Users can set this. |
is_account_superuser  | int     | 1 or 0 indicating the user as Account Super User permission. Only Super Users and Account Super Users can set this. |
is_layout_admin       | int     | 1 or 0 indicating whether the user is a layout admin or not. |
is_device_admin       | int     | 1 or 0 indicating whether the user is a device admin or not. |
camera_access         | array   | Array of arrays, one per device for which the uer has permissions. Each sub array contains two elements. The first field is a device id, and the second field is a string of 1 or more chacterse indicating permissions for the user, for example: [‘cafedead’,’RWS’] = user can view, change, delete this device. [‘cafe0001’,’RW’] = user can view this layout and change this device. Permissions include: 'R' - user has access to view images and video for this camera. 'A' - user is an admin for this camera. 'S' - user can share this camera in a group share. Only superusers or account_superusers can edit this field. |
sms_phone             | string  | Phone number to be used for SMS messaging. |
is_sms_include_picture| int     | 1 or 0. If 1, use MMS messaging to include a picture w with alert messages sent to the sms_phone number. |
alternate_email       | string  | Email address to be used for alert notifications. |
timezone              | string  | User timezone. Defaults to US/Pacific. |
access_period         | array   | Contains the time periods during which the user has access to the account. Each element of the array contains three field separated by dashes. The first field is the day of the week where Monday is 0. The second element is the start time. The third element is the end time. If empty, user has no time restrictions for access to the account. All times are expressed in local time and use a 24 hour clock formatted as HHMM. |
notify_period         | array   | Contains the time periods during which the user will receive alert notifications.. Each element of the array contains three field separated by dashes. The first field is the day of the week where Monday is 0. The second element is the start time. The third element is the end time. If empty, user will not receive any alert notifications. All times are expressed in local time and use a 24 hour clock formatted as HHMM. |
is_notify_enable      | int     | 1 or 0. If 1, user will receive alert notifications as specified in notify_period. |
notify_rule           | array   | Contains alert notification rules Each rule contains three fields separated by dashes And takes the form: Alert_Label-Notification_Method-Delay. Alert_Label: a name defined by the user. Notification_Method: Valid values: email, sms, gui. Delay: the amount of time, in minutes between between notifications. |

<!--===================================================================-->
## Delete User

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -X DELETE -v -H "content-type: application/json" https://login.eagleeyenetworks.com/g/user -d "id=[USER_ID]" -G
```

Deletes a user

### HTTP Request

`DELETE https://login.eagleeyenetworks.com/g/user`

Parameter     | Data Type   | Is Required
---------     | ----------- | -----------
**id**        | string      | true

<!--===================================================================-->
## Get List of Users