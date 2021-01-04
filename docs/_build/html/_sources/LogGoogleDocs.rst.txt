.. _googleDocs:

=============================
Log to Google Sheet - IFTTT
=============================

.. image:: images/testsheetgoogle.png
	:align: center

**Log trigBoard events to an online Google Sheet!**  This is very simple with IFTTT, but you will need to obtain a free maker key - go to the :ref:`Supported Services Page <IFTTT>` to set this up.  Once you have the key, you can proceed with the following steps.  Also note that you can use this as a "backup", so receive a push notification as well as an email.  Just enable any service you want and they'll all fire off at once.  

**Create an Applet**

.. image:: images/createApplet.png
	:align: center

**Click Add**

.. image:: images/ifbuttonclick.png
	:align: center

**Search for Webhooks**

.. image:: images/searchforwebhooks.png
	:align: center

**Select this**

.. image:: images/selectwebhooks.png
	:align: center

**This is important - the Event Name must Match the trigBoard Name you give in the Configurator**

.. image:: images/eventNameapplet.png
	:align: center

**"THAT" click Add**

.. image:: images/thatbuttponclick.png
	:align: center

**Search Google Sheets and select add row - note that you will have to link this to your google account**

.. image:: images/googlesheetsearchsheet.png
	:align: center

.. image:: images/googleaddrowsheet.png
	:align: center

**The settings for the spreadsheet - give the sheet a name and note that Value3 is not used, so can delete that**

.. image:: images/googlesheetappletsettings.png
	:align: center

**All set then - continue**

.. image:: images/googlesheetappdone.png
	:align: center

**Give the applet a custom name and finish up**

.. image:: images/googlesheetapplname.png
	:align: center

**Launch the Configurator and set things up, WiFi, IFTTT enabled and put your Maker Key in, and set the trigBoard name to the Event Name**

.. image:: images/configuratoriftttname.png
	:align: center

**All set! Now you can test things out and you should see a spreadsheet**

.. image:: images/googledrivefile.png
	:align: center

.. warning::
	I have seen some issues here where the spreadsheet is not automatically created... in that case, try creating it manually per the path you have configured in the settings for this applet. If still having problems, you can disconnect the link with Google from IFTTT and try re-creating the applet.  

**And after testing a couple events, all looks good! Note that you can go back into the settings for this applet and modify how the logs are added.  Also, multiple trigBoards can log to the same sheet, just add a prefix to the contact open/close message while using the same trigBoard name. So name might be trigBoard, then contact open message " Garage Opened" and second trigBoard using same name could have its message " Front Door Opened"**

.. image:: images/testsheetgoogle.png
	:align: center

