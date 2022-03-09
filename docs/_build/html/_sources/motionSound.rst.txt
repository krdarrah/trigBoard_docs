=====================================
Motion Activated mp3 Security System
=====================================


.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/shOsIt5IQ-8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


This is a pretty cool project - an ultra low power 2uA motion sensor system that plays any mp3 you want when motion is detected.  Also has ability to only play the file during certain hours of the day and because it's based on the ESP32, you can also send push notifications.  

.. image:: images/dogBarkPicture.png
	:align: center


Parts Needed
---------------
| - `mp3 Player Board <https://www.amazon.com/dp/B0725RHR4D?psc=1&ref=ppx_yo2_dt_b_product_details>`_
| - `DC to DC Booster Module <https://www.amazon.com/dp/B099K3P7MG?psc=1&ref=ppx_yo2_dt_b_product_details>`_
| - `10W Audio Amplifier <https://www.amazon.com/dp/B06XGL2L1S?psc=1&ref=ppx_yo2_dt_b_product_details>`_
| - `Ultra Low Power PIR Sensor <https://www.digikey.com/en/products/detail/panasonic-electric-works/EKMB1103113/2601873>`_
| - Speaker is nothing special.  I experimented with a few random speakers I had lying around.  
| - Recommend using a pretty large battery for this, just because of the high peak currents when the trigBoard wakes up.  >2000mAh
| - P-CH MOSFET - I used something I had lying around:  `DMP3010LK3Q-13 <https://www.digikey.com/en/products/detail/diodes-incorporated/DMP3010LK3Q-13/5080328>`_  In my case, the PIR is always active, but I only want to play audio within a certain time frame, so this gives the ability to keep the audio circuitry off during the day when motion is detected.  If that part isn't available, just find something with that can support a couple amps, VGS threshold >3V

Connections
-------------

	.. warning::
		The DC-DC booster modules need to be preset before installation.  The 3.3V pin on the trigBoard is only enabled when triggered, so you can wire this to the booster module, but **DON'T** connect the output, until you adjust it for 12V using the trimpot.  I did this with the trigBoard and just held the wake button until the LED starts flashing. That will keep the 3.3V rail up while you adjust the booster output.  Then you can disconnect the battery and finish the connections.  

.. image:: images/dogBarkDiagram.png
	:align: center

|	:download:`Download Diagram <images/dogBarkDiagram.png>`

	.. note::
		I added in a P-CH MOSFET to the design so that power to the audio circuit can be controlled. This is nice because the 3V3 rail will always turn on when motion is detected, even if you don't want to play audio at that time.  I added the MOSFET, so now, only during the active time frame the power to the amplifier & mp3 board is enabled by driving pin 13 LOW from the code.  


Note that the 12V connection is soldered directly to the amplifier board:

.. image:: images/12vwiredtoamplifier.png
	:align: center



Source Code
--------------

:ref:`See Firmware Page for how to upload <Firmware>`

| `Customized Base Firmware HERE <https://github.com/krdarrah/trigBoardV8_BaseFirmware_DOGBARK>`_

	.. note::
		This code adds support for the mp3 player board as well as using the RTC (Clock) to check the current hour so that the dog bark is only enabled during a certain timeframe.  


