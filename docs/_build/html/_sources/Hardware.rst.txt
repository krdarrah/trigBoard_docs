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


.. toctree::
   :maxdepth: 2 

   pinout.rst
   battery.rst
   mechanical.rst
