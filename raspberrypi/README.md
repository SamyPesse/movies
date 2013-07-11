Install TV.js on a Raspberry Pi
====

## Software Stack:

    Raspbian OS – a Debian distro specially made for the rPi
    NodeJs
    tv.js
    ffmpeg
    Chromium Browser

## 1. Connect to ssh to your Raspberry Pi:

We are not going to use a keyboard for installing TV.js on the Raspberry Pi but ssh, first we need to find the Raspberry Pi IP address, for this we are going to use nmap

#### Install nmap on mac using :

	brew install nmap

#### Run nmap to find the Raspberry Pi IP :

	nmap -A -T4 192.168.0.1-254 

#### Connect using ssh :

	ssh pi@192.168.0.39

## 2. Configuration:

	sudo raspi-config

Select expand_rootfs and enable boot_todesktop.

## 3. Update system :

	sudo apt-get update
	sudo apt-get upgrade


## 4. Installing node.js

We will download the ARM binary package from Node.JS and create the directory /opt/node where we want Node.JS installed. We will not place Node.JS in ‘/usr/local’, instead we use a separate directory for Node.JS, this way it is much easier to update a manually installed package.

	sudo mkdir /opt/node
	wget http://nodejs.org/dist/v0.10.2/node-v0.10.2-linux-arm-pi.tar.gz
	tar xvzf node-v0.10.2-linux-arm-pi.tar.gz
	sudo cp -r node-v0.10.2-linux-arm-pi/* /opt/node

Finally we have to add Node.JS to our path variable. For that you have to edit the ‘/etc/profile’ configuration file.

	sudo nano /etc/profile

Add the following lines to the configuration file before the ‘export’ command.

	...
	NODE_JS_HOME="/opt/node"
	PATH="$PATH:$NODE_JS_HOME/bin"
	export PATH
	...

You now need to logout and log in again, so that the changed path variable will work. So we are going to reboot :

	sudo shutdown -r now

After that, rconnect with ssh to the raspberry ip.

## 5. Installing chromium :

	sudo apt-get install chromium-browser
	sudo apt-get install ttf-mscorefonts-installer

## 6. Installing tv.js

    git clone https://github.com/SamyPesse/tv.js.git && cd tv.js
    make install

Installation will fail for yapp.js (because yapp.js currently need to be installed in global), so just install it with :

	sudo -i npm install -g git+https://github.com/FriendCode/yapp.js.git#master

## 7. Installing ffmpeg

TV.js needs ffmpeg for converting downloaded movies into streams. Simply install ffmpeg using :

	sudo apt-get install ffmpeg

Maybe a better way is to compile ffmpeg from source and make sure to have --enable-libtheora --enable-libvorbis as configure options.


## 8. Build and run tv.js

You can now build the TV.js client and run the server

    make build
    make run

Be sure that you are in the window environment, if not, enable the boot on the desktop in raspi-config

If you’re using SSH to connect to your RaspberryPi you should first add “DISPLAY=:0.0″ to your env variables, by simply executing

	export DISPLAY=:0.0	
	
You can hide the mouse in the window environment using: unclutter

	sudo apt-get install unclutter	
	unclutter -display :0 -noevents -grab

Start chromium in kiosk mode :

    chromium --kiosk http://localhost:8888

## 9. Start TV.js at boot :

Add the start script to init.d :

	sudo cp raspberrypi/tvjs.sh /etc/init.d
	sudo chmod 755 /etc/init.d/tvjs.sh
	sudo update-rc.d tvjs.sh defaults

Test the script :
	
	sudo /etc/init.d/tvjs.sh start


## 10. Update TV.js

If you want the latest version of TV.js to run on you Raspberry Pi, just run :

	sudo /etc/init.d/tvjs.sh update
	

