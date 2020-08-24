===============================
Motion Sensor - PIR
===============================

.. image:: images/PIRpic.png
	:align: center

This turns your trigBoard into an ultra low power motion sensor! And if you're using the cellular system, this works with that as well - you actually setup the board exactly the same through the Configurator.  The message that is used though is for the **Wake Button**.  This is because the sensor shares this same wake source.  Due to this, some special connections and code are needed.  

`Motion Sensor Code is Here <https://github.com/krdarrah/trigBoardV8_Motion>`_

The motion sensor is based on the AM312 and only pulls like 12uA - bought on `amazon <https://www.amazon.com/gp/product/B07GJDJV63/ref=ppx_yo_dt_b_asin_title_o00__o00_s00?ie=UTF8&psc=1>`_

.. image:: images/pirAmazon.png
	:align: center


You'll also need a Schottky diode and 10k resistor.  The connection will need to be made directly to the same pad shared by the wake button:


.. image:: images/pirHookup.png
	:align: center

.. warning::
	In the past I have ran into issues with these PIR sensors randomly triggering... so far with this setup after running for a long time along a stairwell getting triggered multiple times a day, I have had no issues.  `Here is fix <https://www.kevindarrah.com/wiki/images/thumb/1/11/PIRfilter.png/800px-PIRfilter.png>`_ if you do ever run into strange false triggers.  

**Theory of Operation**
------------------------

The PIR Sensor shares this trigger with the wake button input, which is totally fine and in fact, you could also add a normal contact to this is as well - then it becomes a door/window sensor and motion sensor! or even a water detector/motion sensor? That would be pretty cool!

Sharing this trigger is fine, but that means if we do use the wake button, the message is the same as the PIR trigger... no big deal.  The wake button pulls that pin HIGH to 4.2V, which is why we have that diode in there.  The PIR only drives up to ~1V or so.  That's plenty to wake the board up, but without the diode you would damage the PIR anytime you pressed the wake button.  The code linked above is special because it monitors GPIO39 to see if it's HIGH, then it knows this is a PIR wake, not from the button.  BUT, this is only 1V or so, so I actually have to analogRead this pin, and also just wait for it to go back low before going to sleep again: 

.. code-block:: C++

	  while (analogRead(PIRpin) > 500) { //wait for PIR to settle
	    int PIRread = analogRead(PIRpin);
	    Serial.println(PIRread);
	    delay(10);
	  }

