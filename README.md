# Pi3_as_Beacon_Reader
Raspbberry Pi3 based Beacon Reader 

##Hardware Requirements
Most likely you'll already have some of these components

* Ctrl+S / Cmd+S to s
* Raspberry Pi3 Model B
* 8gb SD with NOOBS 1.4 already loaded
* Beacons (Any Brand) / or you can use "[BLE Beacon Simulator](https://play.google.com/store/apps/details?id=net.alea.beaconsimulator&hl=en)" Android App 


##Method 1 (Based on HCITool) :+1:
###Scan For Bluetooth Enabled iBeacons Via A Raspberry Pi IoT Device

Update the package index and Install the newest versions of all of the packages

		sudo apt-get update
		sudo apt-get update
		sudo apt-get upgrade -y
		sudo apt-get dist-upgrade -y
		sudo rpi-update

###Installing the Software

		sudo apt-get install pi-bluetooth

####As the chip requires a firmware blob to work along with the driver. Bluez also installs a suite of tools.		

		sudo apt-get install bluez bluez-firmware

		sudo apt-get install blueman
		
####Bluez comes with a tool called 'bluetoothctl'
		
		pi@raspberrypi:~ $ bluetoothctl
		[bluetooth]# help
		Available commands:
		list                       List available controllers
		show [ctrl]                Controller information
		select <ctrl>              Select default controller
		devices                    List available devices
		paired-devices             List paired devices
		power <on/off>             Set controller power
		pairable <on/off>          Set controller pairable mode
		discoverable <on/off>      Set controller discoverable mode
		agent <on/off/capability>  Enable/disable agent with given capability
		default-agent              Set agent as the default one
		scan <on/off>              Scan for devices
		info <dev>                 Device information
		pair <dev>                 Pair with device
		trust <dev>                Trust device

[BlueZ](http://www.bluez.org/) is a very popular Linux bluetooth stack and it includes tools like hcitool and hcidump.  The goal with these tools is to scan for LE data in the background and dump the data into a script.
		
Assuming your Raspberry Pi is already up and running with Raspbian, execute the following:
		
		sudo apt-get install bluez bluez-hcidump
		
With the appropriate tools installed, iBeacons can be scanned and dumped like the following:
		
		sudo hcitool lescan --duplicates &
With the --duplicates setting the scan will not ignore multiple packets from the same iBeacon.
		
		sudo hcidump --raw
		
The above commands are only a part of the puzzle.  The data dumped by hcidump isn’t anything nice to work with.

Instead, we can take a look at a very popular parsing tool released by [Radius Networks](http://developer.radiusnetworks.com/ibeacon/idk/ibeacon_scan).

		sudo ./ibeacon_scan
		

##Scanning BLE adverts from Linux
	
		sudo hcidump -R							// "-R" to show the raw data
		sudo hcidump -x							// "-x" for hex dumping
		sudo hcidump -R > out 2>&1				// saving raw data into file
		

## Method 2 (Using BlueZ Library)

#Installing blueZ on the Raspberry Pi (Pre Jessie – and Pre Raspberry Pi 3)
	
	sudo apt-get update
	sudo apt-get upgrade

	sudo apt-get install libusb-dev 
	sudo apt-get install libdbus-1-dev 
	sudo apt-get install libglib2.0-dev --fix-missing
	sudo apt-get install libudev-dev 
	sudo apt-get install libical-dev
	sudo apt-get install libreadline-dev
	sudo apt-get install libdbus-glib-1-dev

	sudo mkdir bluez
	cd bluez
	sudo wget www.kernel.org/pub/linux/bluetooth/bluez-5.19.tar.gz
	sudo gunzip bluez-5.19.tar.gz
	sudo tar xvf bluez-5.19.tar
	cd bluez-5.19
	sudo ./configure --disable-systemd
	sudo make
	sudo make install

	sudo apt-get install python-bluez
	sudo shutdown -r now
Now you have bluez installed and running on your Raspberry Pi.  Next install your USB Bluetooth 4.0 Dongle and start the check out.
	
	lsusb

Note your USB Bluetooth dongle should show up something like this depending on what you have plugged into your USB bus.  You can see a lot more information about the USB device by typing:
	
	sudo lsusb -v -d 0a5c:
	
Now you can look for the Bluetooth device using hciconfig:

	hciconfig

Finally, turn on the device:

	sudo hciconfig hci0 up

And now run the blescanner command to see what iBeacons might be around you.  If you don’t have an iBeacon, you can simulate on with your either iPhone or Android phone with any of a number of apps on the Appstores.

	sudo python testblescan.py
		ble thread started
		----------
		cf:68:cc:c7:33:10,b9407f30f5f8466eaff925556b57fe6d,13072,52423,-74,-78
		cf:68:cc:c7:33:10,74696d6f74650e160a181033c7cc68cf,46608,13255,-52,-77
		da:f4:2e:a0:70:b1,b9407f30f5f8466eaff925556b57fe6d,28849,11936,-74,-79
		da:f4:2e:a0:70:b1,74696d6f74650e160a18b170a02ef4da,46769,28832,46,-78

The content of each line above is:

example: 	
	cf:68:cc:c7:33:10, b9407f30f5f8466eaff925556b57fe6d, 13072, 52423, -74, -78
	Beacon MAC Address, iBeacon UDID, iBeacon Major Number, iBeacon Minor Number, TX Power at 1m, RSSI

##Conclusion
With the price of iBeacons and Raspberry Pi devices being so low, it makes developing with the two more enjoyable.  To scan for iBeacon devices with a Raspberry Pi, you need the BlueZ tool set and the convenient Radius Networks command line script.  There are many other ways to scan for proximity beacons on a Raspberry Pi, for example Python, but that is a story best saved for another day.
