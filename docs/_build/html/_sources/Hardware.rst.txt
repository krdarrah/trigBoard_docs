=======================
Hardware
=======================
+--------------------------------------+-----------------------------------------------------------------------------------+
| Special Hardware on the trigBoard                                                                                        |
+======================================+===================================================================================+
| .. image:: images/trigBoardFeat.png  | | **Ultra Low Power Real Time Clock RV-8263-C7**                                  |
|                                      | | * Can check if sensor contact is still open/closed after timeout                |
|                                      | | * Check battery voltage at some interval or even "phone home"                   |
|                                      | | * Read sensor data every hour or whatever and send to cloud or "fetched"        |
|                                      +-----------------------------------------------------------------------------------+
|                                      | | **3.3V Switched Power Supply**                                                  |
|                                      | | * Anytime the board is triggered, the 3.3V Supply is enabled                    |
|                                      | |   *This is how the sleep current is so low, power is completely removed*        |
|                                      +-----------------------------------------------------------------------------------+
|                                      | | **4.2V Always On Power Supply**                                                 |
|                                      | | * This is always enabled, to keep sensor circuit and RTC powered                |
|                                      | | * Boosted from battery voltage, so will always be 4.2V                          |
|                                      +-----------------------------------------------------------------------------------+
|                                      | | **Pulsed Normally Open/Closed Monitoring Circuit**                              |
|                                      | | * The secret sauce as to how the board monitors the sensor input                |
|                                      | | * No jumpers or anything, both Normally Open/Closed works with v8               |
|                                      +-----------------------------------------------------------------------------------+
|                                      | | **Auto Reset for easy programming from USB-Serial Converter 3.3V**              |
|                                      | | * Recommended is from KD Circuits!                                              |
|                                      +-----------------------------------------------------------------------------------+
|                                      | | **Battery Voltage Measurement**                                                 |
|                                      | | * Tested during factory programming and single point offset applied             |
+--------------------------------------+-----------------------------------------------------------------------------------+

+----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| Based on Espressif ESP32 WiFI+Bluetooth Module                                                                                                                         |
+========================================+===============================================================================================================================+
| .. image:: images/ESP32-WROOM.jpg      | | **ESP32-WROOM-32D 16MB Version**                                                                                            |
|                                        | | `Datasheet <https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32d_esp32-wroom-32u_datasheet_en.pdf>`_ |
|                                        | | Arduino Compatible with Core Installation - see firmware section                                                            |
|                                        | | Programmable with USB-Serial Converter (onboard autoreset)                                                                  |
|                                        | | x14 expansion pins broken out for sensors/switches/actuators/etc... - see pinout section                                    |
+----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+

Trigger Input
--------------

The positive pin is tied to the 4.2V Source boosted from the battery.  The Negative pin is the actual sensor input, which requires a rapid cross through ~3V in order to wake the trigBoard up. Typically just a switch of some kind is used across the sensor input like window/door sensors. You'll notice all of the interesting projects that use this input for more than just a dry contact input, but the interesting thing to note is this input requires this rapid cross through ~3V in order to trigger.  Sometimes "slow moving" inputs need to sped up - like in the case of the water detector, so in that case a MOSFET is used to create a "hair trigger".  But also, there are certain applications where the input is very quick, or rapidly switched, so depending on what the trigBoard is hooked up to, it is important to understand the timing specs:

Timing Specifications
======================

The input must be open or closed for at least ~200mS in order to wake the trigBoard.

Testing with a relay to open/close within 100ms, here you can see does not wake the trigBoard (videos are slow):

.. image:: images/100msTest.mov
	:align: center

But works fine at 200ms:

.. image:: images/200msTest.mov
	:align: center


And if you try to re-wake the board within ~200ms after it shuts down, there is a chance it will not trigger.  This is due to the low power circuitry "un-latching".  So if you had a door you were monitoring for both open and close, and someone ran out the door, there's a chance you may only detect the opening of the door. In this scenario, the RTC to check if the door is still open still would work though, but there are things we can do in firmware to check for this, like change the RTC time after an opening to be shorter, just so you know for sure within a period of time the door was closed.  Or even elongate the time that you stay awake for, so you can wait for the closure, but then of course this will impact the battery life.  

