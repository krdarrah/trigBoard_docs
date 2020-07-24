.. _Pinout:

=======================
Pinout
=======================

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


   