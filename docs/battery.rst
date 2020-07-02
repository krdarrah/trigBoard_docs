.. _Battery:

=======================
Battery
=======================

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
--------------------

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
-------------------------

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
-------------------------

.. warning::
	* Operating voltage range is 1.8-5V, but the recommended operating voltage is 2.5-5V.
	* Reverse polarity protection is built-in, but pay attention to polarity!
	* Make sure the low battery threshold is set properly in the configurator for the particular battery topology

* **Two AAA batteries in series (3V)** is highly recommended, since they will have a low self-discharge rate, they're easily replaceable, they're small,they're safe, wide operating temperature, and can have good capacities.  For example, the `Energizer Ultimate Lithium Batteries <https://data.energizer.com/pdfs/l92.pdf>`_ may yield 1200mAh!  The trigBoard product page provides a compatible holder, but these exist elsewhere as well: `Like this <https://www.adafruit.com/product/4191>`_

* **Other Combinations of AAA or AA** If extremely long battery life is needed, could use AA instead of AAA batteries, or even three1 in series.

* **Lithium Batteries** Adafruit has a nice selection of these kinds of batteries - `any of these will work <https://www.adafruit.com/category/574>`_