If you have a "button" style input, where someone could quickly tap the button, then the trigBoard may not wake.  In this case, here is a simple RC circuit you can build to stretch the wake signal long enough:

.. image:: images/fastswitchingInputs.png
	:align: center




Pinout
-----------

Reference for the expansion pins:

.. image:: images/pinOutFront.png
	:align: center

For expansion - `randomnerdtutorials <https://randomnerdtutorials.com/esp32-pinout-reference-gpios/>`_ has a great guide on what pins do what. Just be careful with the pins marked above with the hazard symbol.  These pins can be used, but just make sure that the mode they're configured for does not interfere with the normal operation of the board.  For example, the GPIO12 cannot be held high during boot up, or the ESP32 will never fully boot up and execute the code.  Also, on board is an RTC using the I2C port, so that same port can be used for other I2C devices, just as long as there's no address conflict.  

**trigBoard Pin Usage**  At the core of the trigBoard, it's just a tricked out ESP32 development board with some incredible low power capability.  Power is actually removed from the ESP32 in order to accomplish the single digit uA standby current.  This is important to note since the 3.3V rail is controlled by the wake circuitry.  If needed, the base firmware can be completely overwritten by whatever custom application that fits the needs of the user.  These are the pins used by the wake circuitry on the trigBoard, which can be used by the user for the custom application:

| **IO36** AINPUT  Battery Pin for analog measurements of the battery voltage
| **IO0** OUTPUT  LED pin - drive low to turn LED ON, but be careful with this pin, since also used by the auto reset circuitry.
| **IO16** OUTPUT  Power Latch Pin - this pin can be driven high to keep power latched to the ESP32.  When the board wakes up, this pin is driven HIGH immediately to keep power applied while the wake source is determined and unlatched.  When the board is ready to go back to sleep, this pin can be written LOW.
| **IO17** OUTPUT  Kill Power Pin or UnLatch Pin - The sensor input utilizes two latches for the normally open/closed inputs.  When the board wakes, these latch inputs can be read in, then unlatched with this pin by writing it HIGH.
| **IO18** INPUT    Contact Opened Latch Pin - If low, the sensor input has got from closed to open.
| **IO19** INPUT    Contact Closed Latch Pin - If low, the sensor input has got from open to closed.
| **IO23** INPUT    Contact Status Pin - real time status of the contact - HIGH = Contact is OPEN, LOW = Contact is CLOSED
| **IO27** INPUT    Wake Button Pin - LOW = Button was pressed
| **IO21 & IO22** Used by the RTC at I2C address 0x51

.. warning::
	* Logic Level is 3.3V on expansion pins - make sure to properly level shift if external device is powered by VBAT.
	* 4V2 pin is always powered at 4.2V even if battery voltage is lower - this can be used to power devices if needed
	* 3V3 pin is the switched 3.3V power to the board when triggered. Useful for polling sensors on wake event (RTC/or Input)


.. image:: images/pinOutBack.png
	:align: center
	:scale: 50 %

Battery
-----------

The greatest feature in the trigBoard is its amazing low standby current, so it is important to select a battery that gives the longest run time. There are a couple considerations to factor in:

* What is the target run time? Usually how many years?
* How often will the trigBoard wake up? Like for a door, how many times a day will that door open? 

To get started quickly, let's take an example:

	* `1200mAh battery <https://www.adafruit.com/product/258>`_ from adafruit
	* The board is hooked up to a door and is opened once an hour
	* Pushover used to send the notification

===========================	=============
 Battery Capacity         	 1200mAh 	
 Awake Device Consumption 	 60mA    	
 Awake Time               	 5sec    	
 Sleep Device Consumption 	 2uA     	
 Sleep Time               	 1hr     	
===========================	=============
Battery Life               	 1.2852 years!
===========================	=============

These parameters can be entered into the tool here: (make sure to enable advanced mode!)

Calculator
=============

