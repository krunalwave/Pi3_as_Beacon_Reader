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

Instead, we can take a look at a very popular parsing tool released by [Radius Networks]http://developer.radiusnetworks.com/ibeacon/idk/ibeacon_scan).

		sudo ./ibeacon_scan
		

##Scanning BLE adverts from Linux
	
		sudo hcidump -R							// "-R" to show the raw data
		sudo hcidump -x							// "-x" for hex dumping
		sudo hcidump -R > out 2>&1			// saving raw data into file
		




##Conclusion
With the price of iBeacons and Raspberry Pi devices being so low, it makes developing with the two more enjoyable.  To scan for iBeacon devices with a Raspberry Pi, you need the BlueZ tool set and the convenient Radius Networks command line script.  There are many other ways to scan for proximity beacons on a Raspberry Pi, for example Python, but that is a story best saved for another day.
