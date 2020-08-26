# SynchroTime - Console client for setting the exact time and calibrating the RTC DS3231 module

## Motivation
The real-time clock module on the [DS3231](https://create.arduino.cc/projecthub/MisterBotBreak/how-to-use-a-real-time-clock-module-ds3231-bc90fe) chip has proven itself well in work with microcontrollers Arduino, Raspberry Pi, etc. According to the declared specification, it has thermal correction, so that the drift of the clock time is within ±2 ppm (ca. 1 minute per year). But a large number of modules on the market do not meet the accuracy declared by the manufacturer, which is undoubtedly upsetting. Nevertheless, the manufacturer has provided for the possibility of correcting the drift of the clock time, which is associated with the aging of the oscillator crystal in the range from -12.8 to +12.7 ppm. This correction value can be written to one of the registers on the DS3231 ([datasheet](https://datasheets.maximintegrated.com/en/ds/DS3231.pdf) page 14). In addition, the manufacturer has provided a non-volatile memory AT24C256 in the module, into which calibration parameters and correction factors can be placed. The tool below can automatically calibrate the DS3231 module.

## About the app

* Console application is used for fine tuning and calibration of the RTC DS3231 module.

* The application allows you to:
  * Synchronize RTC DS3231 with computer time;
  * Correct the DS3231 RTC clock drift. The algorithm performs correction in the range from -12.8 to +12.7 ppm;
  * Automatically save parameters and calibration data to the energy-independent flash memory of the type AT24C256. In case there is a power failure to the module.

* The client communicates with the Arduino server via the serial interface (UART). The Arduino is in turn connected to the Precision RTC DS3231 module via the I²C-interface. The application allows you to easily select a port for communication with the server and save the port number in the program settings. (The Baud Rate is assumed unchanged and equals 115200).

* Command Help 
`
~$ ./synchroTime -h
`

![synchroTime -h](images/consoleApp_About.png)

## Using the app

1. First, you need to load a sketch into Arduino from the [arduino/synchro_RTC.ino](arduino/synchro_RTC.ino) project directory and connect the RTC DS3231 module according to the diagram shown in the specification.
 Connect your Arduino to your computer via a free USB port. If there is a necessary driver in the system, a new virtual serial port will appear in the system (under Linux it will be /dev/ttyUSBx, under Windows - COMx).
 To find the name of this port, call the application with the -d (--discovery) switch:
```
~$ ./synchroTime -d 
```
![synchroTime -d](images/consoleApp_Discovery.png)

2. To select a virtual Serial Port, enter its system name after the command -p \<portName\>. The app will automatically create a configuration file, and the next call will contact the selected port.
```
~$ ./synchroTime -p ttyUSB0 
```
![synchroTime -p](images/consoleApp_SetPort.png)

3. Use the -i (--information) command to get the current information from the DS3231 module. If everything is connected correctly, then you will get the current time of both clocks, the difference between the clocks in milliseconds (with an accuracy of ±2 ms), the value written in the offset register and the calculated time drift value in ppm. If the offset register and time drift are zero, then the DS3231 has not yet been calibrated (see step 5.)
```
~$ ./synchroTime -i 
```
![synchroTime -p](images/consoleApp_Info.png)

4. To set the exact time, use the -a (--adjust) command. The module clock will be synchronized with the computer time with an accuracy of ±1 ms. After updating the time, the date of the time setting will be recorded in the module's memory, which will allow later to determine the exact drift of the clock time.
```
~$ ./synchroTime -a 
```
![synchroTime -p](images/consoleApp_Adjust.png)

5. To calibrate the clock of the DS3231 module, enter the -c (--calibration) command. For the successful execution of this procedure, the module must be activated (see point 4.) and it is necessary that enough time has passed so that the calculated value of the clock drift is well distinguishable from the rounding error (from several days to several weeks). The algorithm of the program will calculate the amount of drift of the clock time and the correction factor, which will be written into the offset register. The clock time will also be updated. If the calibration is successful, the current time, drift and correction factor will be displayed, as in the screenshot.
```
~$ ./synchroTime -c 
```
![synchroTime -p](images/consoleApp_Calibrate.png)

6. To reset the shift register to its default value and clear the module's memory of calibration data, enter the -l (--clean) command. The default value will be written to the register, and memory cells will be overwritten with bytes with 0xFF.
```
~$ ./synchroTime -l 
```
![synchroTime -p](images/consoleApp_Clean.png)

## Specification

see [datasheet](https://datasheets.maximintegrated.com/en/ds/DS3231.pdf) page 13.

![circuit](images/Steckplatine_DS3231.png)

## System requirements

## Installing the app

## TODO
