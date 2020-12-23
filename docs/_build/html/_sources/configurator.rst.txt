.. _Configurator:

=======================
Configurator
=======================

.. image:: images/configurator.png
	:align: center
	:target: https://krdarrah.github.io/trigBoardConfigurator/

All of the parameters in the trigBoard are configured in the `Configurator Tool <https://krdarrah.github.io/trigBoardConfigurator/>`_

.. hint::
	To enter configuration mode on the trigBoard, simply press and hold the wake button until the blue LED starts flashing, then you can let go. The board is ready to be configured.  If you can't connect to it or you change your mind, you can just press the reset button and the board will go back to sleep. I suggest testing out the configurator before installing the trigBoard in it's final location.

.. warning::
	* The Configurator tool only runs in the `Google Chrome Browser <https://www.google.com/chrome/>`_
	* This works on Windows10/Mac/Linux - do not use a mobile device. Older computers may not work!
	* Requires Bluetooth to be enabled and turned ON
	* MUST BE HTTPS
	* You may have to `enable experimental features <chrome://flags/#enable-experimental-web-platform-features>`_ but I have not seen that here with my machines.
	* Problems with Windows 10? Check your build version "Settings->System->About" and should be at least 1703.  One user had issues with v1607. Also noted `this <https://github.com/WebBluetoothCG/web-bluetooth/blob/gh-pages/implementation-status.md#chrome>`_ helped in troubleshooting.


.. image:: images/configuratorGip.gif
	:align: center

This video describes the configurator (and other features) in extreme detail: 

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/tZ0RSLaAH8g" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

| 0:00 Intro
| 00:50 Example/Demo
| 1:43 New Features
| 5:34 Accessories
| 6:47 Low Power Testing
| 10:16 Configuration
| 13:18 WiFi Settings
| 15:18 Wake Settings
| 16:27 Timer Settings
| 19:35 Push Notification Services
| 23:22 UDP Message Demo
| 25:38 MQTT
| 26:30 High Speed Trigger
| 27:54 trigBoard Timer Examples
| 28:55 OTA/Serial Programming
| 29:35 Final Thoughts - Manufacturing/Pricing

Source Code
------------

Here is the `git for the configurator <https://github.com/krdarrah/trigBoardConfigurator>`_

My code includes all libraries, so shouldn't need to add anything, but for reference, this uses the amazing `p5.ble library <https://itpnyu.github.io/p5ble-website/>`_

.. note::
	* Written in `Processing <https://processing.org>`_ v3.5.4
	* In `p5.js mode <https://p5js.org>`_
	* You can run this offline, but use an app for chrome called `Web Server For Chrome <https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb?hl=en>`_ Then from there you can target the downloaded source folder and it will run with no internet connection


