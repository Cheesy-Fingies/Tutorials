# RPI TFT Screen Tutorial 
In this tutorial, we will go over how to connect a small TFT screen to a Raspberry Pi model 4B with the SPI communication protocol. Let's get started!

## Materials
In this project, we will need these few items...
- RaspberryPi Model 4B
- A TFT Screen with a ST7789V driver
- Wires for the SPI connections; Ribbon cables work fine
- An install of the Raspbian OS

## Hardware
Before we can begin our descent into the firmware/software of this tutorial, the hardware must be discussed and approriately configured.
The raspberrypi 4B with 2GB of RAM was used for this guide. The details about the TFT screen being used in this guide can be found at this link: https://goldenmorninglcd.com/tft-display-module/2-inch-240x320-st7789v-gmt020-02/.
Small TFT displays with the ST7789V graphics driver can be found on realitors like AliExpress. Though, Buyer Beware! Some products may not be of quality, so be wary when choosing which screen to purchase.
The pinout for this screen can be found below, thanks to pinout.xyz and Gadgetoid for providing the wonderful documentation found here: https://pinout.xyz/ and https://github.com/pinout-xyz/Pinout.xyz.

![rpi_tft_hardware](https://github.com/user-attachments/assets/59b11846-1a96-46f0-ad8c-a499a477add1)

Now, using the *PHYSICAL* pin list printed next to each RPI pin in the photo, connect the pins of the TFT screen to the pins of the RaspberryPi in the following fashion...

| TFT Pin | RPI Pin |
| ------ | ------ |
| CS | 24 |
| DC | 18 |
| RST | 22 |
| SDA | 19 |
| SCL | 23 |
| VCC | 17 |
| GND | 20 or 25|

Once this step is complete, we are ready to move onto the firmware and software sections of this guide.

## Raspbian Download and Setup
If you already have a raspberry pi with the raspbian OS aleady installed, you may skip this section of the tutorial. Otherwise, you will need a *MicroSD Card* and to proceed to the following link to obtain the *RaspberryPi Imager*: https://www.raspberrypi.com/software/

After installation, as long as you are using the same hardware, choose the following settings...

![rpi_os_setup](https://github.com/user-attachments/assets/a5f31c01-324b-4675-977e-60d2ef8865be)

After clicking *NEXT*,  set the desired username, password, and wifi network that the device will utilize.

![rpi_os_setup_gen](https://github.com/user-attachments/assets/fbf04e8f-690f-4875-9e21-de006c3d2f9a)

Once complete, go to the *SERVICES* section and enable SSH. We will need the SSH port so we may edit the device without the need for a display connection. Choose whether or not you want to have to login to the device with a password everytime you connect via an SSH connection.

![rpi_os_setup_ssh_enable](https://github.com/user-attachments/assets/9ca4ef8e-6daa-4830-b586-f39652ef236d)

After these settings have been confirmed, continue to download and installation. After sometime, the install will be complete and you can now remove the MicroSD Card and insert it into the RaspberryPi.

![rpi_os_setup_end](https://github.com/user-attachments/assets/5d62b7a7-7188-4a46-95ca-33a345c1e521)

## Connecting through SSH
Now that we have the OS ready, we can begin connecting to the device by SSH so we may setup the necessary drivers for the screen to function. Make sure the RPI is plugged in before beginning.

On a seperate computer, start by typing 'cmd' into the search bar and opening a command-line window on your PC. This tutorial will use Windows, but Mac and Linux systems can also be used for SSH connections!
Proceed by typing the following argument

```sh
arp -a
```

This command will list all the devices and MAC addresses that can be seen by the local network. It will appear as something similar to this...

![rpi_ssh_connect_arp](https://github.com/user-attachments/assets/1567b921-3226-41da-8fdd-42652c0bd558)

We now have a list of local hosts. Unless you want to log into your network's WiFi router to find the devices local ip, you will now have to test the various hosts until you come across the raspberrypi. It will appear similar to the following after using the command...

```sh
nslookup
```

![rpi_ssh_connect_nslookup](https://github.com/user-attachments/assets/f96e4bed-5d55-4ffc-b6c4-476ce1d0be34)

There it is! We now have the local IP address of our raspberrypi and may proceed to logging in via SSH. Use the following command to attempt an ssh connection...

```sh
ssh pi@<local-host ip>
```

If you changed the name of your device, the *pi* label will have to be altered to match the name you provided during the installation process. the <*local-host ip*> argument will be replaced with YOUR devices local ip that was found in the previous step.
Assuming that all went well, you will see the following...

![rpi_ssh_connect_success](https://github.com/user-attachments/assets/24741509-d7c1-4030-83bc-b6e940130f35)

If for whatever reason you get back a screen such as...

![rpi_os_setup_ssh_hostfail](https://github.com/user-attachments/assets/8518c93c-2f34-4080-9616-53d6d47fba22)

...for cases such as outdated keys, then we must generate a new key for the SSH connection allow connection. This can be done by running the following cmd-line argument...

```sh
ssh-keygen -R <local-host ip>
```

Which will produce the following or similar...

![rpi_os_setup_ssh_keygen](https://github.com/user-attachments/assets/f1ec6e48-eb5e-4e61-9e16-67c5a073b03b)


Now, attempt ssh connection again. This time, you should be met with the following prompt... 

![rpi_os_setup_ssh_authenticate](https://github.com/user-attachments/assets/45b76fc9-e903-4083-8709-b1e0c2d44ae9)

Type *yes* for key authentification. Finally, the ssh login should proceed forward, and you will have an SSH connection to the RPI.

![rpi_os_setup_ssh_login](https://github.com/user-attachments/assets/67f21a03-49c6-42bd-b8c3-d6b1a1c8dcff)

## TFT Screen Firmware Code

Now that we finally have access to our single-board computer, the RPI, we can start writing the necessary firmware code that activates and runs our TFT module. 
Start by configuring the device with...

```sh
sudo raspi-config
```

You will then be presented with the following screen...

![rpi_os_setup_ssh_TFT_firmware_raspi-config](https://github.com/user-attachments/assets/b53ec8f7-b2de-4dc2-8742-dd2b9550b469)

From here, select *1 System Options*

![rpi_os_setup_ssh_TFT_firmware_raspi-config2](https://github.com/user-attachments/assets/d77464f9-cbad-481a-84bb-707acd75a4e1)

Proceed then by selecting *S5 Boot*

![rpi_os_setup_ssh_TFT_firmware_raspi-config3](https://github.com/user-attachments/assets/305d1bd7-145f-452b-a6df-77d360b964ca)

Decide here if you would like for the RPI to boot to the command-line or the desktop environment

After you have made the decisions about the where to boot to exit by clicking finish 

![rpi_os_setup_ssh_TFT_firmware_raspi-config4](https://github.com/user-attachments/assets/d85e00fc-8ec9-4d80-83df-b8ac8dea68a9)

The next step is to write the driver code for the system to interface with SPI screen. Start by typing the following command argument

```sh
sudo nano /boot/firmware/config.txt
```

This command will bring us to the file configuring all the hardware the RPI includes or should utilize. We will write our driver here so it is seen by the pi.
Firstly, find...
```sh
#dtparam=spi=on
```
...and erase the '#' character to activate the SPI protocol. 

![rpi_os_setup_ssh_TFT_firmware_spi_on](https://github.com/user-attachments/assets/5ba67281-3fbb-471b-8301-ceb41795f50a)


Next, deactivate the following line...
```sh
dtoverlay=vc4-kms-v3d
```
...which is deactivated by doing the opposite of before, placing a '#' character before the line. This has to be done to ensure HDMI does not take over as the main display, otherwise, nothing will display on our SPI TFT! 

Then, right below this deactivation, write the following bit of driver code...
```sh
# TFT Screen Driver
dtoverlay=fbtft,spi0-0,st7789v
dtparam=dc_pin=24
dtparam=reset_pin=25
dtparam=width=240
dtparam=height=320
dtparam=fps=60
dtparam=rotate=270
dtparam=speed=40000000
```
*fbtft* is the driver built-in to the kernel of the OS and will be the name given to our driver, *spi0-0* uses the 0-0 standard spi mode(there are four), *st7789v* is the physical hardware display driver the TFT screen is using.
*dc_pin* and *reset_pin* are set using their SOFTWARE-defined names; we must set these two pins for the driver to function properly, and pins 24 and 25 just so happen to be available.
*width* and *height* are defined for what the screen's hardware offers, in our case 320x240 is the pixel size we must define. *fps* is used to set the framerate the screen refreshes at. *rotate* is optional, only use this if the screen alignment does not function as intended.
*speed* is the SPI communication speed, not exceeding 50MHz(50000000), but make sure to be above 10MHz(10000000) so the screen does not buffer when drawing new frames to the screen.

Once completed, it should appear as such...

![rpi_os_setup_ssh_TFT_firmware](https://github.com/user-attachments/assets/cb3b73ad-9c84-4216-9989-9890abf482c1)

Exit from this file by hitting "CTRL + X", then type "Y" to save the file; DO NOT ALTER THE FILE NAME JUST SAVE IT!

Perform the *sudo reboot* command...
```sh
sudo reboot
```

Now, if you selected to boot to the command-line interface (CLI) mode, then you are all done! Login and the TFT screen will boot to a terminal view! 

![rpi_tft_login](https://github.com/user-attachments/assets/4f1c6430-5fed-454a-af4f-79425c1e9c95)

Here is the same nano viewer we just used to change the boot file, but on the TFT screen...

![rpi_tft_login_nanoviewer](https://github.com/user-attachments/assets/4b560e81-7a06-4669-9414-1728d50df377)

While I don't recommend using a screen this small for a desktop environment, it is possible to set it up. If you selected to have a desktop GUI, you will see this at the boot process...

![rpi_tft_desktop_boot](https://github.com/user-attachments/assets/650d5337-ab4d-4bae-b69d-ab2339601bf5)

This will take a minute or two. Next, the desktop will appear but it will not fit the screen very well; We will have to configure this!

![rpi_tft_desktop_first](https://github.com/user-attachments/assets/d2cd5823-473c-4b0f-b13c-b58f1c4c7856)

Go to the *raspberry-pi logo* at the top left of the screen and scroll down in the drop-down menu until you see preferences...

![rpi_tft_desktop_PI_dropdown](https://github.com/user-attachments/assets/da2fc0d9-3481-4939-9472-6d7fc74e359a)

Now you will click on *appearance settings*...

![rpi_tft_desktop_settings](https://github.com/user-attachments/assets/62a5f149-8899-4129-b381-3e4383ad91b9)

Afterwards, do your best to move the popped-up window and find the *defaults* tab

![rpi_tft_desktop_appearance_settings](https://github.com/user-attachments/assets/a8264988-dfa7-45e4-8e9c-ab75302673fe)

There will be three options, pick *small screens* so we can better fit the GUI to the border

![rpi_tft_desktop_small_screens](https://github.com/user-attachments/assets/7f645386-26b4-4b8b-9be9-d96507de7da9)

After clicking this option, the window and taskbar should adjust and should appear as something like the following... 

![rpi_tft_desktop_resize](https://github.com/user-attachments/assets/1b5b6d18-1d4f-45b1-84ec-2b8a79ede957)

We can now close out of this menu and finally take a breath because we are also done!

![rpi_tft_desktop_final](https://github.com/user-attachments/assets/1206b8bf-9eef-4519-a34b-5af35c78c7d3)


While some windows still don't fit well, this can't be helped due to the screen size. If you are set on using a desktop environment, I recommend buying a screen that is at minimum 640x480 for the desktop enviornment to configure properly! Just don't forget to alter the settings for this bigger screen in the */boot/firmware/config.txt* file, too.


That being said, if you followed up until this point, you now have a Raspberry Pi with a working TFT screen using the SPI protocol. This can be incorporated into a multitude of projects where large monitors are not necessary, but a display is required for simpler tasks (ie. showing time, temperature, or ip information). Congrats on setting up your screen and thank you for reading!

















