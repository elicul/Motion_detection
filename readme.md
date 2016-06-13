# Motion detection

Motion detection is a web application that uses Motion to detect motion from an android net Ip webcam on a raspberry pi and Google Cloud Vision API to analyze the picture from motion.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisities

Things you need to install:

```
Git, Php, MySql, Nginx, Motion, Ip Webcam and Python 2.7
```

### Installing

A step by step series of examples that tell you have to get a development env running

Git install

```
sudo apt-get update
sudo apt-get install git
```

Php install

```
sudo apt-get install php5-fpm
sudo nano /etc/php5/fpm/php.ini

Change the following line ;cgi.fix_pathinfo=1 into cgi.fix_pathinfo=0

sudo service php5-fpm restart
```

MySql install

```
sudo apt-get install mysql-server php5-mysqlsudo apt-get install mysql-server php5-mysql
sudo mysql_install_db
sudo /usr/bin/mysql_secure_installation
sudo php5enmod mcrypt
sudo service php5-fpm restart
```

Nginx install

```
sudo apt-get install nginx
sudo service nginx start
```


Nginx configuration file (/etc/nginx/sites-available/default)

```
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /home/websites/motion/public;
    index index.php index.html index.htm;

    server_name [server ip address];

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location /webcam {
	proxy_redirect off;
        proxy_buffering off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size 10m;
        client_body_buffer_size 128k;
        proxy_connect_timeout 10;
        proxy_send_timeout 10;
        proxy_read_timeout 10;

        proxy_pass http://localhost:8081;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
	try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
 	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_index index.php;
        include fastcgi_params;
    }

}
```
Ip Webcam install (Google Play Market)

```
Start the app and select Start Server **Remeber the cam IP**
```

Python modules install

```
sudo pip install --upgrade google-api-python-client
sudo pip install python-gflags
sudo pip install mysql-connector-python-rf
```

Motion install

```
sudo apt-get install motion
sudo apt-get install libv4l-0
sudo apt-get install uvccapture
```

Motion configuration file (/etc/motion/motion.conf)

