# Camera

<!--===================================================================-->
## Overview

The Device service allows access to create new logical devices (Cameras or Bridges) and establish a relationship between logical and physical devices. Get method is available to any user with camera ‘R’ (read) permission. Methods Post, Delete are available to account super users and users with Camera ‘W’ (write) permissions for the indicated camera. Put method is only available to account super users.

When adding a new camera, the name and settings parameters are required. The settings parameter should contain the id of the bridge and the guid of the camera.

### Camera Settings Overview

The camera setting system is based on an inheritance model. User settings are “overlayed” on top of default settings for a device. By removing the user settings, the camera setting will automatically go back to the default value provided and maintained by Eagle Eye for the camera.

Under the covers this works as follows:

* There is a default setting, range, and value for everything built into the code, guaranteeing there are always settings for any feature supported by the software. These values are defined when the feature is implemented in software.
* There is an optional "global" setting file that can/will be distributed during patches/updates that can override and extend default settings, including changing valid ranges etc. As with 1, this is a constant across all bridges that get the upgrade. It is tweaked for system policy changes like different upload bandwidth, default schedules etc. which do not imply code changes.
* There is an optional per make/model/version file, part of the make/model/version driver deployment, that can add and adjust settings for anything above. This is used to optimize things like sensitivity or codec parameters while building the driver for the specific device, and is released a camera driver is updated. This is also where camera specific features (audio availability) is announced.
* The first three get merged to produce the base settings for the the camera, which defines the value, min, and max for every supported feature.
* User settings (things the user has consciously "taken control of") are dynamically overlay-ed on the above settings inheritance to produce the active settings on the camera.
* Any scheduled or triggered changes are then overlayed on top create the running settings (so upload bandwidth can be changed in a schedule to something different from the users settings, which is also different from the system default, and when the schedule is “removed” the bandwidth will go back to the user value.

The implication of this is that if a user has not "pinned" a setting by changing/managing it, it will track any system or make/model/version release updates. That is, EEN can optimize operating parameters and they will automatically propagate unless the user has changed/pinned them. Further, the user should be "aware" of this, which works well with an open/closed control metaphor or a check box - an y setting control that is open/checked is "manually" set by the user, whereas closed controls will track EEN system wide recommendations. Finally, it is expected that the “user” settings will be managed to be only the value specifically modified by users, not all settings.

The set of all settings is potentially large, and far more than most users will ever want or need to manage. A separate table will be maintained which lists the “normal” settings - settings most users may want to interact with. This is standard across all devices, and contains a list of settings names that should be accessible to the user. This list should be “joined” with the list of all settings to result in a subset of controls to be displayed in basic mode. An advanced mode should make all settings available with primitive controls to set or delete a value.

An implication of this model the “user settings” object is a generic object that is only lightly interpreted by the device. Settings that match a known names (ie are within the camera base or mmv settings) will be utilized, but all values will be stored and returned as part of the “user settings” field. This can be used to support user interface elements on a per camera basis with values the bridge/camera do not interpret.

### Read Camera Settings (GET device "camera_settings" property)

When getting the camera settings, a JSON string representing a JSON object is returned containing:

  * “active_settings”: A set of named entities encapsulating all settings understood for this device. Each entity contains an object of
    * “v”: the current value of the setting, as influenced by filters on top of base settings
    * “max”: the maximum allowed value
    * “min”: the minimum allowed value.
    * “d”: the default value of the setting, also defining the expected type for the field
    * Note: max and min are applicable for numeric fields. for set fields (e.g. preview_resolution) the min field will contain an array of the valid values
    * Note: additional descriptive members may be added to this object over time, implementation must ignore fields they do not understand.
  * “active_filters”: an array of filters currently being applied. List is in priority order, that is earlier entries will override later entries in the list. Each entry is a string with format of one of
    * “schedule_<name>”: name replaced by schedule name
    * “user_user”: constant, indicating where user settings are applied
    * “trigger_<name>”: name replaced by the triggered name (ie “night”).
  * “user_settings”: an object with the following fields:
    * “settings”
      * A subset of the base settings, indicating items the user has specifically set.
      * The user settings contain only the “v” of the setting and are bare objects (e.g. “contrast”: 0.1)
      * Most setting are “atomic” entities updated at a single time. For value settings (brightness) this is obvious, but for complex settings (e.g. alerts) it is important the entire setting object is replaced with a new value.
      * A few settings (currently "alerts","rois","active_alerts","active_rois") are accumulation settings. A setting add transaction adds the new member to the set, and a settings delete removes a member.
    * “schedules”: A set of named members, each with the following members
      * “start”: time object, indicating when the schedule is set to on. This is a transition point in time, not a description of the active time period. To have a schedule that runs during working hours - { “start”: { “hours”: 8, “wdays”: [1,2,3,4,5]}, “end”: {“hours”: 17, “wdays”: [1,2,3,4,5] }}.
      * “end”: time object, indicating when the schedule is removed
      * “priority”: a floating point value defining the priority of the schedule. Lowest number wins. All user settings are applied with priority of 10.0, so schedule values with priority < 10 will override user settings, while value > 10 will not. It priority or two schedules is equal and their settings conflict,
      * “settings”: a object with members mirroring the settings above, indicating the new value for settings to use while the schedule is active. For accumulation settings, values will be added into the set when activated and removed when deactivated.
    * time object is a object with the following named members, loosely patterned after crontab arguments. Each time the fields match the current time, the event is toggled.
      * fields
          * seconds(0-59)(defaults to 0)
          * minutes (0-59) (defaults to 0)
          * hours (0-23) (defaults to 0)
          * mdays(1-31) (defaults to *)
          * wdays(1-7) (1=Monday, 7=Sunday. defaults to nothing.)
          * months(1-12) (defaults to *)
      * each field can be
          * single integer
          * string “*” indicating all
          * list of integers
      * if both “days” fields are set, the action will be run on the union

### Update Camera Settings (POST device "camera_settings_add" argument)

To update/set settings (i.e. override default setting value with a "user" setting), a JSON string is sent representing a JSON object containing:

* “settings”: an optional object with members to be overlayed over base settings value. Values are bare (that is simply replacements for the “v” field of base)
* “schedules”: an optional object with 1 or more members, each a schedule object per the get description. Note schedules with the same name will be replaced in the their entirety with the new value.

### Delete Camera Settings (POST device "camera_settings_delete" argument)

To delete/unset settings (i.e. return to default setting value), a JSON string is sent representing a JSON object containing:

* “settings”: an optional object with members to be removed from user settings. Values ignored.
* “schedules”: an optional object with 1 or more members, each a the name of a current schedule. Value of the members are ignored.

### Camera Settings Currently Supported

Each camera make/model/version is different, thus not every setting is supported for some cameras, but here is list of core camera settings that are relevant to most applications:

* "active_rois": object indicating which rois are currently active (by "name"; see "rois" setting below)
* "audio_enable": boolean (true/false) indicating whether audio is enabled or not
* "camera_on": boolean integer (1/0) indicating whether camera is turned on/off respectively
* "motion_sensitivity": float between 0 and 1 indicating how sensitive the motion detection is
* "motion_size_ratio": float between 0 and 1 indicating the size of objects to detect for motion
* "motion_boxes_metric_active": boolean integer indicating whether motion boxes are enabled
* "preview_realtime_bandwidth": float indicating the max bandwidth of real-time preview image transmission
* "preview_transmit_mode": string indicating when preview images are transmitted to the cloud
* "preview_interval_ms": integer indicating how many milliseconds between preview images
* "preview_resolution": string indicating the resolution of the preview images. When displaying the options for this setting, you must use the data from "video_config.v.preview_quality_settings.<preview_resolution>" ("w" and "h") to show what this resolution string translates to for display purposes.
* "preview_quality": string indicating the quality of the preview images
* "retention_days": integer indicating how many days worth of data should be retained in the cloud
* "rois": extensible object, containing multiple ROI objects keyed by a "name", with each ROI object supporting the following members:
    * “verts”: [[x,y],...], polygon vertices in order. Coordinates will be scaled so 0-1.0 is full screen for x and y, with 0,0 being top left corner. Edges can’t cross or bad things will happen.
    * "motion_noise_filter": as for main screen. If < 0.001 will not be applied
    * "motion_sensitivity": as for main screen. If < 0.001 will not be applied
    * "motion_hold_interval": as for main screen. If < 0.001 will not be applied
    * “priority”: float (bigger wins), control settings overlay. Defaults to 0.0
    * “motion_threshold”: (float)percentage of the screen to be occluded by motion within this ROI to create an ROI event. Defaults to motion_size_ratio from main screen.
    * "name": string used for the display name of the ROI in a GUI. Not to be confused with the "name" of the ROI as the key of this ROI object.
    * "ignore_motion": boolean integer (1/0) indicating whether motion will be ignored for this ROI. Used as a GUI abstraction to indicate we want to set motion_sensitivity to ".001" and motion_noise_filter to ".99"
    * “roiid”: (int)id to attach to the ROI event. If 0, or not present, events will not be created, which will also prevent roi based alerts.
    * “hold_off_ms”: (int) ms of constant motion before an event is created, defaults to motion_event_holdoff_ms
    * “hold_on_ms”: (int) ms of idle before stopping an ROI motion event. Defaults to motion_event_holdon_ms from main settings.
* "scene_type": string indicating the type of scene the camera is viewing
* "video_transmit_mode": string indicating when video is transmitted to the cloud
* "video_capture_mode": string indicating when video will being recorded
* "video_bandwidth_factor": integer indicating the bit rate of the video. When displaying options for this setting, you must use the data from "video_config.v.video_quality_settings.<video_resolution>.quality.<video_quality>.kbps" to show what this setting translates to for display purposes.
* "video_resolution": string indicating the resolution of the video. When displaying the options for this setting, you must use the data from "video_config.v.video_quality_settings.<video_resolution>" ("w" and "h") to show what this resolution string translates to.
* "video_quality": string indicating the quality of the video
* "video_config": READ-ONLY object defining all the preview/video configuration parameters for each available resolution. Helps give useful information for display purposes of the "preview_resolution", "video_resolution", and "video_bandwidth_factor" settings/options.

### Regions of Interest (ROIs)

ROIs will be defined by simple polygons - sequences of x,y coordinates that form a closed object, edge crosses are illegal and will have bizarre results. Each ROI will describe a portion of the screen. ROIs can overlap, and priority (higher wins) determines what sensitivity settings to use. For overlapping ROIs, all will get motion block detection and can trigger ROI motion spans.

ROIs can

* adjust DCT sensitivity and detection properties (ignore stuff in an area, track stuff aggressively in an area)
* cause specific events
* characterize an object for later alert/event processing (dwell, transitions counting)
* turn on certain detectors within a region.

ROIs within settings will be “rois”: { “roiname”: roi,... }. ROIs are enabled and disabled by “active_rois”: { “roiname”: true,...} to allow ROIs to easily turned on and off to support schedules and ROI based alerts. To remove an active ROI delete it with the same arguments.

Like the alert logic, “rois” and “active_rois” are accumulation settings - adding an object adds it to the holding object instead of replacing the entire object like most settings. Similarly, deleting an object removes it from the parent object, but leave the parent in place. Both also automatically trigger updates to the active esn data streams.

ROIs can produce events and force video recording on activity within them. These events are distinct from motion events (whole screen events). Each ROI event has a simple snapshot algorithm the grabs a snapshot immediately, as opposed to the optimized object tracking for motion events. Since ROIs are presumed to be smaller, this should result in good summary images.

ROI events are reported by the ROMS and ROME etags

ROMS

* cameraid (guint32)
* eventid(guint32) - unique to this event
* roiid(guint32) - roi id from the ROI definition
* videoid(guint32) - id of the associated video

ROME

* cameraid (guint32)
* eventid(guint32) - unique to this event

<!--===================================================================-->
## Get Camera

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
## Create Camera

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
## Update Camera

<!--===================================================================-->
## Delete Camera

<!--===================================================================-->
## Get List of Cameras