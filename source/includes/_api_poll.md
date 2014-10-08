# Poll

<!--===================================================================-->
## Overview

The poll service provides a mechanism for an application to receive notifications of events or spans from the Eagle Eye service. These entities are grouped by resource. There are six main resources:

  * thumb - Thumbnail resource. Provides a timestamp for a thumbnail image. One can use the timestamp to grab the actual thumbnail image.
  * pre - Preview resource. Provides a timestamp for a preview image. One can use the timestamp to grab the actual preview image.
  * video - Video resource. Provides a start and end timestamp of a video event.
  * event - Event resource. Provides full event information.
  * status - A bitmask flag defining the state of a bridge or a camera.
  * registry - A boolean field determining if a camera or bridge became registered.

Events can come from lots of sources:

  * Devices or Cameras (camera alerts, start and stop recording, etc.)
  * System Events (maintenance, server changes)
  * Account Events (other user changes, account alerts, layout changes).

Device and Camera events include, on, off, online, offline, currently recording, currently sensing motion, start/stop schedule event, being controlled with PTZ)

This service will continually be extended. Any UI should not care about stuff that it does not understand.

Poll is a stateful request for updates any time a matching event occurs within the service. The initial poll request is a POST with a JSON formatted body indicating the resources to track. Resources that are video, pre, and thumbnail automatically register the api caller to their respective events. However, resource type ‘event’ requires the api caller to tell the API what events to listen for.

Each object consists an id element and a list of resource types to be monitored. The POST transaction receives an immediately responds with a JSON formatted body indicating the current timestamp for all requested resources. The response also includes a cookie, which can be used to track changes to the indicated resources via GET transaction.

Each resource type has a specific object format in response:

Type        | Response          | Notes
---------   | -----------       | -----------     
video       | [startts, endts]  | List of start, end Timestamps for video segment. Updates at start and per key frame received until end.
thumb       | thumbts           | Timestamp of latest thumbnail image
pre         | prets             | Timestamp of latest preview image
registry    | boolean           | True is returned if a camera/bridge becomes registered. False if the camera/bridge becomes unregistered.
status      | bitmask           | A numerical bitmask defining the status. Bit position defines status. The meaning of each bit is defined in the table below.
event       | object            | Events are a key value pair, where the key is the four CC of the event, and event structure are the actual meta data for that specific event. Available events are shown in the table below.

### Status Bitmask

Bit (LSB) | Status
--------- | -----------      
0         | Camera registered to EEVBS (DEPRECATED)
1         | Camera Online (DEPRECATED)
2         | Camera On (User Setting) (DEPRECATED)
3         | Camera Recording (DEPRECATED)
4         | Camera Sending Previews
5         | Camera Located
6         | Camera Not Supported
7         | Camera Configuration in Process
8         | Camera Needs Password
9         | Camera Available But Not Attached
10        | Reserved Bit
11        | Camera Error
12        | Reserved Bit
13        | Reserved Bit
14        | Reserved Bit
15        | Reserved Bit
16        | Invalid
17        | Camera On
18        | Streaming
19        | Recording
20        | Registered

This status bit mask is used to determine what the high-level/overall camera status is, using the following logic:

IF "Camera On" (bit 17)==0 THEN "Off" (orange forbidden icon)
<br>ELSE IF "Registered" (bit 20)==0 THEN "Internet Offline" (red exclamation icon)
<br>ELSE IF "Streaming" (bit 18)==1 THEN "Online" (green check icon)
<br>ELSE IF "Password" (bit 8)==1 THEN "Password Needed" (effectively "Offline") (red padlock icon)
<br>ELSE "Offline" (red X icon)

Recording status uses the following logic:

IF "Recording" (bit 19) THEN Recording (green circle icon)

IF "Invalid" (bit 16)==1 THEN no status change (use whatever status bits were set previously)

### Event Objects

Four CC   | Description
--------- | -----------      
VRES      | Video start event
VREE      | Video end event
VRKF      | Video key frame event
VRSU      | Video update event
PRSS      | Preview stream start event
PRTH      | Thumbnail event
PRFR      | Preview event
PRSE      | Preview stream end event
PRSU      | Preview stream update event
EMES      | Motion start event
EMEE      | Motion end event
ESES      | Event stream start
EVVS      | Video swap event
ESEE      | Event stream stop
ECON      | The camera is online
ECOF      | The camera is offline
AELI      | Account event log in
AELO      | Account event log out
AEDO      | Download video event
AEUC      | Create user event
AEUD      | Delete user event
AECC      | User config change event
AELD      | Live display event
AEPT      | Pan tilt zoom event
AAEC      | Layout create event
AEED      | Layout delete event
AEEL      | Layout change event
AEAC      | Account create event
AEAD      | Account delete event
AEAH      | Account change event
AEDC      | Device create event
AEDD      | Device delete event
AEDH      | Device change event
CECF      | Camera found event
CSAT      | Camera stream attach event
CSDT      | Camera stream detach event
COFF      | Camera off event
CONN      | Camera on event
COBC      | Camera bounce event
CSTS      | Camera settings event
CSTC      | Camera settings change event
CPRG      | Camera purge event
CDLT      | Camera data lost event
CBWS      | Camera bandwidth sample event
BBWS      | Bridge bandwidth sample event
AEDA      | Device alert event
AEDN      | Device alert notification event
MRBX      | Motion Box event
MRSZ      | Motion size reports
ROMS      | Region of interest motion start
ROME      | Region of interest motion end
ALMS      | Alert Motion Start
ALME      | Alert Motion End
ALRS      | Alert Region Of Interest Start
ALRE      | Alert Region Of Interest End

<!--===================================================================-->
## Initialize Poll

> Request

```shell
```

> Json Response

```json
```

Subscribe to poll service, which is required for GET /poll. 
Response headers: set_cookie: ee-poll-ses=xxxxxx

Parameter       | Data Type   | Description
---------       | ----------- | -----------
PostPollCameras | json        | Cameras

<!--===================================================================-->
## Polling

> Request

```shell
```

> Json Response

```json
```

Used to receive updates on real-time changes. Either requires a valid 'ee-poll-ses' cookie from POST /poll or the 'token' response from POST /poll.

Parameter       | Data Type   | Description
---------       | ----------- | -----------
token           | string      | Token from POST /poll. Not required if you have the 'ee-poll-ses' cookie from POST /poll.
