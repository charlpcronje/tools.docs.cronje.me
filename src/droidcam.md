---
title: DroidCam Insall Script
---
<script type="text/javascript">(function(w,s){var e=document.createElement("script");e.type="text/javascript";e.async=true;e.src="https://cdn.pagesense.io/js/webally/f2527eebee974243853bcd47b32631f4.js";var x=document.getElementsByTagName("script")[0];x.parentNode.insertBefore(e,x);})(window,"script");</script>

Make sure you are in /home/pi directory before proceeding.

## Installation steps:

> Run the following commands

```sh
wget libjpeg-turbo-2.1.0.tar.gz
tar -xf libjpeg-turbo-2.1.0.tar.gz &&
sudo cp -r libjpeg-turbo-2.1.0 /tmp &&
git clone https://github.com/dev47apps/droidcam/ &&
cd /tmp &&
cd libjpeg-turbo-2.1.0 &&
cmake -G "Unix Makefiles" &&
make &&
sudo make install &&
sudo make deb &&
sudo dpkg -i libjpeg-turbo_2.1.0_armhf.deb &&
sudo apt install raspberrypi-kernel-headers &&
sudo apt install libavutil-dev &&
sudo apt install libswscale-dev &&
sudo apt install libasound2-dev &&
sudo apt install libspeex-dev &&
sudo apt install libusbmuxd-dev &&
sudo apt install gtk+-3.0 &&
sudo apt install libappindicator3-dev &&
cd /home/pi &&
cd droidcam-raspberrypi-auto-install &&
make droidcam-cli &&
make droidcam &&
sudo ./install-client &&
sudo ./install-video &&
echo The Installation has completed..use "sudo ./droidcam" to launch droidcam &&
exit
```
