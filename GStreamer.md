# GStreamer
This repository is collection of my knowledge of GStreamer

# Commands
`v4l2-ctl --list-formats-ext`
 - v4l2 = video 4 Linux 2
 - v4l2-ctl = video 4 linux - control
You should see something like this:
```
ioctl: VIDIOC_ENUM_FMT
	Index       : 0
	Type        : Video Capture
	Pixel Format: 'MJPG' (compressed)
	Name        : Motion-JPEG
		Size: Discrete 1280x720
			Interval: Discrete 0.033s (30.000 fps)
		Size: Discrete 960x720
			Interval: Discrete 0.033s (30.000 fps)
		Size: Discrete 640x480
			Interval: Discrete 0.033s (30.000 fps)
	Index       : 1
	Type        : Video Capture
	Pixel Format: 'YUYV'
	Name        : YUYV 4:2:2
		Size: Discrete 1280x720
			Interval: Discrete 0.100s (10.000 fps)
		Size: Discrete 960x720
			Interval: Discrete 0.100s (10.000 fps)
		Size: Discrete 640x480
			Interval: Discrete 0.100s (10.000 fps)
```
This command asks the camera which camera format is supports
`v4l2-ctl --device=/dev/video2 --list-formats-ext`
Do:
`ls /dev/video*`
and you should notice 4 video device 0-3. If you see this, it means the camera device you are using isn't supported. This is a weird ROckchip problem.
```
/mnt/app/scripts # v4l2-ctl --device=/dev/video1 --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
```

```
gst-launch-1.0 -v v4l2src device=/dev/video0 ! \
image/jpeg,width=1280,height=720 ! \
jpegdec ! \
videoconvert ! \
kmssink sync=false
```

- So if you dissect this a bit youll notice the v4l2src (video for linux) then the next piece is  called caps (short for capbailities)
- The caps tells the video which format to choose, and we know from the v4l2-ctl that it supports MJPG (motion jpeg) and YUYV (raw)
- Since its cranking out jpegs, we push it thru a decoder  and a videoconvert. Videoconvert is smart it converts colorspaces for the stuff before and after it. its like a lazy way to link stuff together.
- kmssink is a video display (it throws stuff on the screen)
- How do i know to use the videoconvert ? just experience. but you can also examine the kmssink using gst-inspect-1.0 and it'll tell you what inputs it accepts
just so you dont have to look it accepts NV12 and NV16 color spaces. the output of the jpegdec is YUV420 (you dont need to know what these are, just that theyre like RGB, but different) so the videoconvert asks jpegdec what format are you outputtting, and kmssink, whats the input format, and it converts one to the other. Magic!


```
gst-launch-1.0 -v udpsrc port=5006 buffer-size=2097152 ! \
application/x-rtp, payload=26 ! \
rtpjpegdepay ! \
jpegdec ! \
videoconvert ! \
kmssink sync=false &
```

- udpsrc - the video is sent thru a udp socket. qwc sends it, the ui picks it up. if you dont know what udp is, its a quick google.
- rtpjpegdepay - real time protocol jpeg depayloader. so think of the udp packets as serial 01010101010, right ? well the depayloader grabs chucks of the serial data, and puts it into 2 dimensional frames (jpegs in this case)
- jpegdec - decodes the jpeg to some raw colors
- video convert - magic/lazy converter
- kmssync - show it on the screen
# Links
- https://gstreamer.freedesktop.org/
