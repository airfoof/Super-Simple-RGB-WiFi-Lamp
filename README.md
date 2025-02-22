# Super Simple RGB WiFi Lamp
[![Super Simple RGB WiFi Lamp](http://img.youtube.com/vi/WLXLV6ASLJM/0.jpg)](https://www.youtube.com/watch?v=WLXLV6ASLJM)
 
## Getting started
This is the GitHub repo to support my Instructables publish. Be sure to head over there to [see how to build and wire it](https://www.instructables.com/id/Super-Simple-RGB-WiFi-Lamp/)!

#### Installing the Arduino IDE 
This project was written in the Arduino environment. If you do not have it you will need to [download it from here](https://www.arduino.cc/en/main/software) to be able to open and compile any of the files in this repo.

#### Installing the ESP8266 Core
Since this project uses the ESP8266 as the microcontroller, the board needs to be installed into the Arduino environment. This can be done by following the steps over at [the GitHub repo for the ESP8266](https://github.com/esp8266/Arduino).

#### Installing Libraries
The code to run the lights was built to be as simple as possible for users to set up their device and get it up and running. This comes at the expense of the code being a little more complex to get some of the features I wanted. To speed up development I used a couple of absolutely amazing additional libraries which include;
- [ArduinoJson](https://arduinojson.org/) for messaging in JSON. 
- [ESPAsyncTCP](https://github.com/me-no-dev/ESPAsyncTCP)] for the base layer of the WebSocket's 
- [ESPAsyncUDP](https://github.com/me-no-dev/ESPAsyncUDP) for collecting the time from an NTP via UDP
- [FastLED](https://github.com/FastLED/FastLED) for controlling the LED's
- [Timelib](https://github.com/PaulStoffregen/Time) for keeping track of the current time
- [WebSockets](https://github.com/Links2004/arduinoWebSockets) for creating a server for sending messages to and from clients

These additional libraries need to be installed, of which some are available in the Arduino IDE and some not. Due to this I have included the current working versions of the libraries in .Zip format to be able to import into the Arduino IDE. If you need to install these libraries, clone this repo and install the .zip files in the External Library folder using [the method from Arduino](https://www.arduino.cc/en/guide/libraries).

## Website Features
This project comes with its own inbuilt website built in Bootstrap 4 to help make controlling the LED's much simpler. The website is accessible either via your home network if connected, or the ESP's wireless access point. Within the site you can change the mode and the individual settings for each. You can also change your connected Wi-Fi on the Wifi config page.

If you connect your ESP8266 through your home Wi-Fi network, this will be available at the IP address of the device, or at the mDNS name which defaults to http://Super-Simple-RGB-WiFi-Lamp.local/. 

If you are connected to the ESP8266 via its access point, the webpage should be sent via a captive DNS. This is automatic on windows and apple products, but will require clicking "sign into network" on android phones. It should be noted that mDNS does not work in this mode, and the default IP address is http://192.168.4.1.

#### Home Page
![Alt text](Pictures/Website%20Home%20Page.PNG)
Here on the home page you can easily select which mode you would like or select the Wi-Fi config tab to change your Wi-Fi network. At the top of this page you will see the three global settings. These are the current mode the light is in, The fading time between modes and power cycles, and the power button. These functions are at the top of each page. Please note, if the website is not connected to your ESP8266's WebSocket server, the Mode text will say WS closed.

#### Colour Mode
![Alt text](Pictures/Website%20Colour.PNG)
The colour page sets the mode to colour automatically. In this mode all of the LED's are set to the same colour and brightness. The colour can be changed by clicking on any of the buttons, the top one providing a colour picker. 

#### Rainbow Mode 
![Alt text](Pictures/Website%20Rainbow.PNG)
Rainbow Mode has two methods of being used. The user can either set the starting hue, being the colour that the RGB strip starts at, or set the speed at which the rainbow shifts around the entire strip. The total brightness of the LED's in rainbow mode can also be controlled here.

#### Clock Mode
![Alt text](Pictures/Website%20Clock.PNG)
In clock mode the time of day is shown using the top and bottom of the light. On the top, the lights progression along the length is the total number of hours passed since 12am/pm. This is 12 hour, so 6am/pm will be in the middle. The minutes are shown in the same manor, just with the total period of time being the number of minutes that have passed in the hour. In minutes 0 is at the start, and 30 will be the middle.

#### Bell Curve Mode 
![Alt text](Pictures/Website%20Bellcurve.PNG)
Bell Curve mode is the same as colour, where all the LED's are set to the same brightness. They are however dimmed in a bell curve shape to provide a more aesthetically pleasing cove of light. 

#### Night Rider Mode
![Alt text](Pictures/Website%20Night%20Rider.PNG)
This is a gimmick mode that I just had to put in. It fades the lights left and right across the length just like the front of Kit from night rider. This mode has no but instead just nostalgia.

## Messaging Specification
The webserver code is listening for incoming WebSocket messages with a JSON payload. This is processed after the message is received out of the callback. The complete example of the message is as follows. By connecting to the WebSocket server at port 81 from an external application such as node red, allows users to talk to the device. 

```
{
    "Name": "Super Simple RGB WiFi Lamp",
    "Mode": "Colour",           // Needs to be a String
    "State": true,              // Needs to be boolean
    "Fade Period" : 200,        // In milliseconds and will be kept above 0
    "Colour": {
      "Red": 0,                 // Values will be constrained to between 0 and 255 
      "Green": 0,               // Values will be constrained to between 0 and 255
      "Blue": 0                 // Values will be constrained to between 0 and 255
    },
    "Rainbow": {
      "Hue": 0,                 // Values will be constrained to between 0 and 360
      "Speed": 10,              // Values will be kept above 0
      "Brightness": 100         // Values will be constrained to between 0 and 255
    },
    "Clock": {
      "Epoch" : 1569494993,     // This is the UNIX time stamp of seconds since 1970
      "hourColour": {
        "Red": 0,               // Values will be constrained to between 0 and 255
        "Green": 0,             // Values will be constrained to between 0 and 255
        "Blue": 0               // Values will be constrained to between 0 and 255
      },
      "minuteColour": {
        "Red": 0,               // Values will be constrained to between 0 and 255
        "Green": 0,             // Values will be constrained to between 0 and 255
        "Blue": 0               // Values will be constrained to between 0 and 255
      }
    },
    "Bell Curve" : {
      "Red": 0,                 // Values will be constrained to between 0 and 255
      "Green": 0,               // Values will be constrained to between 0 and 255
      "Blue": 0                 // Values will be constrained to between 0 and 255
    },
    "Night Rider" : {
      
    },
    "Wifi": {
      "SSID": "Test",           // This needs to be a String, empty strings will be accepted causing the Wi-Fi to disconnect and go into softAP mode
      "Password": "Test"        // This needs to be a string, empty values are accepted
    }
  }
```

## Contributing
If you would like to edit or use this project elsewhere you are more than welcome, I just ask that you let me know if you can, I'd love to see how the project is used and adapted. Changes and additions to Modes, the website, or documentaion here on GitHub are also welcome. I just ask that you follow the basic contributing guide and make a pull request so I can both read and test your changes before implimenting. 
