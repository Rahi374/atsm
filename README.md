## Inspiration
Wanted to do something embedded IoT with the Dragonboard. Searched lists of projects online and they were all overdone and suddenly thought of this.

## What it does
Connect the main unit (in this case, the Dragonboard) to the suitcase (in production it might be embedded in the suitcase), and connect the slave unit(s) (in this case, the Raspberry Pi) to your important items that are in your check-in luggage. If someone tries to remove an item from your suitcase, a loud obnoxious "beep" will be played and a text message will be sent to you with the suitcase's GPS coordinates.

## How I built it
Dragonboard as the main board that keeps a Bluetooth connection alive with the slave board which is a Raspberry Pi. Uses transmit power level (TPL) to determine distance. Raspberry Pi simply opens up a Bluetooth socket and listens.

Dragonboard communicates to buzzer (and reset switch) over GPIO. It continuously monitors the Bluetooth TPL and if it maxed out, ring the buzzer. Also uses Twilio to send a text message. The text message includes GPS coordinates. In actuality, they are obtained through geolocation, using Google's geolocation API, using a ruby script, and the output nicely formatted to be sent by text over Twilio.

## Challenges I ran into
RSSI was supposed to be used to measure Bluetooth signal strength; it was continuously maxed out.

The Dragonboard's built-in GPS was too weak or the firmware wasn't operational, and there was no USB GPS module. For this reason, the GPS coordinates rae obtained from Google's geolocation API, and the text messages are sent through WiFi (just for this hack).

For some reason, both the Grove button and touch sensor both were stuck sending a high voltage, so I had to use a light sensor as a substitute "reset" switch.

## Accomplishments that I'm proud of
Actually getting the whole thing to work!

Modularity; every small component of the project was in a different script file, so getting the entire thing to work was a matter of making them run together.

Total 205 lines of code! And 4 lines of config!

## What I learned
Sometimes it's good to use premade/precompiled disk images for speedy development.

## Installation/Configuration tips/requirements
Bash is practically the only dependency. Dragonboard requires Ruby.

Within the kernel config, GPIO and Bluetooth are required on both boards.
For this hack, WiFi is required on the Dragonboard as well.
Both the Raspberry Pi and the Dragonboard Debian default kernels come with them.

### Raspbery Pi
- Set autologin console-only `raspi-config`
- Add the following three lines to `.bashrc`
```
if tty | grep tty; then
	sudo /home/pi/script
fi
```
- This runs the script at boot, but after userspace has been completely loaded

### Dragonboard
- Requiers mezzanine shield
- Add `/home/linaro/atsm/boot` to `/etc/rc.local`
- Of course, don't forget the api key in file `get_coords`
- Ruby is required
- Buzzer in GPIO port K
- button/touch/light sensor in GPIO port G
