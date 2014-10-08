# Images and Video

<!--===================================================================-->
## Overview

Asset services provide access to media assets - previews and video in appropriate formats. Asset services are used in conjunction with list transactions to enumerate and identify assets.

Assets are identified by a tuple of timestamp, cameraid, quality, and format.

  * Timestamp: Eagle Eye timestamps have the format YYYYMMDDhhmmss.xxx and are always specifined in GMT time. In most contexts, special tokens can also be used to specify relative times - “now” is the current time, a value starting with + or - is an offset from the current time.
  * CameraID: Cameras are identified by a 8 character hexadecimal string, representing a unique 32 bit id associated with a specific camera. Note CameraID are not necessarily linked to specific hardware devices to allow device upgrade and replacement without disruption of history storage.
  * Quality: (low,med,high) Images and video may have multiple quality levels, each representing the same base asset. Video can be transcoded between quality levels on demand (at some point) to support reduced bandwidth for mobile devices. Normally cameras will capture at medium or high quality. For video, low quality is targeted at around 100kbps, medium quality is under 500kbps, and high quality is around 1mbps. Additional quality levels will be supported in time.
  * Format: Images are always returned as JPEG images. Video can currently returned as either FLV format (playback in browsers via Flash), MP4 (download and export format), and m3u/mpegts (HttpStreaming for iOS and newer android devices).

### Request Image

The image request model provides an efficient mechanism for accessing image sequences for several usage models. Image requests can be done directly using the next/after/prev virtual model. This returns images before or after specified timestamps. Alternatively, the timestamp and event information can be fetched through the “list” interface (to get events for history) and “poll” interface to track new images as they become available in real-time. The following cookbooks provides typical usage models for various events.

  * low bandwidth video playback: The preview stream is a sequential set of jpeg images. If played back in order, low resolution video is accomplished.
    * The simplest implementatation is to fetch “next” with a timestamp of “now” (e. g. /asset/next/image.jpeg?t=now;c=12345678;a=pre) - waiting for the subsequent image after the current time. Each time an image is returned, a new request should be made. If the downstream bandwidth is very low, the image fetch will automatically slow down (because delivery of image A happens after image B has been received, so the next call will fetch image C, skipping display of image B entirely). This approach works well for tracking a single image stream. As a tip, the first request should be done as a “prev” request to make sure an image is displayed, before the sequential next requests. The downside of this model is it requires a dedicated socket for each image stream being played. Many browsers have a limited pool of open sockets.
    * A more efficient mechanism for tracking multiple image streams is to use the “poll” interface. The Poll interface will provide the timestamp of the next image available for a set of camera, which can then be fetched via the /asset/asset call. Since the poll request supports multiple cameras in a single request, it requires only a single socket for any number of cameras. The client application should implement a “fair” algorithm across the returned timestamps to address low bandwidth situations (that is, make sure every image stream gets updated before you fetch a new image for the same stream). This algorithm will provide smooth frame rate degradation across any number of cameras, whether the performance bottleneck is client CPU or bandwidth. The best model for this is
      * receive update notifications for all cameras being tracked via a single sequential poll session
      * for each camera, keep track of the “lastest” image notification, replacing the last one even if it has not been fetched yet.
      * with a limited pool of “requests”, do a fair rotation between all cameras, fetching only the most recent image for each, and skipping the fetch if the image is already loading.
  * Random access image discovery. The preview and thumb image streams can provide a visual navigation tool for accessing recorded video. The typical implementation requires a map from a timestamp to the “best” image for that timestamp. To implement this approach, the client should use the “after” and “prev” requests with the timestamp of the user playhead. Both calls provide header data for x-ee-timestamp, x-ee-next, and x-ee-prev which identify the current and subsequent images in both directions when it can be easily determined. The usage paradigm for this should be
    * On navigation event (large jump), determine the timestamp of the user playhead and do an “/asset/prev” call to get the appropriate image (hmm, this should really be a best, figure out how ugly that would be). Store the x-ee-timestamp, x-ee-next and x-ee-prev values for the image.
    * As the user moves the playhead, if the time change is within the next/prev halfway bounds, no new request is required. when the use moves outside of the time range, do an image fetch with the new timestamp.
  * Thumbnail navigation. The system provides a “thumbnail” image for each event which is intended to provide a small representation of the event. The easiest mechanism to get a thumbnail for an event is to do an /asset/after/image.jpeg?a=thumb... image request with the starting timestamp of the event.

### Retrieve List of Images

There are a couple different ways to get a list of images. There first is to get all preview images between April 1st and April 2nd: https://apidocs.eagleeyenetworks.com/asset/list/image.jpeg?c=100676b2;t=20140401000000.000;e=20140402000000.000;a=all;

