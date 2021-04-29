.. _HomeAssistant:

=============================
Home Assistant
=============================

.. image:: images/homeassistlogo.png
	:align: center

.. image:: images/demoDash.png
	:align: center

**trigBoard can send to Home Assistant out of the box!**  If you're already familiar with Home Assistant, then you already know how powerful this is and will be very happy to find that you can integrate the trigBoard in just a few steps.  This takes the trigBoard to a new level, because now you can tie this in to alerts, automations, monitoring, etc... It's also very easy to add sensors to the trigBoard and provide that additional data to your system.  

**Home Assistant Setup**
=========================================

First step is to make sure you have Home Assistant Installed (if you don't already) - head over here and follow these steps: `Home Assistant Installation <https://www.home-assistant.io/installation/>`_

*Note - I am running Home Assistant on a Raspberry Pi 3 64bit*

Make sure you're in advanced mode: 

.. image:: images/advancedModehass.png
	:align: center

Take note of the IP address: 

.. image:: images/hassIPaddress.png
	:align: center

**MQTT Mosquitto Broker Add-On**
=========================================

Supervisor>>Add-on Store

.. image:: images/addonmosquite.png
	:align: center

Search for "mqtt"

.. image:: images/mqttsearch.png
	:align: center

Go and install, then start:

.. image:: images/mqttInsatll.png
	:align: center

.. image:: images/mqttstart.png
	:align: center

Then just for reference, here is my configuration tab and note the MQTT port we're going to use: 

.. image:: images/mqttconfigtab.png
	:align: center

Now go to Configuration>>Integrations>>Click Configure in the MQTT block>>Click Submit in the popup window

.. image:: images/MqttSubmitconfig.png
	:align: center


**trigBoard Setup**
=========================================

Launch the configurator as you normally would when setting up the trigBoard - hold wake button until Blue LED is flashing.  Can go here for reference on all parameters: :ref:`Configurator Tool <Configurator>`

Setup the WiFi network settings first and make sure you get a connection, then setup the messages:

The only requirement here for all of your messages is that they contain **no spaces or commas**, because we're going to use these characters for parsing on the Home Assistant side.  Also, I recommend setting the battery threshold low, so that it never sends the low battery alert.  Reason is because the low battery alert message would be a second packet for you to parse out... when really, you don't need this alert since you will already have the ability to use the battery voltage to generate alerts within the Home Assistant system.  If you have a trigBoard that rarely wakes and reports its status, but still want battery monitoring, you could set the timer to check if the contact is still open/closed and just set those messages to the same as your normal open and close messages like this: 

.. image:: images/trigmessagessetuphass.png
	:align: center

or could use STILL_OPEN/CLOSED and just setup automations to detect these - that works too!  

.. image:: images/stillopneclosedhass.png
	:align: center

**MQTT Settings**
--------------------

| 1- Enable MQTT
| 2- Enable Security
| 3- Enter in same credentials you use to login to Home Assistant
| 4- Default port is fine at 1883
| 5- Server is the IP Address of your Home Assistant Server - see above on where you can find this
| 6- **Important** The topic is the identifier for this trigBoard and will be unique for this board.  Choose something descriptive and take note of what you entered.  

.. image:: images/mqttsettingshass.png
	:align: center

**Home Assistant Yaml**
=========================================

We'll be using the File Editor to make changes to the configuration.yaml file, so go an install/start that just as shown above for Mosquitto 

.. image:: images/fileEditor.png
	:align: center

I like to keep this in the sidebar for quick access: 

.. image:: images/showinsidebar.png
	:align: center

Launch the file editor and make you are editing the configuration.yaml file:

.. image:: images/yamleditor.png
	:align: center

Here is where we're going to add the trigBoards (more advanced users may keep this config elsewhere)

Let's start by adding a sensor subscribed to that same topic we had set in the trigBoard Configurator: 

.. code-block:: YAML

	sensor:
	  - platform: mqtt
	    state_topic: "frontdoor"
	    name: "Front Door"
	    icon: mdi:door

Which looks like this when you paste it in: 

.. image:: images/pastedttedtYaml.png
	:align: center

Save that: 

.. image:: images/saveyoaml.png
	:align: center

Go to Configuration>>Server Controls>>Check Configuration

.. image:: images/checkconfiguration.png
	:align: center

If all good, you can scroll down a bit and click "MANUALLY CONFIGURED MQTT ENTRIES"

.. image:: images/refreshmqtthass.png
	:align: center

**TESTING**
------------------

Let's make sure you see the trigBoard - Developer Tools>>**STATES** and look for your newly created entity:

.. image:: images/sensorfoundSatethass.png
	:align: center


If you look at its state while opening/closing the contact, you should see the normal trigBoard message. 

**PARSING**
-----------------

Within the Developer Tools, go to the **TEMPLATE** tab: 

Here we can experiment with parsing, first just to look at the raw message: 

.. image:: images/rawmessageTaml.png
	:align: center

If we split by the space character: 

.. image:: images/splitSpcacetemp.png
	:align: center

Now we can select the part of the message we want: 

.. image:: images/firstpartbeforecommahass.png
	:align: center

And we can split again to get rid of that comma: 

.. image:: images/hassridofcomma.png
	:align: center

And we can the same thing to get the voltage and strip out the unit: 

.. image:: images/volatgeParsingTemp.png
	:align: center

With this knowledge, we can use a **value_template** in the YAML file to automatically pull this information out of the trigBoard message.  We need to create two sensors for both status and voltage.  Note that you can add a custom `MDI Icon <https://materialdesignicons.com/>`_ and give a unit: 

.. code-block:: YAML
		
	sensor:
	  - platform: mqtt
	    state_topic: "frontdoor"
	    name: "Front Door"
	    value_template: "{{ value.split(' ')[1].split(',')[0] }}"
	    icon: mdi:door
	  - platform: mqtt
	    state_topic: "frontdoor"
	    name: "Front Door Battery"
	    value_template: "{{ value.split(',')[1].split('V')[0] }}"
	    icon: mdi:battery
	    unit_of_measurement: "V"

Which looks like this in the file: 

.. image:: images/finalwithtaemphass.png
	:align: center

Don't forget to save and reload MQTT entries!

Now you're all set - head over to **Overview** and click the 3 dots in the upper right to edit your dashboard.  Go ahead an take control of the Lovelace UI, then create an **Entity** Card and you should be able to add in the trigBoard: 

.. image:: images/cardeditorUI.png
	:align: center

And now you have a trigBoard in your dashboard! 

.. image:: images/cardFinishedHAss.png
	:align: center

With these entities, you can go nuts with this!  Create conditional cards to show custom text when these states change, automations to run actions based on events (even have your Alexa/Google speak!), or really anything you want from here!

**Custom Sensors**
=========================================

Want to add a temperature sensor to the trigBoard? Maybe report status of something along with the message?  This is very **EASY**

First step is to make sure you can compile/upload the :ref:`Base Firmware <Firmware>`

The key is to just concat the data onto the existing message - here is a snippet from a sensor I have that samples a GPIO pin: 

.. image:: images/solarBoardHASSaddon.png
	:align: center

You see how it's just a comma separated value added to the end of the push message?  That's all you need, since this is super easy to then pull out from the value_template, like in this case I only needed battery voltage and this new value for state: 

.. code-block:: YAML

	sensor:
	    state_topic: "trigSolarTester"
	    name: "trigSolar Voltage"
	    icon: mdi:car-battery
	    unit_of_measurement: "V"
	    value_template: "{{ value.split(',')[1].split('V')[0] }}"
	    
	  - platform: mqtt
	    state_topic: "trigSolarTester"
	    name: "trigSolar State"
	    icon: mdi:solar-power
	    unit_of_measurement: "V"
	    value_template: "{{ value.split(',')[2] }}"

Or here's another example adding a temperature value: 

.. image:: images/mcptempsensorexample.png
	:align: center

And same as before, this sensor is on a timer and wakes every minute to send data - I only care about voltage and this new temperature value: 

.. code-block:: YAML

	sensor:
	  - platform: mqtt
	    state_topic: "MCP9808_OUTSIDE"
	    name: "Back Porch Temperature"
	    icon: mdi:temperature-fahrenheit
	    unit_of_measurement: "F"
	    value_template: "{{ value.split(',')[2] }}"

	  - platform: mqtt
	    state_topic: "MCP9808_OUTSIDE"
	    name: "Back Porch TempSensor Voltage"
	    icon: mdi:car-battery
	    unit_of_measurement: "V"
	    value_template: "{{ value.split(',')[1].split('V')[0] }}"

And here's what I'm doing with this data: 

.. image:: images/dashboardwithtempsaj.png
	:align: center


















