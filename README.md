# LSC-Smart-Connect-GU10-ESPHome
ESPHome firmware for GU10 with Tuya T1/BK7238
Action LSC Article Number: 3208076.3

<img width="722" height="723" alt="LSC-GU10" src="https://github.com/user-attachments/assets/5ea46dc2-4d34-46ea-825e-f7b58b2df17e" />

Hardware hacking the [Action LSC GU10 (spot) lights](https://www.action.com/nl-nl/p/3208076/lsc-smart-connect-slimme-spots/) into ESPHome.

These lights are a real pain in the butt to open up. The milky white top cover can be removed by applying heat from a heat gun or hair dryer and gently prying with a plastic prying tool.
Be gentle and apply just the right amount of heat because you may break the glas housing or melt the milky white top cover! And yes, I broke the glass ;)

<img width="3024" height="4032" alt="GU10-1" src="https://github.com/user-attachments/assets/4a2a498f-2ad0-4c3a-a4b6-e8e048591b62" />

Once opened up you will be greeted with the RGB, Cold White and Warm White LED's:

<img width="3024" height="4032" alt="GU10-2" src="https://github.com/user-attachments/assets/614589e2-6b12-4bdb-b324-a084a77e663b" />

The ring containing the LED's is glued in place firmly. At first I used a knife to cut away the glue and then applying isopropyl alcohol to weaken the glue bond whilst prying along the sides with a suited prying tool.
Eventualy the glue bond will losen the ring and you can gently grab the ring by the white connector with small pliers to take it out.  

Now you can see the PCB. Nothing much to see so I yanked out the board to see if and where the TX, RX, GND, 3V3 and CEN pins were located.

<img width="4032" height="3024" alt="GU10-3" src="https://github.com/user-attachments/assets/3e384b8a-15e5-4a94-8f68-85aac121d689" />

Pretty darn nice! You don't have to remove the PCB from the glass housing to flash this light. You can solder the 3V3 and GND wires to the two contact pads on the left of the resistor and the TX, RX and CEN pins are in the front of the PCB were you can solder easily.

Next, connect the soldered wires to your UART flasher according to the diagram below:
```
I:     --------+        +--------------------
I:          PC |        | BK7231             
I:     --------+        +--------------------
I:          RX | ------ | TX1 (GPIO11 / P11) 
I:          TX | ------ | RX1 (GPIO10 / P10) 
I:             |        |                    
I:         GND | ------ | GND                
I:     --------+        +--------------------
```
Make sure to solder an extra wire (2nd wire) to GND (you can connect this to the CEN pin later.)

> [!IMPORTANT]
```The UART adapter's 3.3V power regulator is usually not enough. Instead, a regulated bench power supply, or a linear 1117-type regulator is recommended.```

Once you have soldered suitable wires (dupont) to the following pins: 3V3, GND, TX and RX, it will look like this:

<img width="3024" height="4032" alt="GU10-4" src="https://github.com/user-attachments/assets/99f4399b-2ab3-4f28-9592-387c1f4c7657" />

- Connect the external 3V3 power source
- Connect the UART adapter to a USB port on your system (make sure the correct drivers are installed) ;)
- Compile your ESPHome configuration to create an .rbl file. Download this file to your Downloads folder.

> [!NOTE]
> You can backup the existing Tuya / LSC firmware by executing this command first:
```ltchiptool flash read bk72xx backup.bin```
> But, if your Action article number is exactly the same I don't think you have to because I already did this.
> If you chose to do so, I used [BK7231 GUI Flash Tool](https://github.com/openshwprojects/BK7231GUIFlashTool) to successfully extract the Tuya GPIO/config from the backup image file.
> These findings and settings (like exact Tuya mA current settings for each LED) are in the esphome_config.yaml file.

> [!NOTE]
> Use one of the flash tools from the link below. I used macOS. Unfortunately, it didn't work with Windows.
```https://docs.libretiny.eu/docs/flashing/tools/ltchiptool/#installation```

- From MacOS terminal, run the following commands: ```pip install ltchiptool``` & ```pip install wxPython```
- Navigate to the "Downloads" folder. You should find your previously generated ESPHome.rbl file there
- Right-click on the "Downloads" folder at the bottom of the window and select "Open in Terminal"
- Turn on the external 3V3 power supply
- Run the following command in the terminal window: ```ltchiptool flash write bestandsnaam.rbl```
- ltchiptool starts the flashing process
- Now briefly touch the CEN pin with the extra GND wire
- You will see the flash process start now
> [!CAUTION]
```Be patient! The flash process may take a while. If the chip doesn't boot after flashing, try the flash process again. If you see an error/retry, stop flashing and start again! ```
- Flashing complete? Great! Turn off the 3V3 power supply, disconnect the UART adapter, and turn the 3V3 power supply back on
- Go to the ESPHome dashboard and check via the wireless logs whether the device you just flashed comes/is online
- Node online? Great! Turn off the 3V3 power supply, desolder all wires, and check that all soldering is still correct
- Connect your light and check whether they work as expected
- Yes? Great!
- Insert the LED disk back in to the housing and make sure the pins are connected in the right way in the white connector
- Take a hot glue gun (or other glue), squirt some glue around the edge of the glass and PCB to seal it
- Apply some hot glue to the inner ring of the glass and place the milky white plastic
- Hold firmly (or fix)
- Enjoy! :)

> [!NOTE]
The ```espome_config.yaml``` file contains a working configuration with some light effects! Adjust the configuration to your personal needs.
