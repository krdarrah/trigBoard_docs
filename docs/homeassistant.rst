.. _HomeAssistant:

=============================
Home Assistant
=============================

.. image:: images/homeassistlogo.png
	:align: center

.. image:: images/demoDash.png
	:align: center

**trigBoard can send to Home Assistant out of the box!**  If you're already familiar with Home Assistant, then you already know how powerful this is and will be very happy to find that you can integrate the trigBoard in just a few steps.  This takes the trigBoard to a new level, because now you can tie this in to alerts, automations, monitoring, etc... It's also very easy to add sensors to the trigBoard and provide that additional data to your system.  

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/Fs0_ihgrvKA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

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

**Testing**
------------------

Let's make sure you see the trigBoard - Developer Tools>>**STATES** and look for your newly created entity:

.. image:: images/sensorfoundSatethass.png
	:align: center


If you look at its state while opening/closing the contact, you should see the normal trigBoard message. 

**Parsing**
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

**Final Config**
-----------------

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


**Plotting with Grafana+InfluxDB**
=========================================

This takes plotting your data to the next level - just look at this dashboard I created: 

.. image:: images/dashboardshowinggrafana.png
	:align: center

**Install InfluxDB**
---------------------

Same as before: Supervisor>>Add-on Store, then search for InfluxDB  Go ahead and install/start that: 

.. image:: images/influxdbaddon.png
	:align: center

Once started, go into InfluxDB and click the InfluxDB Admin icon: 

.. image:: images/influxadmindbicon.png
	:align: center

Create a new database and call it homeassistant 

.. image:: images/influxdbhomeassistant.png
	:align: center

I changed my duration to 7d

.. image:: images/influxduration7d.png
	:align: center

Click on Users and create a new user called homeassistant and choose a password: 

.. image:: images/homeassistantuserinfluxdb.png
	:align: center

Don't forget to change the permissions to ALL

.. image:: images/homeassistantuserpermissons.png
	:align: center

While we're in here, add a user for Grafana as well and also give ALL permissions: 

.. image:: images/addgrafanauser.png
	:align: center

Go back to Supervisor and select InfluxDB and click the Documentation tab - scroll down a bit to the Yaml snippet - COPY this: 

.. image:: images/homeinfluxdbyamlcopysni.png
	:align: center

Head back over to your file editor to modify your configuration.yaml file and paste this in there, but note that the database name, user, and password all match up with what we have created in the InfluxDB admin page: 

.. image:: images/influxpastedyamlconfig.png
	:align: center

Finally, give your Home Assistant a restart: Configuration>>Server Controls and click RESTART

.. image:: images/restartinfluxdb.png
	:align: center

**Install Grafana**
---------------------

Supervisor>>Add-on Store, then search for Grafana  Go ahead and install/start that: 

.. image:: images/grafanainstalladdaon.png
	:align: center

Once started go into the Documentation tab - and COPY this URL: 

.. image:: images/grafanalinkdocumenattioon.png
	:align: center

Go to the configuration tab and make sure SSL is false: 

.. image:: images/grafanaconfiguration.png
	:align: center

Now open up Grafana and click the configuration icon and go into the Data Sources: 

.. image:: images/grafanadatasourcesicon.png
	:align: center

Add a data source: 

.. image:: images/adddatasourcegrafana.png
	:align: center

Search for InfluxDB and select that: 

.. image:: images/influxdbdaatasource.png
	:align: center

That URL we copied can be pasted here now: 

.. image:: images/urlintografana.png
	:align: center

Enter in the homeassistant InfluxDB, then the user and password is the one we created for Grafana - Save & Test and you should see this: 

.. image:: images/saveandtestgrafana.png
	:align: center

**Create a Dashboard**
-----------------------

In Grafana, go and create a new dashboard, then add an empty panel: 

.. image:: images/grafanacreateanewdash.png
	:align: center

.. image:: images/grafanaaddpaneldash.png
	:align: center

Here we have a query where we can select anything we want to plot or even just display in text.  I use it to create a beautiful looking dashboards.

If we wanted to pull in and plot the battery voltage from a trigBoard, we can run a query like this: 

.. image:: images/zoominonimagequery.png
	:align: center

Then looks like this: 

.. image:: images/trigBoardvoltagequeryy.png
	:align: center

Then if you save this dashboard, you can share, then copy the link out: 

.. image:: images/sharegrafanalink.png
	:align: center

.. image:: images/copygrafanalink.png
	:align: center

In your Home Assistant Dashboard, you can now create a webpage card and paste that URL in: 

.. image:: images/homeassistantwebpage.png
	:align: center

Now you have Grafana plots in your Dashboard!

Note that you can modify that URL to include the time frame and I recommend enabling kiosk mode, so only the plot is shown - here's an example:

.. code-block:: YAML

	http://homeassistant.local:8123/api/hassio_ingress/lT8ixi8MuSreoBQVVB9bjFyRyfmXki0Z5rnit7RGHWo/d/xc8gaT9Mk/trigsolartest?orgId=1&refresh=30s&kiosk&from=now-24h&viewPanel=2

See the kiosk label in there, the auto refresh time at 30sec, the time frame is now-24hr to give last 24hours

Here's a query I needed for an **ESPhome** device to get its state: 

.. image:: images/esphomstatequery.png
	:align: center


**trigBoard activated WiFI Light Bulb**
=========================================

