===============================
Cellular/Battery Backed System
===============================

.. image:: images/cellularBlockDiag.png
	:align: center


This is actually the preferred method for using the trigBoard - a complete low power system that relies on no existing WiFi/Internet and can even get push notifications out when there is a power outage!

* A central Gateway is installed in the system, where an ESP32 is configured to be a WiFi Access point.  By default, the SSID is hidden for security purposes.  This also means this is a private network hosted in the trigBoard system, meaning any existing WiFi is not relied upon.

* trigBoard communication to the gateway is done using UDP messaging.  This is very fast, and since no cloud communication is needed, the trigBoard message can be sent out very quickly and it can go back to sleep faster (saves battery life). This can be setup from the configurator with the base firmware.

* A `Particle Boron <https://store.particle.io/collections/cellular/products/boron-lte>`_ is also on the gateway to provide cellular connectivity to send push notifications without the need for existing WiFi/Internet connection.  At the time of writing this, the data plan from particle is $3/mo

* Local monitors in the system can play mp3 files based on what even took place.  Like if the Front Door is opened, these monitors will immediately play a custom mp3 "The Front Door has Opened"  These work on the same private WiFi network hosted by the gateway - when a message is received by a trigBoard sensor, it is immediately forwarded out to monitors.

* Battery Backed! Obviously all trigBoard sensors are all on batteries, but the gateway is battery backed by the on-board Particle Boron battery connection. The monitors are also battery backed with the use of Adafruit's ESP32 Feather board having on-board battery connection and USB charging.  

* KD Circuits has designed simple carrier boards for gateway and monitor boards - just bare PCB here, but easy to solder together with all through hole components.  $2/pc `contact KD Circuits directly <https://www.kdcircuits.com#contact>`_

trigBoard Setup
----------------

This is actually the easiest part! The messages are sent out via UDP, so setup the trigBoard as you normally would, but just enable udp from the configurator.  Timer settings, open/closed, etc... are all however you want them to work.

.. note::
	| Three things need to be decided
	| 1-SSID will be whatever you want - pick something random like "ajhagwiuycgwerilucberw" just don't use special characters
	| 2-Password also something random- AT LEAST 8 characters and nothing special
	| 3-Port for the UDP messaging, I just use 1234 
	| **Write these down somewhere, we'll need them for setting everything up**

| Here is my setup:

.. image:: images/udpSetup_cell.png
	:align: center

* **SSID** Custom SSID to be used for this private network.  This is only used by trigBoards in this system, so do not use your existing WiFi credentials here.  Make something up that is totally random.  try to use just letters and numbers, like "hgkjgKJGH687G" at least 8 characters.  Keep this information somewhere secure, since we'll need to set everything else up and other trigBoards in the future.

* **Password** Same as SSID, something random, at least 8 characters like "jklhoilhJLKHHj8907h"     

* **Static IP** You can set this to 192.168.4.102 if you'd like, or give each of your trigBoards something like 192.168.4.102, 192.168.4.103, 192.168.4.104  Just make sure to start at .102 and you could even give each trigBoard the same ip address.  The only risk there is if two trigBoards are triggered at the same exact time and both have the same static IP address.

* **Target IP** Must be  192.168.4.1 - this is the ip Address of the gateway where the messages are all sent

* **Target Port** You can set this to anything you want, but I normally set to 1234

* **Subnet** Set to 255.255.0.0

* **Primary DNS** Set to 8.8.8.8

* **Secondary DNS** Set to 8.8.4.4

* **Blast Count** Here is where you can get creative - UDP is a one way kind of message service, so how do we know the message got through? Well we don't and would take too much time to acknowledge back and forth, so best to just send a blast of messages!  I recommend 10-25 messages for this.  The battery calculations were done with a 20 message blast.

* **Time Between Blasts** This is the time between each message as they're sent out.  10 has worked pretty good for all of my testing.

| **How to set which mp3 to play on which event**
|	If using a Monitor to play mp3 files, setting this is done on each trigBoard through the configurator.  Very Easy! Each file on the SD card starts with a number, so these numbers are added in to the messages you set in the configurator: 

.. image:: images/mp3message.png
	:align: center

So you can see -5- or -7- or -2- is appended to the messages. If you don't want a certain message to be played by the monitor, just don't add the number


Gateway Setup
----------------
.. image:: images/gatewayPic.JPG
	:align: center

**********************
Hardware Needed
**********************
|	 * trigBoard is ideal for the Private network side, but you can adapt this to any ESP32 board
|	 * `Particle Boron <https://store.particle.io/collections/cellular/products/boron-lte>`_
|	 * A good sized Lithium battery - you can see above that use a giant 5000mAh battery.  The bigger the better so that if you loose power, it will run for a long time.  **The Battery plugs into the Particle Boron** which supports the Adafruit Feather form factor, so any of the batteries `they sell <https://www.adafruit.com/category/574>`_ will work - always double check polarity though!! 
|	 * You can make life a lot easier if you purchase a gateway carrier board from KD Circuits - then just some spare headers are needed to solder down the trigBoard (see picture above)

| **This is the PCB Design for reference:**

	.. image:: images/BoronCarrier.png
		:align: center

.. note::
	Expansion pins from the trigBoard are brought out specifically for adding lights/alarms/etc... or even a "silence button/switch"

.. warning::
	Do not connect anything to the trigBoard's battery or sensor connectors! The battery backup is provided by the Particle Boron and continuous power is through the Boron's micro USB port (just like the picture above)

**********************
trigBoard Software 
**********************

latest code can be downloaded in the `Gateway Git Repository <https://github.com/krdarrah/trigBoard_GatewayV8>`_

.. note::
	* This is all based on the Base Firmware, so make sure you have all of those dependencies and versions of libraries installed first.

	* I use the same board settings as well

	* **Software Serial** is used to communicate to the Particle Boron, so for the ESP32, I had to install EspSoftwareSerial first before compiling:

	.. image:: images/EspSoftwareSerialImg.png
		:align: center
		
	* USB-Serial Programming is recommended

Configuration of the trigBoard settings is also done through the configurator! On bootup, you'll notice the Blue LED flashing - it will do this for about 5 minutes, allowing you to connect to it through the google chrome `Configurator Tool <https://kevindarrah.com/configurator/>`_  You should see trigBoard Gateway now in the scan list. 
	
	Because this is a Gateway acting as the Access Point, you will not be connecting to any SSID, instead you will specify the SSID and password for this private network.  Most of the functionality in the configurator - you set this here in WiFi SSID and Password - then click "Save and Connect..."

		.. image:: images/gatewayWiFiSet.png
			:align: center   

	Then the only other setting is when you enable UDP, you can set the port number. **ALL OTHER SETTINGS DO NOTHING**

.. note::
	After saving the WiFi Settings for the gateway, **YOU HAVE TO PRESS THE RESET BUTTON ON THE TRIGBOARD**  This is because, the settings won't take effect until the board boots up.  You can test things at this point with the USB-Serial Converter and look at the Serial Monitor debug window.  When a trigBoard sends a message to it, you should see some activity!

**********************
Particle Software 
**********************

















