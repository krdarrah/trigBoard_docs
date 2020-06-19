.. trigBoardDocs documentation master file, created by
   sphinx-quickstart on Thu Jun 18 19:00:23 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

trigBoard v8
=========================================
.. image:: images/trigBoard.jpeg
	:align: center


The trigBoard is an extremely low power platform for the ESP32 WiFi+Bluetooth Module. This board is perfect for battery powered applications, due to it's single digit uA standby current draw ~1.5uA with a 3V battery input. To give an idea as to how low this current draw is, a cheap smoke detector pulls about 7uA. This board was initially designed to be the ideal IoT device - a WiFi connected battery powered monitoring solution for dry contacts (door/window sensors), where a push notification is immediately sent when the contact state has changed. The ultra low standby current does not change regardless if the contact remains in the closed/opened state (this is part of the secret sauce in the trigBoard design). But of course, the trigBoard is a playground of low power features, so it can easily be adapted to a unique monitoring application:

.. youtube:: https://www.youtube.com/watch?v=iMPBpUdL-rk&feature=youtu.be

Contents
========
.. toctree::
   :maxdepth: 2 
   :caption: Basics:

   first.rst
