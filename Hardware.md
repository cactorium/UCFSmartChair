# Hardware

The original system had four boards, one for power, one for driving the LEDs, one for all the communications, and one for controlling the microcontrollers.
We probably could've fit them all on one board, but it seemed to make sense at the time because we could theoretically test all of them in parallel.
The power board ended up not working at all, bringing the project down to three boards, and we borrowed a regulator from Richie to make up for the broken one.
All the schematics are stored in (a probably broken) Kicad project in schem/seniordesign.
Also we came up with some cool [citation needed] names for all the components, which I don't remember, but you can probably figure it out by reading through the CDR and whatever else.
(TODO: put a git submodule there)

## Overall architecture
(TODO: Look at the chair again because I remember we did some last minute hacks that changed communications)

## Power board
Although the circuit didn't work, we still managed to use the power board as a USB adapter to get 5 volts to power some of the electronics.

### Power supply
The system originally was supposed to run off a single power supply, but it turns out the single supply didn't provide enough current.
Being only two weeks until the demo date, we jerry rigged a second one. 
So, there's a USB battery velcroed to the back of the chair.
This supplies power to the Raspberry Pi 2, which then powers the logic circuitry through its USB ports.
The second power supply is powered from the wheelchair batteries, and supplies power to all the high current things, like the LEDs and servos.
This is connected to the battery through a custom Molex adapter, which you should avoid breaking because Molex pins are basically made from tin foil that breaks if you don't crimp it at just the right angle.

The ground must be shared across both of these (I think we have some wiring that's soldered that does this for you), otherwise best case the chair doesn't run, and worst case you fry everything.
Also we were lazy and didn't add diode protection to anything so please check the polarity of the supplies or you can fry everything.


## LED driver
The LED driver's basically an AVR chip connected to a bunch of simple BJT open collector driver circuits.
The LEDs are connected through some long wires, which have some very very thin (easily thinner than hair) wires soldered to their ends, which then go to the LEDs expoxied in the acrylic sheet on the front.
Please try not to break these because they are a pain to fix.
The wire we used was something like 42 AWG gauge poly coated wire we got from an old guitar pickup.

### Programming
We found that the easiest way to program this was to use an Arduino flashed with ISP code as the programmer, using `avrdude` as the programming utility.
Basically just get the right code on the Arduino, plug it in, make sure the Makefile has the right port, and just run `make flash`.
This is running on a Linux system, although I don't think it'd be too different on a Windows machine.
(TODO find where I put the flasher files).


## Servo controller
There's two chips on this board, an FTDI USB to UART chip and a MSP430F2553.
This lets you talk to the MSP430 using a Micro-USB connector, and the MSP430F2553 then bit bangs PWM for both the servos.
(TODO figure out where the code is for this).
The code for this isn't particularly advanced, it basically just has five states and goes hard in each direction.
The current code, or the servos, tend to cause the servos to burn out, so anyone working on this project may want to look into fixing this.
Also plugging the power to the servos backwards immediately destroys their electronics.

### Programming
We just popped the chip out of the socket and programmed it in a LaunchPad.
I'm pretty sure the TI lab is still giving out LaunchPads.
Worst case it's like 10 dollars online.
A small flathead screwdriver is very useful from removing this chip, and buy a few spares because you _will_ break some of the pins off the chips if you're messing with them long enough.


## Everything else board
This guy has everything else. GPS, Bluetooth, and a Tiva C. 
Everything works except for the Tiva C socket, which seems to consistently destroy chips.
So we have a dev board soldered on the appropriate GPIO pins effectively.
