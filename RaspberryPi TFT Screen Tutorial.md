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

We now have a list of local hosts. Unless you want to log into your network's WiFi router to find the devices local ip, you will now have to test the various hosts until you come across the raspberrypi. It will appear similar to the following...

![rpi_ssh_connect_nslookup](https://github.com/user-attachments/assets/f96e4bed-5d55-4ffc-b6c4-476ce1d0be34)

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











