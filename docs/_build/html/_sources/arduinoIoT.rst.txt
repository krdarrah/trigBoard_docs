.. _ArduinoIot:

=========================================
Arduino IoT Cloud - Temperature Monitor
=========================================

.. image:: images/arduinoIotDashboard.png
	:align: center

Looking for a super easy and simple cloud service platform?  This might be a good fit for you!  There is also a free version to get you started, so that's a nice perk as well.

I was also happy to see they have a mobile app you can use to view your dashboards: 

.. image:: images/mobilearduinocloud.png
	:align: center


.. note::
	You can setup very easily with this using any ESP32 board, but this tutorial is specific to using the trigBoard and a temperature sensor.  Hopefully, this tutorial will show you how to set this up with any kind of sensor with the trigBoard (or no sensor at all - just contact status)

**Wiring**
-------------

You can use this without 

The wiring is simple - just need an `MCP9808 Breakout Board from Adafruit <https://www.adafruit.com/product/1782>`_ which is a very accurate, fast, and reliable temperature sensor.  Can also get these boards on Amazon for much cheaper.

.. image:: images/MCP9808wiringwithtrigboard.png
	:align: center

.. image:: images/MCP9808Installed.png
	:align: center

Or even cleaner: 

.. image:: images/mcp9808mountedtoshield.png
	:align: center

**Arduino IoT Cloud Setup**
---------------------------------

First thing to do is login to your account at `Arduino IoT Cloud <https://create.arduino.cc/iot/>`_

Then let's just get started and create a device - will be a third party board: 

.. image:: images/thirdpartyboardarduino.png
	:align: center

Will be ESP32, and select Dev Module: 

.. image:: images/devmoduleselectionArduino.png
	:align: center

Give the board a name!

.. image:: images/giveanamearduino.png
	:align: center

**Important** save the Device ID and Secret Key - we'll need these later

.. image:: images/deviceidsecretarduino.png
	:align: center

Now we see the device is created and can create a thing with variables:

.. image:: images/createthingsarduino.png
	:align: center

Then we create variables: 

.. image:: images/createvariablesarduino.png
	:align: center

For example, and as you'll see in the code, these variable names need to line up with the actual variable used in the trigBoard code: 

.. image:: images/variablesetupArduinoCloud.png
	:align: center

The example code uses three variables - pay attention to type (float, bool, etc) and since we're just sending these up to the cloud, are read only and the periodic time doesn't matter.

.. image:: images/threevariablesarduinocloud.png
	:align: center

You have a Device ID, Secret Key, and now save the Thing ID at the bottom of this page - **You need all three**

.. image:: images/thingIDarduinocloud.png
	:align: center

From there, it's all pretty easy to create dashboards to visualize the data.  In the future, we'll look at how to pull this data out of the Arduino Cloud to automate things, like turn something on/off on a threshold.  

**trigBoard Setup**
---------------------------------

`The github repo is here <https://github.com/krdarrah/trigBoardV8_BaseFirmware_ArduinoIoT>`_

To compile this code as-is, you will need the MCP9808 library from Adafruit as well as the supporting libraries for Arduino IoT Cloud.  You can install all of these libraries from the Arduino IDE, you'll find it: sketch>>Include Library>>Manage Libraries  Note, you are still compiling the base firmware, so go to the :ref:`Firmware Page <Firmware>` for what is needed to do that

What's really cool about this code is that it re-uses other fields in the trigBoard configurator to set this up to work with Arduino IoT Cloud, so no need to hard code things. 

+------------------------------------+---------------------------+
|Configurator                        |Arduino IoT Cloud          |
+====================================+===========================+
|Pushover Key                        |Thing ID                   |
+------------------------------------+---------------------------+
|Pushover API Token/Key              |Device ID                  |
+------------------------------------+---------------------------+
|Push Safer Key                      |Secret Key                 |
+------------------------------------+---------------------------+
|Message when Wake Button Pressed    |C or F                     | 
+------------------------------------+---------------------------+

So then when you launch the configurator, you setup your WiFi credentials, the interval time in the timer settings, then the Arduino IoT Cloud parameters.

Taking a look at the code, you'll see how easy it is to modify variables - head over to the thingProperties.h tab: 

.. code-block:: C

	void initProperties() {

	  ArduinoCloud.setBoardId(config.pushAPIKey);
	  ArduinoCloud.setSecretDeviceKey(config.pushSaferKey);
	  ArduinoCloud.setThingId(config.pushUserKey);
	  ArduinoCloud.addProperty(arduinoTemperature, READ, 1 * SECONDS, NULL);
	  ArduinoCloud.addProperty(arduinoBatteryVoltage, READ, 1 * SECONDS, NULL);
	  ArduinoCloud.addProperty(arduinoContact, READ, 1 * SECONDS, NULL);

When you scroll down there, you can see how the ArduinoCloud properties are added.  These variables are declared as globals at the top of this file:

.. code-block:: C

	float arduinoTemperature;
	float arduinoBatteryVoltage;
	boolean arduinoContact;

From there, these variables are stuffed with the data we want to send up to the cloud: 

Head back over to the main tab, and you'll see how this is done: 

.. code-block:: C

	  arduinoTemperature = atof(temperatureString);
	  arduinoBatteryVoltage = getBattery();
	  arduinoContact = contactStatusClosed;//arduinoContact=true when contact closed
	  initProperties();//ArduinoIoTCloud
	  sendArduinoIoT();