# Png Span

<!--===================================================================-->
## Overview

This service offers native PNG span rendering to support metric visualization. For spans, the image is filled with the foreground color where the specified span is active, and with the background where it is inactive. At least one pixel will be filled for a span, independent of scale, though the span may overlap others. For etags, one pixel is filled for each active event, but as with spans the pixel may overlap others.

### Response Headers

content-location: resource actually rendered. absolute start/end ts and either table/etag depending on whether an index was used

### Discussion

The pngspan is a very efficient mechanism for visualizing where metrics and spans are active. Scale the image vertically as needed. PNG are extremely compact - a day of spans will be a few hundred bytes.

Tile the PNGs for fast, infinite scrolling. Render a width/timespan that represents a rational chunk of the current screen - say 4 hours in a day view. Fill the screen with tiles, fetch offscreen at the same size in preparation to scroll. Change origin of each entity to accomplish fast smooth scrolling. Fetch successive offscreen buffers as they come on screen.

Hit detection (for rollover) can be done in a browser by rendering opaque colors and reading pixels values from a one pixel high offscreen image. If an active pixel is detected, fetch the window of events around the timestamp estimate (since the pixel resolution is usually much less than the ms resolution needed for a timestamp), and use the response to determine what metric/span to display (ie the closest one…).

### PNG Types

  * setting
  	* table=onoff - "camera_on" setting, the inverse of which represents camera "off"
  * purge
	* fflags=LOST - filter for only purges the lost data (within retention window)
  * span
  	* table=video - video recording on
	  * fflags=STREAM - filter for only streaming video	
	* table=motion - motion detected (overall)
	  * fflags=ALERTS - filter for only motion that triggered alert
	* table=roim - roi motion detected
	  * fflags=ALERTS - filter for only roim that triggered alert
	  * flname=roiid, flval=roiid value
	* table=stream - bridge is streaming from camera, the inverse of which represents camera "offline"
	* table=register - camera is registered with the cloud, the inverse of which represents "internet offline"

<!--===================================================================-->
## Get Png Span

> Request

```shell
```

> Json Response

```json
```