```
# Rename this distribution example file to motion.conf
#
# This config file was generated by motion 3.2.12+git20140228


############################################################
# Daemon
############################################################

# Start in daemon (background) mode and release terminal (default: off)
daemon off

# File to store the process ID, also called pid file. (default: not defined)
process_id_file /var/run/motion/motion.pid

############################################################
# Basic Setup Mode
############################################################

# Start in Setup-Mode, daemon disabled. (default: off)
setup_mode off


# Use a file to save logs messages, if not defined stderr and syslog is used. (default: not defined)
;logfile /tmp/motion.log

# Level of log messages [1..9] (EMR, ALR, CRT, ERR, WRN, NTC, INF, DBG, ALL). (default: 6 / NTC)
log_level 6

# Filter to log messages by type (COR, STR, ENC, NET, DBL, EVT, TRK, VID, ALL). (default: ALL)
log_type all

###########################################################
# Capture device options
############################################################

# Videodevice to be used for capturing  (default /dev/video0)
# for FreeBSD default is /dev/bktr0
videodevice /dev/video0

# v4l2_palette allows to choose preferable palette to be use by motion
# to capture from those supported by your videodevice. (default: 17)
# E.g. if your videodevice supports both V4L2_PIX_FMT_SBGGR8 and
# V4L2_PIX_FMT_MJPEG then motion will by default use V4L2_PIX_FMT_MJPEG.
# Setting v4l2_palette to 2 forces motion to use V4L2_PIX_FMT_SBGGR8
# instead.
#
# Values :
# V4L2_PIX_FMT_SN9C10X : 0  'S910'
# V4L2_PIX_FMT_SBGGR16 : 1  'BYR2'
# V4L2_PIX_FMT_SBGGR8  : 2  'BA81'
# V4L2_PIX_FMT_SPCA561 : 3  'S561'
# V4L2_PIX_FMT_SGBRG8  : 4  'GBRG'
# V4L2_PIX_FMT_SGRBG8  : 5  'GRBG'
# V4L2_PIX_FMT_PAC207  : 6  'P207'
# V4L2_PIX_FMT_PJPG    : 7  'PJPG'
# V4L2_PIX_FMT_MJPEG   : 8  'MJPEG'
# V4L2_PIX_FMT_JPEG    : 9  'JPEG'
# V4L2_PIX_FMT_RGB24   : 10 'RGB3'
# V4L2_PIX_FMT_SPCA501 : 11 'S501'
# V4L2_PIX_FMT_SPCA505 : 12 'S505'
# V4L2_PIX_FMT_SPCA508 : 13 'S508'
# V4L2_PIX_FMT_UYVY    : 14 'UYVY'
# V4L2_PIX_FMT_YUYV    : 15 'YUYV'
# V4L2_PIX_FMT_YUV422P : 16 '422P'
# V4L2_PIX_FMT_YUV420  : 17 'YU12'
#
v4l2_palette 17

# Tuner device to be used for capturing using tuner as source (default /dev/tuner0)
# This is ONLY used for FreeBSD. Leave it commented out for Linux
; tunerdevice /dev/tuner0

# The video input to be used (default: -1)
# Should normally be set to 0 or 1 for video/TV cards, and -1 for USB cameras
input -1

# The video norm to use (only for video capture and TV tuner cards)
# Values: 0 (PAL), 1 (NTSC), 2 (SECAM), 3 (PAL NC no colour). Default: 0 (PAL)
norm 0

# The frequency to set the tuner to (kHz) (only for TV tuner cards) (default: 0)
frequency 0

# Rotate image this number of degrees. The rotation affects all saved images as
# well as movies. Valid values: 0 (default = no rotation), 90, 180 and 270.
rotate 0

# Image width (pixels). Valid range: Camera dependent, default: 352
width 864

# Image height (pixels). Valid range: Camera dependent, default: 288
height 480

# Maximum number of frames to be captured per second.
# Valid range: 2-100. Default: 100 (almost no limit).
framerate 30

# Minimum time in seconds between capturing picture frames from the camera.
# Default: 0 = disabled - the capture rate is given by the camera framerate.
# This option is used when you want to capture images at a rate lower than 2 per second.
minimum_frame_time 0

# URL to use if you are using a network camera, size will be autodetected (incl http:// ftp:// mjpg:// rstp:// or file:///)
# Must be a URL that returns single jpeg pictures or a raw mjpeg stream. Default: Not defined
netcam_url http://192.168.1.149:8080/video ** Ip address of the smartphone cam **

# Username and password for network camera (only if required). Default: not defined
# Syntax is user:password
; netcam_userpass value

# The setting for keep-alive of network socket, should improve performance on compatible net cameras.
# off:   The historical implementation using HTTP/1.0, closing the socket after each http request.
# force: Use HTTP/1.0 requests with keep alive header to reuse the same connection.
# on:    Use HTTP/1.1 requests that support keep alive as default.
# Default: off
netcam_keepalive off

# URL to use for a netcam proxy server, if required, e.g. "http://myproxy".
# If a port number other than 80 is needed, use "http://myproxy:1234".
# Default: not defined
; netcam_proxy value

# Set less strict jpeg checks for network cameras with a poor/buggy firmware.
# Default: off
netcam_tolerant_check off

# Let motion regulate the brightness of a video device (default: off).
# The auto_brightness feature uses the brightness option as its target value.
# If brightness is zero auto_brightness will adjust to average brightness value 128.
# Only recommended for cameras without auto brightness
auto_brightness off

# Set the initial brightness of a video device.
# If auto_brightness is enabled, this value defines the average brightness level
# which Motion will try and adjust to.
# Valid range 0-255, default 0 = disabled
brightness 0

# Set the contrast of a video device.
# Valid range 0-255, default 0 = disabled
contrast 0

# Set the saturation of a video device.
# Valid range 0-255, default 0 = disabled
saturation 0

# Set the hue of a video device (NTSC feature).
# Valid range 0-255, default 0 = disabled
hue 0


############################################################
# Round Robin (multiple inputs on same video device name)
############################################################

# Number of frames to capture in each roundrobin step (default: 1)
roundrobin_frames 1

# Number of frames to skip before each roundrobin step (default: 1)
roundrobin_skip 1

# Try to filter out noise generated by roundrobin (default: off)
switchfilter off


############################################################
# Motion Detection Settings:
############################################################

# Threshold for number of changed pixels in an image that
# triggers motion detection (default: 1500)
threshold 500

# Automatically tune the threshold down if possible (default: off)
threshold_tune off

# Noise threshold for the motion detection (default: 32)
noise_level 32

# Automatically tune the noise threshold (default: on)
noise_tune on

# Despeckle motion image using (e)rode or (d)ilate or (l)abel (Default: not defined)
# Recommended value is EedDl. Any combination (and number of) of E, e, d, and D is valid.
# (l)abeling must only be used once and the 'l' must be the last letter.
# Comment out to disable
despeckle_filter EedDl

# Detect motion in predefined areas (1 - 9). Areas are numbered like that:  1 2 3
# A script (on_area_detected) is started immediately when motion is         4 5 6
# detected in one of the given areas, but only once during an event.        7 8 9
# One or more areas can be specified with this option. Take care: This option
# does NOT restrict detection to these areas! (Default: not defined)
; area_detect value

# PGM file to use as a sensitivity mask.
# Full path name to. (Default: not defined)
; mask_file value

# Dynamically create a mask file during operation (default: 0)
# Adjust speed of mask changes from 0 (off) to 10 (fast)
smart_mask_speed 0

# Ignore sudden massive light intensity changes given as a percentage of the picture
# area that changed intensity. Valid range: 0 - 100 , default: 0 = disabled
lightswitch 0

# Picture frames must contain motion at least the specified number of frames
# in a row before they are detected as true motion. At the default of 1, all
# motion is detected. Valid range: 1 to thousands, recommended 1-5
minimum_motion_frames 1

# Specifies the number of pre-captured (buffered) pictures from before motion
# was detected that will be output at motion detection.
# Recommended range: 0 to 5 (default: 0)
# Do not use large values! Large values will cause Motion to skip video frames and
# cause unsmooth movies. To smooth movies use larger values of post_capture instead.
pre_capture 0

# Number of frames to capture after motion is no longer detected (default: 0)
post_capture 0

# Event Gap is the seconds of no motion detection that triggers the end of an event.
# An event is defined as a series of motion images taken within a short timeframe.
# Recommended value is 60 seconds (Default). The value -1 is allowed and disables
# events causing all Motion to be written to one single movie file and no pre_capture.
# If set to 0, motion is running in gapless mode. Movies don't have gaps anymore. An
# event ends right after no more motion is detected and post_capture is over.
event_gap 5

# Maximum length in seconds of a movie
# When value is exceeded a new movie file is created. (Default: 0 = infinite)
max_movie_time 0

# Always save images even if there was no motion (default: off)
emulate_motion off


############################################################
# Image File Output
############################################################

# Output 'normal' pictures when motion is detected (default: on)
# Valid values: on, off, first, best, center
# When set to 'first', only the first picture of an event is saved.
# Picture with most motion of an event is saved when set to 'best'.
# Picture with motion nearest center of picture is saved when set to 'center'.
# Can be used as preview shot for the corresponding movie.
output_pictures center

# Output pictures with only the pixels moving object (ghost images) (default: off)
output_debug_pictures off

# The quality (in percent) to be used by the jpeg compression (default: 75)
quality 100

# Type of output images
# Valid values: jpeg, ppm (default: jpeg)
picture_type jpeg

############################################################
# FFMPEG related options
# Film (movies) file output, and deinterlacing of the video input
# The options movie_filename and timelapse_filename are also used
# by the ffmpeg feature
############################################################

# Use ffmpeg to encode movies in realtime (default: off)
ffmpeg_output_movies off

# Use ffmpeg to make movies with only the pixels moving
# object (ghost images) (default: off)
ffmpeg_output_debug_movies off

# Use ffmpeg to encode a timelapse movie
# Default value 0 = off - else save frame every Nth second
ffmpeg_timelapse 0

# The file rollover mode of the timelapse video
# Valid values: hourly, daily (default), weekly-sunday, weekly-monday, monthly, manual
ffmpeg_timelapse_mode daily

# Bitrate to be used by the ffmpeg encoder (default: 400000)
# This option is ignored if ffmpeg_variable_bitrate is not 0 (disabled)
ffmpeg_bps 500000

# Enables and defines variable bitrate for the ffmpeg encoder.
# ffmpeg_bps is ignored if variable bitrate is enabled.
# Valid values: 0 (default) = fixed bitrate defined by ffmpeg_bps,
# or the range 2 - 31 where 2 means best quality and 31 is worst.
ffmpeg_variable_bitrate 0

# Codec to used by ffmpeg for the video compression.
# Timelapse mpegs are always made in mpeg1 format independent from this option.
# Supported formats are: mpeg1 (ffmpeg-0.4.8 only), mpeg4 (default), and msmpeg4.
# mpeg1 - gives you files with extension .mpg
# mpeg4 or msmpeg4 - gives you files with extension .avi
# msmpeg4 is recommended for use with Windows Media Player because
# it requires no installation of codec on the Windows client.
# swf - gives you a flash film with extension .swf
# flv - gives you a flash video with extension .flv
# ffv1 - FF video codec 1 for Lossless Encoding ( experimental )
# mov - QuickTime ( testing )
# ogg - Ogg/Theora ( testing )
ffmpeg_video_codec mpeg4

# Use ffmpeg to deinterlace video. Necessary if you use an analog camera
# and see horizontal combing on moving objects in video or pictures.
# (default: off)
ffmpeg_deinterlace off

############################################################
# SDL Window
############################################################

# Number of motion thread to show in SDL Window (default: 0 = disabled)
sdl_threadnr 0

############################################################
# External pipe to video encoder
# Replacement for FFMPEG builtin encoder for ffmpeg_output_movies only.
# The options movie_filename and timelapse_filename are also used
# by the ffmpeg feature
#############################################################

# Bool to enable or disable extpipe (default: off)
use_extpipe off

# External program (full path and opts) to pipe raw video to
# Generally, use '-' for STDIN...
;extpipe mencoder -demuxer rawvideo -rawvideo w=320:h=240:i420 -ovc x264 -x264encopts bframes=4:frameref=1:subq=1:scenecut=-1:nob_adapt:threads=1:keyint=1000:8x8dct:vbv_bufsize=4000:crf=24:partitions=i8x8,i4x4:vbv_maxrate=800:no-chroma-me -vf denoise3d=16:12:48:4,pp=lb -of   avi -o %f.avi - -fps %fps



############################################################
# Snapshots (Traditional Periodic Webcam File Output)
############################################################

# Make automated snapshot every N seconds (default: 0 = disabled)
snapshot_interval 0


############################################################
# Text Display
# %Y = year, %m = month, %d = date,
# %H = hour, %M = minute, %S = second, %T = HH:MM:SS,
# %v = event, %q = frame number, %t = thread (camera) number,
# %D = changed pixels, %N = noise level, \n = new line,
# %i and %J = width and height of motion area,
# %K and %L = X and Y coordinates of motion center
# %C = value defined by text_event - do not use with text_event!
# You can put quotation marks around the text to allow
# leading spaces
############################################################

# Locate and draw a box around the moving object.
# Valid values: on, off, preview (default: off)
# Set to 'preview' will only draw a box in preview_shot pictures.
locate_motion_mode off

# Set the look and style of the locate box if enabled.
# Valid values: box, redbox, cross, redcross (default: box)
# Set to 'box' will draw the traditional box.
# Set to 'redbox' will draw a red box.
# Set to 'cross' will draw a little cross to mark center.
# Set to 'redcross' will draw a little red cross to mark center.
locate_motion_style box

# Draws the timestamp using same options as C function strftime(3)
# Default: %Y-%m-%d\n%T = date in ISO format and time in 24 hour clock
# Text is placed in lower right corner
text_right %Y-%m-%d\n%T-%q

# Draw a user defined text on the images using same options as C function strftime(3)
# Default: Not defined = no text
# Text is placed in lower left corner
; text_left CAMERA %t

# Draw the number of changed pixed on the images (default: off)
# Will normally be set to off except when you setup and adjust the motion settings
# Text is placed in upper right corner
text_changes off

# This option defines the value of the special event conversion specifier %C
# You can use any conversion specifier in this option except %C. Date and time
# values are from the timestamp of the first image in the current event.
# Default: %Y%m%d%H%M%S
# The idea is that %C can be used filenames and text_left/right for creating
# a unique identifier for each event.
text_event %Y%m%d%H%M%S

# Draw characters at twice normal size on images. (default: off)
text_double off


# Text to include in a JPEG EXIF comment
# May be any text, including conversion specifiers.
# The EXIF timestamp is included independent of this text.
;exif_text %i%J/%K%L

############################################################
# Target Directories and filenames For Images And Films
# For the options snapshot_, picture_, movie_ and timelapse_filename
# you can use conversion specifiers
# %Y = year, %m = month, %d = date,
# %H = hour, %M = minute, %S = second,
# %v = event, %q = frame number, %t = thread (camera) number,
# %D = changed pixels, %N = noise level,
# %i and %J = width and height of motion area,
# %K and %L = X and Y coordinates of motion center
# %C = value defined by text_event
# Quotation marks round string are allowed.
############################################################

# Target base directory for pictures and films
# Recommended to use absolute path. (Default: current working directory)
target_dir /home/websites/motion/public/images/motion/

# File path for snapshots (jpeg or ppm) relative to target_dir
# Default: %v-%Y%m%d%H%M%S-snapshot
# Default value is equivalent to legacy oldlayout option
# For Motion 3.0 compatible mode choose: %Y/%m/%d/%H/%M/%S-snapshot
# File extension .jpg or .ppm is automatically added so do not include this.
# Note: A symbolic link called lastsnap.jpg created in the target_dir will always
# point to the latest snapshot, unless snapshot_filename is exactly 'lastsnap'
snapshot_filename %v-%Y%m%d%H%M%S-snapshot

# File path for motion triggered images (jpeg or ppm) relative to target_dir
# Default: %v-%Y%m%d%H%M%S-%q
# Default value is equivalent to legacy oldlayout option
# For Motion 3.0 compatible mode choose: %Y/%m/%d/%H/%M/%S-%q
# File extension .jpg or .ppm is automatically added so do not include this
# Set to 'preview' together with best-preview feature enables special naming
# convention for preview shots. See motion guide for details
picture_filename %v-%Y%m%d%H%M%S-%q

# File path for motion triggered ffmpeg films (movies) relative to target_dir
# Default: %v-%Y%m%d%H%M%S
# Default value is equivalent to legacy oldlayout option
# For Motion 3.0 compatible mode choose: %Y/%m/%d/%H%M%S
# File extension .mpg or .avi is automatically added so do not include this
# This option was previously called ffmpeg_filename
movie_filename %v-%Y%m%d%H%M%S

# File path for timelapse movies relative to target_dir
# Default: %Y%m%d-timelapse
# Default value is near equivalent to legacy oldlayout option
# For Motion 3.0 compatible mode choose: %Y/%m/%d-timelapse
# File extension .mpg is automatically added so do not include this
timelapse_filename %Y%m%d-timelapse

############################################################
# Global Network Options
############################################################
# Enable or disable IPV6 for http control and stream (default: off )
ipv6_enabled off

############################################################
# Live Stream Server
############################################################

# The mini-http server listens to this port for requests (default: 0 = disabled)
stream_port 8081

# Quality of the jpeg (in percent) images produced (default: 50)
stream_quality 100

# Output frames at 1 fps when no motion is detected and increase to the
# rate given by stream_maxrate when motion is detected (default: off)
stream_motion off

# Maximum framerate for stream streams (default: 1)
stream_maxrate 30

# Restrict stream connections to localhost only (default: on)
stream_localhost off

# Limits the number of images per connection (default: 0 = unlimited)
# Number can be defined by multiplying actual stream rate by desired number of seconds
# Actual stream rate is the smallest of the numbers framerate and stream_maxrate
stream_limit 0

# Set the authentication method (default: 0)
# 0 = disabled
# 1 = Basic authentication
# 2 = MD5 digest (the safer authentication)
stream_auth_method 0

# Authentication for the stream. Syntax username:password
# Default: not defined (Disabled)
; stream_authentication username:password


############################################################
# HTTP Based Control
############################################################

# TCP/IP port for the http server to listen on (default: 0 = disabled)
webcontrol_port 8080

# Restrict control connections to localhost only (default: on)
webcontrol_localhost off

# Output for http server, select off to choose raw text plain (default: on)
webcontrol_html_output on

# Authentication for the http based control. Syntax username:password
# Default: not defined (Disabled)
; webcontrol_authentication username:password


############################################################
# Tracking (Pan/Tilt)
#############################################################

# Type of tracker (0=none (default), 1=stepper, 2=iomojo, 3=pwc, 4=generic, 5=uvcvideo, 6=servo)
# The generic type enables the definition of motion center and motion size to
# be used with the conversion specifiers for options like on_motion_detected
track_type 0

# Enable auto tracking (default: off)
track_auto off

# Serial port of motor (default: none)
;track_port /dev/ttyS0

# Motor number for x-axis (default: 0)
;track_motorx 0

# Set motorx reverse (default: 0)
;track_motorx_reverse 0

# Motor number for y-axis (default: 0)
;track_motory 1

# Set motory reverse (default: 0)
;track_motory_reverse 0

# Maximum value on x-axis (default: 0)
;track_maxx 200

# Minimum value on x-axis (default: 0)
;track_minx 50

# Maximum value on y-axis (default: 0)
;track_maxy 200

# Minimum value on y-axis (default: 0)
;track_miny 50

# Center value on x-axis (default: 0)
;track_homex 128

# Center value on y-axis (default: 0)
;track_homey 128

# ID of an iomojo camera if used (default: 0)
track_iomojo_id 0

# Angle in degrees the camera moves per step on the X-axis
# with auto-track (default: 10)
# Currently only used with pwc type cameras
track_step_angle_x 10

# Angle in degrees the camera moves per step on the Y-axis
# with auto-track (default: 10)
# Currently only used with pwc type cameras
track_step_angle_y 10

# Delay to wait for after tracking movement as number
# of picture frames (default: 10)
track_move_wait 10

# Speed to set the motor to (stepper motor option) (default: 255)
track_speed 255

# Number of steps to make (stepper motor option) (default: 40)
track_stepsize 40


############################################################
# External Commands, Warnings and Logging:
# You can use conversion specifiers for the on_xxxx commands
# %Y = year, %m = month, %d = date,
# %H = hour, %M = minute, %S = second,
# %v = event, %q = frame number, %t = thread (camera) number,
# %D = changed pixels, %N = noise level,
# %i and %J = width and height of motion area,
# %K and %L = X and Y coordinates of motion center
# %C = value defined by text_event
# %f = filename with full path
# %n = number indicating filetype
# Both %f and %n are only defined for on_picture_save,
# on_movie_start and on_movie_end
# Quotation marks round string are allowed.
############################################################

# Do not sound beeps when detecting motion (default: on)
# Note: Motion never beeps when running in daemon mode.
quiet on

# Command to be executed when an event starts. (default: none)
# An event starts at first motion detected after a period of no motion defined by event_gap
; on_event_start value

# Command to be executed when an event ends after a period of no motion
# (default: none). The period of no motion is defined by option event_gap.
; on_event_end value

# Command to be executed when a picture (.ppm|.jpg) is saved (default: none)
# To give the filename as an argument to a command append it with %f
on_picture_save python /home/websites/motion/public/py/vision.py %f

# Command to be executed when a motion frame is detected (default: none)
; on_motion_detected value

# Command to be executed when motion in a predefined area is detected
# Check option 'area_detect'.   (default: none)
; on_area_detected value

# Command to be executed when a movie file (.mpg|.avi) is created. (default: none)
# To give the filename as an argument to a command append it with %f
; on_movie_start value

# Command to be executed when a movie file (.mpg|.avi) is closed. (default: none)
# To give the filename as an argument to a command append it with %f
; on_movie_end value

# Command to be executed when a camera can't be opened or if it is lost
# NOTE: There is situations when motion don't detect a lost camera!
# It depends on the driver, some drivers dosn't detect a lost camera at all
# Some hangs the motion thread. Some even hangs the PC! (default: none)
; on_camera_lost value

#####################################################################
# Common Options for database features.
# Options require database options to be active also.
#####################################################################

# Log to the database when creating motion triggered picture file  (default: on)
; sql_log_picture on

# Log to the database when creating a snapshot image file (default: on)
; sql_log_snapshot on

# Log to the database when creating motion triggered movie file (default: off)
; sql_log_movie off

# Log to the database when creating timelapse movies file (default: off)
; sql_log_timelapse off

# SQL query string that is sent to the database
# Use same conversion specifiers has for text features
# Additional special conversion specifiers are
# %n = the number representing the file_type
# %f = filename with full path
# Default value:
# Create tables :
## 
# Mysql
# CREATE TABLE security (camera int, filename char(80) not null, frame int, file_type int, time_stamp timestamp(14), event_time_stamp timestamp(14));
#
# Postgresql
# CREATE TABLE security (camera int, filename char(80) not null, frame int, file_type int, time_stamp timestamp without time zone, event_time_stamp timestamp without time zone);
#
# insert into security(camera, filename, frame, file_type, time_stamp, text_event) values('%t', '%f', '%q', '%n', '%Y-%m-%d %T', '%C')
; sql_query insert into security(camera, filename, frame, file_type, time_stamp, event_time_stamp) values('%t', '%f', '%q', '%n', '%Y-%m-%d %T', '%C')


############################################################
# Database Options
############################################################

# database type : mysql, postgresql, sqlite3 (default : not defined)
; database_type value

# database to log to (default: not defined)
; database_dbname value

# The host on which the database is located (default: localhost)
; database_host value

# User account name for database (default: not defined)
; database_user value

# User password for database (default: not defined)
; database_password value

# Port on which the database is located
#  mysql 3306 , postgresql 5432 (default: not defined)
; database_port value

############################################################
# Database Options For SQLite3
############################################################

# SQLite3 database (file path) (default: not defined)
; sqlite3_db value



############################################################
# Video Loopback Device (vloopback project)
############################################################

# Output images to a video4linux loopback device
# The value '-' means next available (default: not defined)
; video_pipe value

# Output motion images to a video4linux loopback device
# The value '-' means next available (default: not defined)
; motion_video_pipe value


##############################################################
# Thread config files - One for each camera.
# Except if only one camera - You only need this config file.
# If you have more than one camera you MUST define one thread
# config file for each camera in addition to this config file.
##############################################################

# Remember: If you have more than one camera you must have one
# thread file for each camera. E.g. 2 cameras requires 3 files:
# This motion.conf file AND thread1.conf and thread2.conf.
# Only put the options that are unique to each camera in the
# thread config files.
; thread /etc/motion/thread1.conf
; thread /etc/motion/thread2.conf
; thread /etc/motion/thread3.conf
; thread /etc/motion/thread4.conf

```

## Running the application

To run the application you'll need to create a project in the Google Cloud and get the .json credentials (https://cloud.google.com/vision/docs/quickstart#whats_next and https://cloud.google.com/vision/docs/auth-template/cloud-api-auth#using_the_console_name_short). After you have done that copy your clinet_secret.json and storage.json in motion/public/py directory. Now get the ip address of your raspberry and open it in your browser **example http://192.168.1.136/**

## Built With

* Php
* Python
* Laravel - Php framework
* Bootstrap - Css framework
* jQuery - Js framework

## Authors

* **Enzo Licul** - [Enzo Licul](https://github.com/elicul)
* **Rikardo Jakus** - [Rikardo Jakus](https://github.com/rjakus)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
