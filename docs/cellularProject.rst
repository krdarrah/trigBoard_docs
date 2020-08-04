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

| **Silent Button/Switch**
| This is completely optional, but the gateway code supports a silent button/switch to prevent messages from being sent out only to the monitors.  If you have these monitors installed throughout the house and you have a trigBoard mounted on a high-traffic location, the monitor can get pretty annoying!  Note that this code only silences the monitor, not the particle, so the push notifications still go out.  In the code, it's easy to see how this works though, so you can make this operate however you may need.  
| The switch is wired from GND to GPIO12 where CLOSED = SILENT

	.. image:: images/silentButton.png
		:align: center

***************************
trigBoard Gateway Software 
***************************

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

This part is very easy once you get your Particle Boron Commissioned - follow the particle tutorials to get all set up and running so that you see the breathing Cyan LED on the board.  It would be a good idea to at least get familiar with flashing code to the Boron from their `web IDE <https://build.particle.io/build/new>`_

Then all you'll do is paste the code in below, check you have the right board selected (bottom right of IDE), and flash the board by clicking the lightening bolt symbol (upper left of IDE)

.. code-block:: C
		
	String str1,str2;

	void setup() {
	    Serial.begin(115200);//debug
	    Serial1.begin(9600);//from trigBoard
	}

	void loop() {
	  if (Serial1.available() > 0) {// new data came in
	     Serial.println("New Data");
	     str1 = Serial1.readStringUntil(',');//that's the separator
	     str2 = Serial1.readStringUntil('#');
	     sendData();
	     Serial1.flush();
	  }
	}

	void sendData(){
	     unsigned long startConnectTime = millis();
	     char pushMessage[50], pushName[50];
	     str1.toCharArray(pushName, str1.length() + 1);
	     str2.toCharArray(pushMessage, str2.length() + 1);
	     Serial.println(str1);
	     Serial.println(str2);
	     
	     String adaFruitData = "[{\"key\":\"house\", \"value\":\"";
	     adaFruitData.concat(str1);
	     adaFruitData.concat("\"}]");
	     Particle.publish("homeSecurityPost", adaFruitData, PRIVATE, NO_ACK);
	     
	     String pushoverPacket = "[{\"key\":\"title\", \"value\":\"";
	     pushoverPacket.concat(str1);
	     pushoverPacket.concat("\"},");
	     pushoverPacket.concat("{\"key\":\"message\", \"value\":\"");
	     pushoverPacket.concat(str2);
	     pushoverPacket.concat("\"}]");
	     Particle.publish("pushover", pushoverPacket, PRIVATE);//then send to push over so we get the notifications on our mobile devices

	     Serial.print(millis() - startConnectTime);
	     Serial.println("ms to connect");
	}

This code receives data from the trigBoard then sends out to the cloud - both to Pushover and to AdafruitIO.  Why both? Well you may want to do other things with this data, so Adafruit can keep a log of all of the notifications and you can tie that to other things around the internet like IFTTT or even notify other trigBoard systems.  Like let's say you have a remote system setup and you want your local monitors to speak when something in that location occurs.  You can have one Master system configured to also monitor an Adafruit feed to push data back out from the gateway to the monitors. And Adafruit's service is free, so we can set that up now and expand the system later on.  Let's set these things up now: 

1) Set up and account at `io.adafruit.com <https://io.adafruit.com>`_

2) You'll see something in there where to get your Adafruit IO Key - we'll need this later for the webhook from Particle to send data here

3) Create a new feed and call it something - this is where all notification data is sent - in my code above, all data is sent to the feed named "house".  You can change this, but just make sure you also change in the code.

4) You probably already have this setup, but go and set up an account with `pushover.net <https://pushover.net>`_ - the push notifications will be sent here. We'll need both the user and API keys - :ref:`go here  <Pushover>` for instructions on where to get those

5) So now we're all setup to create the webhooks needed for the Particle Boron to send data out to Adafruit and Pushover.  Head over to `Integrations <https://console.particle.io/integrations>`_ and create a new one and select Webhook. The Event Name can be whatever you want, but note that my Boron Code above is calling "homeSecurityPost", so if you do change this, make sure to also change in the code. The URL is setup like this: https://io.adafruit.com/api/v2/krdarrah/groups/feeds/data 

See the "krdarrah" in there? that's where you'll put your adafruit user name, and also make sure your settings look like this:

.. image:: images/adafruitWebhook.png
	:align: center

Expand the advanced settings and set the custom JSON data to look like this: 

.. code-block:: JSON

	{
	  "feeds": [
	    {
	      "value": "{{{0.value}}}",
	      "key": "{{{0.key}}}"
	    }
	  ]
	}

Scroll down to the HTTP HEADERS and ADD ROW twice, so that you can add your Adafruit IO key and Host:

.. image:: images/adafruitHeaders.png
	:align: center

Everything else can be left alone, so you can save this and should be good to go with Adafruit IO.  Let's create the Pushover Webhook next, so follow that same process in creating a new webhook with the Event Name called "pushover", URL = https://api.pushover.net/1/messages.json and change the settings so it looks like this:  

.. image:: images/pushoverwebhook.png
	:align: center

Next, we'll go to the advanced settings and and add a couple rows and set this up like:

.. image:: images/pushoverForm.png
	:align: center

You'll notice where you paste in your user and API tokens from pushover.net.  You can also change the sound of the notification, but I've only ever used the bike sound, so not sure what options you have here.  

Everything else can be left alone, so save this and you now have both webhooks good to go! 

Monitor Setup
----------------

**********************
Hardware Needed
**********************
