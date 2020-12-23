
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





