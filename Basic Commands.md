Unix Root:

Username: root

Password: rock

#### To detect connected android devices:

`adb devices`

#### To disable a CPU core:

`echo 0 > /sys/devices/system/cpu/cpu1/online`

#### To enable a CPU core:

`echo 1 > /sys/devices/system/cpu/cpu1/online`

#### To display a list of processors and memory:

`htop`

#### To test the Touch Panel:

`evtest`, then select 0

#### To search for processes:

`ps` will list all running processes

#### Or to search for specific i.e. (Wire) use grep:

`ps | grep Wire`

#### To kill a task:

`kill -9 <pid from qWirelessController>`

#### To manually switch the switch from I2C to Broadcom:

`cd /sys/class/gpio`

`echo 1 > gpio230/value`

`echo 1 > gpio231/value`

#### To restart the qWC after killing it:

`cd /mnt/app/bin`

`./qWirelessController -q -t ./inspSettings.json &`

#### To detect I2C devices

`i2cdetect -y 1` (where one is the bus number 1-6)

#### The device will show up as a hex address (i.e. 6b)

To probe further : `i2cget -y 3 0x6b 0` (0 is lower 8 bits 7,6,5,4,3,2,1,0)

To probe further : `i2cget -y 3 0x6b 1` (1 is higher 8 bits 15,14,13,12,11,10,9,8)

For example: lower returns 0x37. higher returns 0x1b

So Register 00h settings are: 0x1b0x37 = 0001 1011 0011 0111

Register 01h would use a 2,3.

Register 02h would use a 4,5 and so on.

#### For Wi-Fi, MAC etc. technical information:

`Ifconfig`

`Ifconfig -a`

#### To see if eMMC is good:

`ls /dev/`

You should see mmcblk1 (missing on below so not good below)

#### To stream i2cget messages (i.e. to check for Accel data changing), add watch n1 to the beginning (n1 means once per second).

`watch -n1 i2cget -y 1 0x1d 1`

#### Turn off the Up-is-Up:

`dbus-send --system --print-reply --type=method_call --dest='com.inspectron.qWirelessController' / com.inspectron.qWirelessController.requestEnableUIU boolean:false`

#### Turn on the Up-is-Up:

`dbus-send --system --print-reply --type=method_call --dest='com.inspectron.qWirelessController' / com.inspectron.qWirelessController.requestEnableUIU boolean:true`

#### To monitor the output angle of the G-Sensor:

`mosquitto_sub -h 127.0.0.1 -t imager/UIU/update/+`

#### Turn LED's on or OFF:

`Goto /SYS/CLASS/PWM`

Inside you will see the PWM devices (0, 1 etc).

`Goto /0`

`Echo 1 > enable`

`Echo (a number between 0 and 100%) > duty_cycle`

`Echo 0 > duty_cycle`

#### To reset the zoom level to x1 whilst UisU is enabled:

`dbus-send --system --print-reply --type=method_call --dest='com.inspectron.qWirelessController' / com.inspectron.qWirelessController.requestSetZoomLevel double:1.0`

#### To set to x0.5:

`dbus-send --system --print-reply --type=method_call --dest='com.inspectron.qWirelessController' / com.inspectron.qWirelessController.requestSetZoomLevel double:0.5`

#### To view the raw data stream, kill the GUI and run this:

` gst-launch-1.0 -v udpsrc port=5006 buffer-size=10485760 ! \
  "application/x-rtp, payload=26" ! \
  rtpjpegdepay ! \
  jpegparse ! \
  mppjpegdec ! \
  videoconvert ! \
  kmssink sync=false & `

#### To look at IC temp:

`cat /sys/devices/virtual/thermal/thermal_zone*/temp`

Units are milli-Celsius.

Top is CPU

Bottom is GPU

#### To look at IC freq:

`cat /sys/devices/system/cpu/cpufreq/policy0/cpuinfo_cur_freq`

#### To Install Picocom (Linux UART)

`sudo apt-get install picocom`

#### To Launch Picocom

`sudo picocom -b 115200 /dev/ttyUSB0`

#### To Launch adb shell on Linux

`adb shell`

#### To open Terminal in a specific directory in Linux.

Navigate to directory in explorer.

Hold SHIFT and right click

Select 'Open Terminal from Here'

#### To run a file from Terminal

`./FILENAME-ext`

e.g "./flash.sh", "./qWirelessController -q -t",  "./inspSettings.json &"
