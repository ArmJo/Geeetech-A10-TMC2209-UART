# How to upgrade Geeetech A10 with TMC2209 in UART Mode
I want to show here how to use [TMC2209 Driver](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_Datasheet_V103.pdf) with the Geeetech A10 in UART Mode.

First of all have a look at the [board](http://geeetech.com/forum/download/file.php?id=4635) and the [schematic](https://github.com/Geeetech3D/Diagram/blob/master/GT2560_V3.0_SCH.pdf)
Under the X-Axis Driver is a not populated header called "UART". This can be used to software control all TMC 2209 Driver via UART.

The jumper you can set underneath the driver are not only for the Step-Configuration, but you can also use them to give them an address on the UART-"Bus". More information on it [here](https://wiki.fysetc.com/Silent2209/) and [here](https://learn.watterott.com/silentstepstick/pinconfig/tmc2209/)
The pins MS1 and MS2 have an internal pull-down resistor, so if you set both jumpers MS1 and MS2 the driver has the address 3.

## How to do it step-by-step:

 **_I am not responsible for your hardware. You do this on your own risk._**

### Hardware:
1. First you need to solder a new header to the unpopulated UART-Header on the board. I was lazy and did this from above - not very clean but doable.  

![New header](./img/solder.jpg)

2. You need to cut the driver TX and RX Pins, because the GT2560 Board is for A4988 Driver and have other functions (MS3 and RST)  

![Cut driver](./img/cut.jpg)  
![Driver top](./img/driver_top.jpg)  

3. Next you have to put in the jumper for each driver and give each one a drifferent adddress. Write down which driver you give which address! (the picture only shows it for one driver with address 2!)  

![Set driver address](./img/address.jpg)  

4. You need to cut and solder some wires so you can connect one pin from the header to all 4 drivers. You need one two wires like this - one for TX and one for RX.

5. You put in the driver (**make sure they are in the correct direction**) - for me the color blue and black did not match with the board!

6. You connect the wires to the header and the drivers (RX to RX and TX to TX and **not crossed** like you know it from UART )

![Hardware done](./img/done.jpg)

The hardware is done!

### Firmware:

Now it is time for the firmware. You need [Marlin 2](https://marlinfw.org/meta/download/) for this as far as I know.
I use [Arduino](https://www.arduino.cc/en/Main.Software) to edit and flash the firmware.
In Configuration.h:
1. Change the driver type to TMC2209 (not TMC2209_STANDALONE!)  

![Change driver](./img/driver.JPG)  

In Configuration_adv.h:
1. Define the pins to use for UART and set the baudrate - on this board Serial3 and 115200   

![UART](./img/uart.JPG)  

2. Define the addresses for each driver (X is really address 0 for me)  

![Set address](./img/add.JPG)  

3. (Optional) Now you can do additional configurations for the driver. For example if they should use stealthchop by default or set a hybrid threshold.  

![Optional Configs](./img/stealth.JPG)  

I highly recommend turning on TMC_DEBUG for a first test!

You can find a lot more information on the [Marlin documentation](https://marlinfw.org/docs/hardware/tmc_drivers.html).

I don't use sensorless homing so I can not tell you how to use it.

Now it is time to flash the firmware.

After uploading and turning on the printer you can send G-Code "M122" to test your configuration.
I used the Terminal on my OctoPrint:  

![Test connection1](./img/conn.JPG)  
![Test connection1](./img/conn2.JPG)  

You can also change settings for the driver with the LCD and rotatry encoder under "Configuration" -> "Advanced Settings" -> "TMC Drivers"