.. raw:: html

	<div>
		<div class="omni-calculator" data-calculator="other/battery-life" data-width="300" data-config='{"battery_capacity":{"default":"1200","unitDefault":"mAh","advanced":false},"discharge_safety":{"advanced":false},"consumption_awake":{"default":"60"},"awake_time":{"default":"5"},"consumption_sleep":{"default":"2","unitDefault":"microampere"},"sleep_time":{"default":"1","unitDefault":"hrs"},"battery_life":{"unitDefault":"yrs"}}' data-currency="USD" data-show-row-controls="true" data-version="3" data-t="1593704131906">
		  <div class="omni-calculator-header">Battery Life Calculator</div>
		  <div class="omni-calculator-footer">
		    <a href="https://www.omnicalculator.com/other/battery-life" target="_blank"><img alt="Omni" class="omni-calculator-logo" src="https://cdn.omnicalculator.com/embed/omni-calculator-logo-long.svg" /></a>
		  </div>
		</div>
		<script async src="https://cdn.omnicalculator.com/sdk.js"></script>
	</div>

Current Consumption Data
=========================

Lithium Battery 3.7V
*********************  

================================ =============
3.7V Wifi Connection to Pushover
================================ =============
 Awake Device Consumption 	     52.9mA    	
 Awake Time               	     4.3sec    	
================================ =============   

	.. image:: images/3V7_pushover_awake.png      
		:align: center
		

===========================================	========
3.7V Wifi Connection UDP Message 20 packets
===========================================	========
 Awake Device Consumption 			    	63.1mA    	
 Awake Time               	 			    1.9sec    	
===========================================	========  

	.. image:: images/3v7_udp.png      
		:align: center

===========================	=============
3.7V Sleep Current   
===========================	=============
 Sleep Device Consumption 	 0.861uA    	   	
===========================	=============

	.. image:: images/3V7_sleep.png      
		:align: center

AAA Batteries 3.0V
*********************  

================================ =============
3.0V Wifi Connection to Pushover
================================ =============
 Awake Device Consumption 	     67.5mA    	
 Awake Time               	     4.3sec    	
================================ =============   

	.. image:: images/3Vpushover.png      
		:align: center
		

===========================================	========
3.0V Wifi Connection UDP Message 20 packets
===========================================	========
 Awake Device Consumption 			    	74.4mA    	
 Awake Time               	 			    2sec    	
===========================================	========  

	.. image:: images/3V_udp.png      
		:align: center

===========================	=============
3.0V Sleep Current   
===========================	=============
 Sleep Device Consumption 	 1.05uA    	   	
===========================	=============

	.. image:: images/3V_sleep.png      
		:align: center

Recommended Batteries
======================

.. warning::
	* Operating voltage range is 1.8-5V, but the recommended operating voltage is 2.5-5V.
	* Reverse polarity protection is built-in, but pay attention to polarity!
	* Make sure the low battery threshold is set properly in the configurator for the particular battery topology

* **Two AAA batteries in series (3V)** is highly recommended, since they will have a low self-discharge rate, they're easily replaceable, they're small,they're safe, wide operating temperature, and can have good capacities.  For example, the `Energizer Ultimate Lithium Batteries <https://data.energizer.com/pdfs/l92.pdf>`_ may yield 1200mAh!  The trigBoard product page provides a compatible holder, but these exist elsewhere as well: `Like this <https://www.adafruit.com/product/4191>`_

* **Other Combinations of AAA or AA** If extremely long battery life is needed, could use AA instead of AAA batteries, or even three in series.

* **Lithium Batteries** Adafruit has a nice selection of these kinds of batteries - `any of these will work <https://www.adafruit.com/category/574>`_

Mechanical
-----------

| **Enclosure** This is a simple enclosure designed to house the trigBoard and a AAA holder.  This is available for sale as an option on the Tindie page, but you can print yourself.  Here are the files:
|	 :download:`Lid STL <mech/trigEncLid.stl>`
|	 :download:`Base STL <mech/trigEncBase.stl>`
| **Step Cad Model** Here is the 3D model for the trigBoard - this can be used to design your own enclosure:
|	:download:`trigBoard Step File <mech/trigBoardV8.3.step>`

| **Online 3D viewer** Click `here <https://a360.co/2N7PKFk>`_ with password "trigboard"

.. image:: images/trigBoardgif.gif
	:align: center

