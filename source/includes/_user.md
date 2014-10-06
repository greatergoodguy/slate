# User

<!--===================================================================-->
## Overview
If a user isn't requesting their own user record, these apis requires SuperUser or AccountSuperUser (if accounts match) permissions. If no id is passed in the request, then it will attempt to get the data for the user that is authenticated and making the call.

<!--===================================================================-->
## Get User

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
{
  "id": "1",
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@fakeemail.com",
  "owner_account_id": "1",
  "active_account_id": "1",
  "uid": "1",
  "is_superuser": 1,
  "is_account_superuser": 1,
  "is_staff": 1,
  "is_active": 1,
  "is_pending": 1,
  "is_master": 1,
  "is_user_admin": 1,
  "is_layout_admin": 1,
  "is_live_video": 1,
  "is_device_admin": 1,
  "is_export_video": 1,
  "is_recorded_video": 1,
  "street": 
    [
      "123 Fake Street", 
      "Apt #213"
    ],
  "city": "Awwstin",
  "state": "Techsas",
  "country": "United States of the Earth",
  "postal_code": "00000",
  "phone": "1-800-DO-NOT-CALL-THIS-NUMBER",
  "mobile_phone": "1-800-SERIOUSLY-DO-NOT-CALL-THIS-NUMBER",
  "utc_offset": 0,
  "timezone": "UTC",
  "last_login": "20140101090000.000",
  "alternate_email": "john.doe@superfakeemail.com",
  "sms_phone": "1-800-DO-NOT-CALL-THIS-NUMBER",
  "is_sms_include_picture": 1,
  "json": "",
  "camera_access": 
    [
      "camera_id 1", 
      "camera_id 2", 
      "camera_id 3", 
    ],
  "layouts": 
    [
      "layout_id 1", 
      "layout_id 2", 
      "layout_id 3", 
    ],
  "is_notify_enable": 1
}
```

Returns user object by ID. Not passing an ID will return the current authorized user.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/user`

### Query Parameters

Parameter     | Data Type   | Description
---------     | ----------- | -----------
id            | string      | User ID

<!--===================================================================-->
## Create User

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

Creates a new User

### HTTP Request

`PUT https://login.eagleeyenetworks.com/g/user`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the cat to retrieve

<!--===================================================================-->
## Update User

<!--===================================================================-->
## Delete User

<!--===================================================================-->
## Get List of Users