The second way is to get 500 images before April 1st: https://apidocs.eagleeyenetworks.com/asset/list/image.jpeg?c=100676b2;t=20140401000000.000;count=-500;a=all;

The third way is to get the next 500 images after April 1st: https://apidocs.eagleeyenetworks.com/asset/list/image.jpeg?c=100676b2;t=20140401000000.000;count=500;a=all;

### Retrieve Video

Video is accessed via the “play” command. Video is captured in segments, and is limited to 5 minutes per segment in storage. The video command will seamlessly rewrite headers within the video format to join segments as necessary to deliver the requested data span.

If the end time of the segment is in the future, the video will follow the data stream as it arrives, delivering live video streaming with minimal latency. MP4 format cannot be live streamed. Note: if the camera is not streaming video, the video will stop (and start again) as video is captured, which is typically not what is desired.

The key word “stream_<streamid>” can be used for the starting timestamp. This forces the camera to capture video and stream it to the cloud live. The stream id should be globally unique(ish) string - combination of a timestamp and userid works well. It is only critical for M3U requests, where it assure continuity between the m3u poll transactions.

The start timestamp must match the starting timestamp of a video if the video already exists. Subsegments of a video span can be specified by using the “to” (time offset) argument. For example, assume a 5 minute video has been recorded from 12:30 to 12:35. The query "?t=20121120123000.000;e=20121120123400.000;to=180000;..." will play one minute of video (timestamped at 12:33), 3 minutes into the video starting at 12:30, clipping off the last minute of the recorded segment.

Time offsets and end timestamps can be at any time, but the system will seek to the nearest key frame to actually start the video.

### Video Formats

The video system is based on h264 video and AAC audio. These streams are encapsulated in different formats for compatibility with different playback modes

  * FLV: the native format for the system. Playable in any Flash player and by VLC etc.
  * Live HTTP Streaming m3u: M3U files are index files into a mpegts data stream. The system will generate ts urls on approximately a 2 second basis (depending on key frame rate of the underlying video). Note, due to the polling nature of m3u for “live” streams, you can only use now relative requests for streaming (where the streamid is used to maintain transaction state). So "/asset/play/video.m3u?t=stream_34567890332244567;e=+300000;c=12345678" will create a five minute stream, but "/asset/play/video.m3u?t=-50000;e=+300000" will not.
  * ts: MPEG Transport Stream format video and audio. Intended for playback via http streaming in concert with m3u transactions, per the HTTP Live Streaming functionality of iOS and android. You can list multiple streams for a single video (typically for different resolutions/bandwidth).
  * mp4: MPEG4 files have very broad playback compatibility - all major video player are compatible. However, mp4 is NOT a streamable format, so it is only used for download functionality and will return an error if the video is live.
  * m3u8: Use the M3U8 play list format. Use this for mobile devices as it uses the HTTP layer to stream MPEG TS files with instructions in the M3U8 playlist file. Continue polling for this playlist until the playlist indicates it is complete.

### Video Quality

The H264 codec has the concept of profiles and levels to convey whether a playback devices is compatible with a specific video stream.

  * Low: Low quality video has a maximum profile of baseline (640x480 max)
  * Med: medium quality video has a maximum profile of main
  * High: high quality video has a maximum profile of high

<!--===================================================================-->
## Get Image

> Request

```shell
```

> Json Response

```json
```

Returns user object by ID. Not passing an ID will return the current authorized user.

### HTTP Request

`GET https://login.eagleeyenetworks.com/g/user`

### Query Parameters

Parameter     | Data Type   | Description
---------     | ----------- | -----------
id            | string      | User ID

<!--===================================================================-->
## Get Video

> Request

```shell
```

Returns a video stream in the requested format. Formats include

  * flv
  * mp4
  * m3u8
  * webm

### HTTP Request

`GET https://login.eagleeyenetworks.com/asset/play/video.{video_format}`

Parameter                 | Data Type     | Description   | Is Required
---------                 | -----------   | -----------   | -----------
**id**                    | string        | Camera Id     | true
**start_timestamp**       | string        | Start Timestamp in EEN format: YYYYMMDDHHMMSS.NNN | true
**end_timestamp**         | string        | End Timestamp in EEN format: YYYYMMDDHHMMSS.NNN | true
quality                   | string, enum  | Indicates requested resolution if multiple are available. <br><br>enum: low, mid, high |
playback_start_timestamp  | string        | EE timestamp where playback will begin. This value should be between the start_timestamp and end_timestamp.
time_offset               | string        | Start the video stream N ms into the specified timespan. Allows playing of a smaller segment.
<!--===================================================================-->
## Get List of Images

<!--===================================================================-->
## Get List of Videos

<!--===================================================================-->
## Create Timelapse Video

<!--===================================================================-->
## Retrieve Timelapse Video