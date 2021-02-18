.. _googleDocs:

=============================
Smoke/CO Detector
=============================

.. image:: images/smokeMain.jpeg
	:align: center

**Add trigBoard to an existing Smoke/CO Detector!**  This is something perfect for true peace of mind and originally was only interested in smoke detection, but Carbon Monoxide is an added bonus here.  The reason is because this specific detector runs on two AA batteries which is perfect because that means we can also power the trigBoard.  I was able to find a test point on the board that goes HIGH to 3.3V when the alarm is ON, so that is what is used to go to the sensor input of the trigBoard.

.. warning::
	THIS SHOULD NOT BE DONE ON YOUR PRIMARY SMOKE/CO DETECTOR!!!!  You are making modifications to a life saving device, so there is a chance that this will not work at all - DO NOT RELY ON THIS FOR LIFE SAVING NOTIFICATIONS.  This is clearly not an end-product that you can truly rely on.  Should only be used to supplement an existing system.

.. hint::
	CO Detection is not working yet - I just need add a circuit to delay the buzzer pulse to the trigBoard.  Will be coming soon...

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/NuwXAvgs4Ho" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


**Smoke Detector**

`Found here on Amazon <https://www.amazon.com/gp/product/B000MXJ498/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1>`_

.. image:: images/amazonsmokedetect.png
	:align: center

**Teardown**

I just used a guitar pick and slid around the case to break the tabs free... a screw driver would work as well:

.. image:: images/smoketeardown.png
	:align: center

**Power**

Connect up your JST Cable (PH2.0mm) to the battery contacts - CHECK POLARITY!

.. image:: images/smokepower.png
	:align: center

**Sensor Input**

We only need the negative input for this, so I removed the positive wire.  I found that Test Point "N23" goes HIGH at 3.3V when the alarm is ON.  

.. image:: images/smokesensorinput.png
	:align: center

.. image:: images/smokesensortestpoint.png
	:align: center	

**Finishing Up**

.. image:: images/smokedone.png
	:align: center

Then would be pretty simple to cut a notch in the housing to route the wires out and mount the board right on top!

**Firmware Setup**

The base firmware supports this right out of the box!  Just configure the device from the "Configurator" to wake on Contact Close - then you're all set.  I might set the low battery alarm to be like 2.5V?  That's another nice feature here is to get a low battery notification as well... like sometimes it's hard to figure out which detector is beeping in the house! 
