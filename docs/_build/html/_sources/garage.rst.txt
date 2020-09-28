=========================================
Garage Door Monitor
=========================================

.. image:: images/garageopen.gif
	:align: center


Ever have that feeling that you left the garage open? Well you can use trigBoard to monitor and door/window easily, but the garage door can be tricky to interface with a magnetic sensor.  Here is an easy way using a tilt sensor (mercury switch)  This works so much better, because the garage door does not open/close very smoothly. The shock and vibration of the door will often trigger "double taps", sending out multiple push notifications.

.. warning::
	Mercury is a hazardous substance! Make sure you are fully aware of the dangers in using a mercury switch: `EPA's Website here <https://www.epa.gov/mercury>`_ and how to clean up `here <https://www.epa.gov/mercury/what-do-if-mercury-thermometer-breaks>`_ Only do this at your own risk! 

**Full tutorial:**

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/UFdYCECeScg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


**Parts**
=========

- The mercury tilt switch I'm using was purchased on eBay:

.. image:: images/mercswitchebay.png
	:align: center

And that's pretty much it, I just soldered this to a `JST PH2.0mm connector <https://www.adafruit.com/product/261>`_

**Install**
============

First I made sure I had the mercury switch oriented properly in the enclosure: 

.. image:: images/garagegif.gif
	:align: center

In a garage, things get bumped around and smashed, so I decided to fully seal in the switch with RTV, but maybe epoxy would work better:

.. image:: images/RTVstuff.png
	:align: center

And you see that I have the mercury switch mounted away from the wall of the enclosure to get full coverage around the switch:

.. image:: images/mercmountedenc.png
	:align: center

Then just covered it with RTV:

.. image:: images/rtvaplied.png
	:align: center

Looks pretty good: 

.. image:: images/mercallputtogether.png
	:align: center

.. image:: images/mercgaragebuttoned.png
	:align: center

Actually was able to use an existing screw and some VHB double sided tape to mount: 

.. image:: images/mercmountedongarage.png
	:align: center


**Programming**
===============

None needed - base firmware works perfectly with this, so if you want to  get notified when opens or closes, you set that up all through the configurator.  And same with if you want to know if left open, just use the timer functionality to check if "still open".
