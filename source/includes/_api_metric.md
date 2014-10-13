# Metric

<!--===================================================================-->
## Overview

This service defines metrics that can be queried from the system.

<!--===================================================================-->
## Camera Bandwidth

> Request

```shell
curl -G https://login.eagleeyenetworks.com/g/metric/camerabandwidth -d "A=[VIDEOBANK_SESSIONID]&id=[CAMERA_ID]"
```

> Json Response

```json
{
    "motion": [],
    "core": [
        [
            "20141002190000.000",
            0.0,
            0.0,
            215910545.0,
            76733036.0,
            32049659.0,
            52716510.0
        ],
        [
            "20141002200000.000",
            0.0,
            0.0,
            252051927.0,
            164214711.0,
            36128066.0,
            223484133.0
        ],
        [
            "20141002210000.000",
            0.0,
            0.0,
            222334641.0,
            136879553.0,
            37816477.0,
            204892625.0
        ],
        [
            "20141002220000.000",
            0.0,
            0.0,
            121790883.0,
            103091573.0,
            33450507.0,
            103373171.0
        ],
        [...],
        [...],
        [...],
        [
            "20141009180000.000",
            0.0,
            0.0,
            286694378.0,
            175494759.0,
            37023695.0,
            153388458.0
        ],
        [
            "20141009190000.000",
            0.0,
            0.0,
            41425890.0,
            10373660.0,
            5029677.0,
            78599812.0
        ]
    ],
    "packets": [
        [
            "20141002190000.000",
            0.00183
        ],
        [
            "20141002200000.000",
            0.0018439999999999999
        ],
        [
            "20141002210000.000",
            0.0018500000000000001
        ],
        [
            "20141002220000.000",
            0.001848
        ],
        [...],
        [...],
        [...],
        [
            "20141009180000.000",
            0.0019380000000000001
        ],
        [
            "20141009190000.000",
            0.0
        ]
    ]
}
```

Used to query the bandwidth usage for a particular camera device.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/metric/camerabandwidth`

Parameter       | Data Type   	| Description  	| Is Required
---------       | ----------- 	| -----------  	| ----------- 
**id**   		| string      	| Bridge Id 	| true
start_timestamp | string      	| Start timestamp of query, in EEN format: YYYYMMDDHHMMSS.NNN. Defaults to 7 days ago.
end_timestamp  	| string   		| End timestamp of query, in EEN format: YYYYMMDDHHMMSS.NNN. Defaults to now.
group_by 		| string, enum  | Hour or Day, indicating how the results should be grouped. <br><br>enum: day, hour, minute
motion_interval | int      		| Motion Interval used for Motion Activity metric, in milliseconds. Defaults to 15000.

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
200 | Request succeeded
400 | Unexpected or non-identifiable arguments are supplied
401 | Unauthorized due to invalid session cookie
403 | Forbidden due to the user missing the necessary privileges
404 | Metrics not found

<!--===================================================================-->
## Bridge Bandwidth

> Request

```shell
curl -G https://login.eagleeyenetworks.com/g/metric/bridgebandwidth -d "A=[VIDEOBANK_SESSIONID]&id=[BRIDGE_ID]"
```

> Json Response

```json
{
    "core": [
        [
            "20141002170000.000",
            711610368.0,
            673860608.0,
            21533380.0,
            10299.0,
            10064282.0,
            9903.0
        ],
        [
            "20141002180000.000",
            711610368.0,
            673802922.66666698,
            139693604.0,
            16579176.0,
            30849223.0,
            70446106.0
        ],
        [
            "20141002190000.000",
            711610368.0,
            673748992.0,
            71676103.0,
            45096053.0,
            30430053.0,
            45046744.0
        ],
        [...],
        [...],
        [...],
        [
            "20141009160000.000",
            711610368.0,
            674055509.33333302,
            37737528.0,
            16296475.0,
            28883191.0,
            23120171.0
        ],
        [
            "20141009170000.000",
            711610368.0,
            674052608.0,
            20663486.0,
            8637204.0,
            18879693.0,
            19383808.0
        ]
    ],
    "bandwidth": [
        [
            "20141002180000.000",
            253117.37089200001
        ],
        [
            "20141002220000.000",
            240255.52353499999
        ],
        [
            "20141006030000.000",
            234217.22846400001
        ],
        [
            "20141006070000.000",
            233561.15779600001
        ],
        [...],
        [...],
        [...],
        [
            "20141009110000.000",
            217483.47826100001
        ],
        [
            "20141009150000.000",
            232692.09302299999
        ]
    ],
    "storage": [
        [
            "20141002170000.000",
            21523477
        ],
        [
            "20141002180000.000",
            69247498
        ],
        [
            "20141002190000.000",
            26629359
        ],
        [
            "20141002200000.000",
            105790941
        ],
        [...],
        [...],
        [...],
        [
            "20141009160000.000",
            14617357
        ],
        [
            "20141009170000.000",
            1279678
        ]
    ]
}
```

Used to query the bandwidth usage for a particular bridge device.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/metric/bridgebandwidth`

Parameter       | Data Type   	| Description  	| Is Required
---------       | ----------- 	| -----------  	| ----------- 
**id**   		| string      	| Bridge Id 	| true
start_timestamp | string      	| Start timestamp of query, in EEN format: YYYYMMDDHHMMSS.NNN. Defaults to 7 days ago.
end_timestamp  	| string   		| End timestamp of query, in EEN format: YYYYMMDDHHMMSS.NNN. Defaults to now.
group_by 		| string, enum  | Hour or Day, indicating how the results should be grouped. <br><br>enum: day, hour, minute

HTTP Status Code    | Data Type   
------------------- | ----------- 
200 | Request succeeded
400 | Unexpected or non-identifiable arguments are supplied
401 | Unauthorized due to invalid session cookie
403 | Forbidden due to the user missing the necessary privileges
404 | Metrics not found