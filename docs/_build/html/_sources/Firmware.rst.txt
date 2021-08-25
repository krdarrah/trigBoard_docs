
.. _Firmware:

===============================
Firmware Updates & Programming
===============================

Check Version
-------------------------

Every trigBoard shipped is pre-programmed with a release of the base firmware, but this is under heavy development, so it is a good idea to first check to make sure you have the latest version loaded.  Here's how to check that: 

- Launch the :ref:`Configurator Tool <Configurator>`, hold the wake button on the trigBoard until the LED is flashing, then connect to the board from the Configurator.  At the top of the page, you'll see the date code of the firmware: 

	.. image:: images/configfwversionof.png
		:align: center

- Head over to the `releases git page <https://github.com/krdarrah/trigBoardV8_BaseFirmware/releases>`_ and see if there is a newer date code:

	.. image:: images/gitreleaselatestdatecode.png
		:align: center

If a newer version exists, don't worry because it's very easy to upload - even wirelessy using OTA (OVER THE AIR) updates

Wireless Updates (OTA)
-------------------------

Here is a quick video showing the complete process to update the Firmware using OTA:

	.. raw:: html

		<div style="text-align: center; margin-bottom: 2em;">
			<iframe width="560" height="315" src="https://www.youtube.com/embed/QmJfK1wJ-ow" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
		</div>

.. hint::
	| Something went wrong? Here are some troubleshooting tips:
	| - Make sure the trigBoard is connected to the same network as the computer running the trigUpdater app
	| - If your FW is older, OTA was really not reliable, so just keep trying... it should eventually complete.  If not, you can check out uploading over USB.  
	| - Make sure the battery is fresh/fully charged

The latest bin file can always be downloaded from the `releases git page <https://github.com/krdarrah/trigBoardV8_BaseFirmware/releases>`_

The trigUpdater app for your OS can be downloaded `from here <https://github.com/krdarrah/trigUpdater/releases>`_

Alternate OTA Method
=====================

I also created an updater app with Python/Java, so here is that tutorial: 

	.. raw:: html

		<div style="text-align: center; margin-bottom: 2em;">
			<iframe width="560" height="315" src="https://www.youtube.com/embed/iMPBpUdL-rk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
		</div>


	* The `GUI can be downloaded here <https://github.com/krdarrah/ESP32_GUI_Programmer>`_ - you can download the entire zip, then just run the application for your operating system. You can download the latest BIN file from the `Github Repository for the V8 Base Firmware <https://github.com/krdarrah/trigBoardV8_BaseFirmware>`_ inside the OTA folder

	.. warning::
		When using the GUI, you might get a pop-up about installing an old version of Java - just make sure to install the latest version and it should work

USB Updates
-------------------------

This is a long video showing how it can be done, but also useful for other ESP32 projects: 

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/jIIYrOe25S0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

Compiling/Uploading Base Firmware
----------------------------------------

Latest code can always be found in the `Github Repository <https://github.com/krdarrah/trigBoardV8_BaseFirmware>`_

Boards are always shipped with the release code which can be found at the `releases git page <https://github.com/krdarrah/trigBoardV8_BaseFirmware/releases>`_

.. warning::
	Modifications to the base firmware are encouraged, but be careful that you fully understand the existing base firmware functionality.  The firmware is responsible for both wake and shutdown functions.

**Board Settings From Tools Menu**

.. image:: images/boardsettings.png
		:align: center

| **Versions Prior to Release 8/16/21**
| * Arduino IDE v1.8.10
| * ESP32 v1.0.4  - in boards manager, after this boards manager url in preferences https://dl.espressif.com/dl/package_esp32_index.json
| * PubSubClient Library v2.7.0
| * PushSafer Library Forked `HERE <https://github.com/krdarrah/pushsafer-arduino-library>`_ Thanks to `Brian Lough <https://github.com/witnessmenow>`_
| * Arduino Json Library v6.13.0

| **Versions with latest 8/16/21**
| * Arduino IDE v1.8.15
| * ESP32 v1.0.6  - in boards manager, after this boards manager url in preferences https://dl.espressif.com/dl/package_esp32_index.json
| * PubSubClient Library v2.8.0
| * Arduino Json Library v6.18.0
| * UniversalTelegramBot Library v1.3.0

.. warning::
	| *For USB Programming*
	| I recommend using the KD Circuits Designed `FTDI USB-Serial Converter <https://www.tindie.com/products/13817>`_ 
	|	**Jumpers set to 3.3V and OFF**
	
	| **You need to power the board from the battery connector NOT the USB-Serial Converter**
	|	Don't worry though the power pin on the programming header is not connected.  5V logic level on the programming header CAN damage the ESP32 though.  
	| **For development purposes, you can power the board by the 3.3V pin directly by applying 3.3V volts**
	|  Make sure to not have anything connected to the battery input and also note that the board will not sleep now.  Some people like this for testing code out and it makes it easier to upload, since you don't have to wake up before uploading.  

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

