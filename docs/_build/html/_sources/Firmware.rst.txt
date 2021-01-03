
.. _Firmware:

=======================
Firmware/Programming
=======================

.. hint::
	You can always upload the latest code to your board without compiling - the pre-compiled bin files can be uploaded via USB or OTA (Over The Air)

.. warning::
	Modifications to the base firmware are encouraged, but be careful that you fully understand the existing base firmware functionality.  The firmware is responsible for both wake and shutdown functions.

Base Firmware Repository
-------------------------

Latest code can always be found in the `Github Repository <https://github.com/krdarrah/trigBoardV8_BaseFirmware>`_

Boards are always shipped with the release code which can be found at the `releases git page <https://github.com/krdarrah/trigBoardV8_BaseFirmware/releases>`_

.. note::
	| *For Compiling or Modifying Code*
	| **Latest Versions of IDE and Libraries Used**
	| Arduino IDE v1.8.10
	| ESP32 v1.0.4  - in boards manager, after this boards manager url in preferences https://dl.espressif.com/dl/package_esp32_index.json
	| PubSubClient Library v2.7.0
	| Arduino Json Library v6.13.0 
	| PushSafer Library Forked `HERE <https://github.com/krdarrah/pushsafer-arduino-library>`_ Thanks to `Brian Lough <https://github.com/witnessmenow>`_

	| **Board Settings From Tools Menu**\

	.. image:: images/boardsettings.png
		:align: center

.. warning::
	| *For USB Programming*
	| I recommend using the KD Circuits Designed `FTDI USB-Serial Converter <https://www.tindie.com/products/13817>`_ 
	|	**Jumpers set to 3.3V and OFF**
	
	| **You need to power the board from the battery connector NOT the USB-Serial Converter**
	|	Don't worry though the power pin on the programming header is not connected.  5V logic level on the programming header CAN damage the ESP32 though.  
	| **For development purposes, you can power the board by the 3.3V pin directly by applying 3.3V volts**
	|  Make sure to not have anything connected to the battery input and also note that the board will not sleep now.  Some people like this for testing code out and it makes it easier to upload, since you don't have to wake up before uploading.  


Uploading
-------------------------

Here's a pretty long video showing how it can be done both ways with a USB-Serial converter or OTA, but this is with the command line:

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/jIIYrOe25S0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

**OTA "Over The Air"**
	* To make this far easier, I made a simple app that runs on windows/mac/linux, so all you have to do is connect the trigBoard to your network from the configurator, initialize OTA, then you can setup the app and upload the latest BIN file:
	
	.. raw:: html

		<div style="text-align: center; margin-bottom: 2em;">
			<iframe width="560" height="315" src="https://www.youtube.com/embed/iMPBpUdL-rk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
		</div>


	* The `GUI can be downloaded here <https://github.com/krdarrah/ESP32_GUI_Programmer>`_ - you can download the entire zip, then just run the application for your operating system. You can download the latest BIN file from the `Github Repository for the V8 Base Firmware <https://github.com/krdarrah/trigBoardV8_BaseFirmware>`_ inside the OTA folder

	.. warning::
		When using the GUI, you might get a pop-up about installing an old version of Java - just make sure to install the latest version and it should work


Custom Firmware
-------------------------

The trigBoard uses the popular ESP32 WiFi+Bluetooth module, which is great due to the extensive resources and support for this platform.  The base firmware can be used as-is, but many users prefer to connect to their own service or do other unique things, while enjoying the ultra low power capability with the trigBoard.  This is easy enough though, but is suggested to modify the base firmware and utilize the "low power engine".  Let's review the :code:`void setup()` function to see how custom code can be embedded (just note this may be slightly outdated, but all still applies)

.. code-block:: C

	  void setup() {
		  pinMode(ESPlatchPin, OUTPUT);
		  digitalWrite(ESPlatchPin, HIGH);
		  pinMode(LEDpin, OUTPUT);
		  Serial.begin(115200);
		  checkWakeupPins();
		  loadConfiguration(filename, config);
		  rtcInit(config.timerCountDown, false);
		  Serial.println(getBattery(), 2);
		  if (pushLogic()) { //decide if push will occur or nt and what message will be
		    if (wiFiNeeded) {
		      if (connectWiFi()) {
		        pushOver();
		        pushSafer();
		        ifttt();
		        mqtt();
		      }
		    }
		    udp();
		    tcp();
		  }
		  killPower();
		  waitForButton();
		  initBluetooth();
	}

In the loop, you'll notice that not much happens.  This is because the trigBoard is designed to only run the code in the setup, then go to sleep.  If it ends up in the loop, means that the user held the wake button and it's handling the bluetooth connection or an OTA (OVER THE AIR) firmware update.  Because Pushover, Pushsafer, ifttt, etc are not used then you can just delete all of this out.  Your setup may look like this then:

.. code-block:: C

	  void setup() {
		  pinMode(ESPlatchPin, OUTPUT);
		  digitalWrite(ESPlatchPin, HIGH);
		  pinMode(LEDpin, OUTPUT);
		  Serial.begin(115200);
		  checkWakeupPins();
		  loadConfiguration(filename, config);
		  rtcInit(config.timerCountDown, false);
		  Serial.println(getBattery(), 2);
		  if (pushLogic()) { //decide if push will occur or nt and what message will be

		  //*** CUSTOM CODE GOES HERE **

		  }
		  killPower();
		  waitForButton();
		  initBluetooth();
	}

This code here still works with the configurator, which is important since that's how the wake events are setup. But how to use the wake message in the custom code? The message used to send push notifications is simply :code:`pushMessage` which is just a character array. The trigBoard name set in the Configurator is :code:`config.trigName` - also a char array.  You experiment with this and Serial.print these out to the monitor so you can see how the message can be formatted.

Something else that is extremely useful is using the unused Configurator settings for other things - for example, since now you're not using Pushover, Pushsafer, etc... you can enable those in the Configurator and set values there too be used in your custom code.  Like if you need to add an API key to your custom code, just use the Pushover API key :code:`config.pushAPIKey`.