Want to do something useful with the trigBoard?  In my house here, when I open the door into the garage, wouldn't be handy to turn a light on automatically?  Well this is a perfect application for trigBoard + Home Assistant.  Even better, this trigBoard can still send push notifications out using any of the other services - you're just enabling MQTT as a next step in the sequence in the logic for when the trigBoard wakes.  You'll see below how this can be done.  

**WiFi Bulb Setup**
-----------------------

I didn't want to have to deal with re-flashing a cheap WiFI bulb or worry about a gateway with ZigBee bulbs, so I decided on a TP-Link "Kasa" KL110 bulb `picked up on Amazon <https://www.amazon.com/gp/product/B07G1PH3JL/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1>`_

.. image:: images/kasaamazonpagetplinkbulb.png
	:align: center
	:target: https://www.amazon.com/gp/product/B07G1PH3JL/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1

| Run through the normal setup as given by the manufacturer:
| 1 - Screw the bulb into a powered outlet 
| 2 - Download the Kasa App - no need to setup an account (I skipped this step)
| 3 - Top right "+" button to add device - give it permissions, choose smart bulb, KL100 series
| 4 - Go to WiFI settings on your phone and connect to the "TP-LINK..." bulb network
| 5 - Back to the Kasa app, click through the screens, connect the bulb to same network your Home Assistant is on, then will eventually get you to a connected screen to let you name the bulb
| 6 - **This name is important** we'll need it later when setting up in Home Assistant
| 7 - Just make sure you can turn the bulb on and off from the app - now we're done with the app

.. image:: images/ioslaasaappbulb.png
	:align: center


Add TP-Link Integration to Home Assistant: Configuration>>Integrations, then bottom right click Add Integration, search for TP-Link

.. image:: images/tplinkintegrationsearch.png
	:align: center

I clicked the three dots in the integration, system options, then update: 

.. image:: images/kasaintegrationupdate.png
	:align: center

In File Editor, we add the tplink device with the bulb name we had in the app: 

.. code-block:: YAML

	tplink:
	  discovery: false
	  light:
	    - host: GarageLightBulb

Mine looks like this: 

.. image:: images/tplinkkasaconfigyaml.png
	:align: center

After saving that, I gave my Home Assistant a restart: Configuration>>Server Controls, then click Restart under Server management.  

Once things are back online, try creating a new Bulb card in a dashboard and see if you can select that Entity: 

.. image:: images/bulbcardtesttplinkn.png
	:align: center

You should be able to turn the bulb on and off and adjust brightness.  **IMPORTANT** set the desired brightness you want for the bulb here, then just turn it off.  From the trigBoard, we're just going to toggle it on/off.  

Then that's all we need to do with the Bulb!

**trigBoard Setup**
-----------------------

.. note::
	If you've followed the guides above and already have a trigBoard displaying a state change, then you're good to go and can proceed to the next step, but this will show how to setup a trigBoard that is already in service and if you only want to expand it's features to now activate a light with Home Assistant

In this case, I have a trigBoard already sending push notifications per :ref:`The Cellular Backed Setup using TCP <CellularBattery>` so I really didn't want to modify any of my push messages or other parameters.  This is no problem and easy enough to just enable MQTT.  

| 1 - Launch the Configurator
| 2 - Set your WiFi Settings, then Save and Connect
| 3 - In my case, all of my parameters look like this, since this board is already in service: 

.. image:: images/sidedoorexistingsettingsforkasabulb.png
	:align: center

| 4 - Then just enable MQTT for Home Assistant and add your username/pw you use to login, IP address for the Server (see "Home Assistant Setup" above to find this) and also give a unique Topic and take note of this: 

.. image:: images/mqttforgaragetplinkkasa.png
	:align: center

| 5 - That's it for the Configurator, you can now disconnect


Back to Home Assistant straight into the File Editor, you can add this sensor and note the value template - we're just going to take the full trigBoard message out here without the voltage (we don't care about that for what we're doing).  Note the topic here matches what was entered in the Configurator:

.. code-block:: YAML

	  - platform: mqtt
	    state_topic: "garageDoor"
	    name: "Garage Door"
	    icon: mdi:door
	    value_template: "{{ value.split(',')[0] }}"

This will produce these messages: 

.. code-block:: YAML

	Side Door Opened-4-

	Side Door Closed-5-

Go to Configuration>>Server Controls and click on the button to reset the MQTT entries "MANUALLY CONFIGURED MQTT ENTITIES"

You can go and create an entities card just to test and make sure you're seeing the open/closed messages come through: 

.. image:: images/entitiescardfortplinktrig.png
	:align: center

**All done!!**

**Automation Setup**
-----------------------

You can get really creative here, but I'm boring and just want to turn that light bulb on for 5 minutes when the door opens... that's it!  And so easy with Home Assistant! 


Configuration>>Automations Then create a new Automation, and start with a empty automation: 

.. image:: images/startweithanemptyautomation.png
	:align: center

Give it a good name, then scroll down to triggers, and this is dead simple, we're going to trigger on that trigBoard state as it changes from closed to open.

.. image:: images/automationtriggerfromclosetoopen.png
	:align: center

Scroll down to Actions, and this is simple too, just turn that garage bulb on: 

.. image:: images/actionbulbon.png
	:align: center

So that turns the bulb on, but then to turn off after 5 minutes, I added a delay action for 5minutes, then another state action to turn the bulb off: 

.. image:: images/delayandoffactionsbulb.png
	:align: center

That's all there is to it! Don't forget to save, then should be good from there!



