# Layout

<!--===================================================================-->
## Overview

Layouts contain panes, which is a group of cameras arranged for viewing on screen. Layouts can be created for both mobile and desktop browsers. Layouts are associated with an account and account users are granted view/write/share permissions for the layout. Users who would otherwise have no access to a cameras, gain access to all cameras included in layouts shared with them.

The ordering of the panes is determined by the order of the array of LayoutJsonPane returned by the API. Each pane will have a size of 1, 2, or 3. A size of 1 is the smallest, and fills up 1x1 on the layout grid. A size of 3 is the largest and fills up 3x3 on the layout grid. If the grid does not have enough columns to fit the pane, then the size of the pane is decreased until it is able to fit on the grid.

<!--===================================================================-->
## Get Layout

> Request

```shell
curl -G https://login.eagleeyenetworks.com/g/layout -d "A=[VIDEOBANK_SESSIONID]&id=[LAYOUT_ID]"
```

> Json Response

```json
{
    "name": "Everything",
    "shares": [
        [
            "ca01ce6d",
            "SWRD"
        ],
        [
            "ca0c7d2c",
            "R"
        ],
        [
            "ca02a9bd",
            "R"
        ],
        [
            "ca09ae1d",
            "R"
        ],
        [
            "ca010672",
            "R"
        ],
        [
            "ca0c92ee",
            "R"
        ],
        [
            "ca05e8c2",
            "R"
        ]
    ],
    "current_recording_key": null,
    "configuration": {
        "panes": [
            {
                "cameras": [
                    "100ace90"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "10045dd6"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "100a9fdf"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "100fc5ce"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "10088295"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "100bc4a3"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "10001ca4"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "100b0c11"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            },
            {
                "cameras": [
                    "100891b7"
                ],
                "type": "preview",
                "pane_id": 0,
                "name": "",
                "size": 1
            }
        ],
        "settings": {
            "camera_row_limit": 3,
            "camera_name": true,
            "camera_aspect_ratio": 0.5625,
            "camera_border": false
        }
    },
    "id": "0b58ec7a-61e4-11e3-8f7d-523445989f37",
    "types": [
        "mobile"
    ],
    "permissions": "SWRD"
}
```

Returns layout object by Id

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/layout`

Parameter     | Data Type   | Description
---------     | ----------- | -----------
**id**        | string      | Layout Id

<!--===================================================================-->
## Create Layout

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -X PUT -v -H "content-type: application/json" https://login.eagleeyenetworks.com/g/layout -d '{"name": "[NAME]", "json":"{\"panes\":[ {} ] }", "types":[""]}'
```

> Json Response

```json
{
    "id": "80ca9ee0-4f28-11e4-81bf-523445989f37"
}
```

Creates a new layout

### HTTP Request

`PUT https://login.eagleeyenetworks.com/g/layout`

Parameter     | Data Type     | Description | Is Required
---------     | -----------   | ----------- | -----------
**name**      | string        | Layout Name | true
**types**     | array[string] | Specifies target(s) for layout. Multiple values are allowed. | true
configuration | json          | JSON object that defines the layout configuration
shares        | ???           | Array of arrays, one per account user for whom sharing is enabled for this layout. Each string contains two fields separated by a comma. The first field is a user id and the second field is the list of permissions for the user. Two special user IDs exist: 'account' specifies that the layout is shared with all users of the account. The second field contains permissions for users in the account. Example: ['cafedead', 'RWDS'] = user can view, change, delete or share this layout. ['cafe0001', 'RW'] = user can view and change this layout. ['account', 'R'] = All users in the account can view the layout. Permissions for the user issuing the /layout GET are not included in this array. |

<!--===================================================================-->
## Update Layout

> Request

```shell
```

> Json Response

```json
```

<!--===================================================================-->
## Delete Layout

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" -X DELETE -v -H "content-type: application/json" https://login.eagleeyenetworks.com/g/layout -d "id=[LAYOUT_ID]" -G
```

### HTTP Request

`DELETE https://login.eagleeyenetworks.com/g/layout`

Parameter     | Data Type   | Description
---------     | ----------- | -----------
**id**        | string      | Layout Id

<!--===================================================================-->
## Get List of Layouts

> Request

```shell
curl --cookie "videobank_sessionid=[VIDEOBANK_SESSIONID]" --request GET https://login.eagleeyenetworks.com/g/layout/list
```

> Json Response

```json
[
    [
        "0b58ec7a-61e4-11e3-8f7d-523445989f37",
        "Everything",
        [
            "mobile"
        ],
        "SWRD"
    ],
    [
        "75c03026-719a-11e3-afba-ca8312ea370c",
        "Glenn",
        [
            "mobile"
        ],
        "SWRD"
    ],
    [...],
    [...],
    [...]
]
```

Returns array of arrays, with each sub-array representing a layout available to the user. Please note that the ListLayout model definition below has property keys, but that's only for reference purposes since it's actually just a standard array.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/layout/list`

### Response: Layout Model

Array Index     | Attribute   | Data Type       | Description
---------       | ----------- | -----------     | -----------
0               | id          | string          | Unique identifier for the Layout
1               | name        | string          | Name of the layout
2               | types       | array[string]   | Array of types defined for the layout. This attribute has not yet been implemented.
3               | permissions | string          | String of zero or more characters. Each character defines a permission that the current user has for the layout. Permissions include: 'R' - user can view this layout. 'W' - user can modify this layout. 'D' - user can delete this layout. 'S' - user can share this layout.