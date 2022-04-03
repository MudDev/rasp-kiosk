
<h1 align="center">
  Rotating crypto prices on Raspberry Pi display
</h1>

![image](https://user-images.githubusercontent.com/44410798/156931622-c9ffb307-4032-4f44-92ea-5359c2a17786.png)

## 🔎 What's inside?

There is a simple index.html page that can be hosted by github pages that the raspberry pi will pull from.


## 🔨 What was used:

  - An old Raspberry Pi (Model B Rev 2.0) I had laying around.

  - I picked up this display at amazon (https://www.amazon.com/dp/B083C12N57) then had to make a few modifications to fit the old raspi in the case, but looks nice mounted on the wall.

### Install the LCD Drivers for the screen:

 - Follow the instructions for the 3.5 screen (sudo ./LCD35-show) https://github.com/goodtft/LCD-show I forked it just incase the other repo goes down: (https://github.com/MudDev/LCD-show)

## ⌨ Setting up the raspi to automatically boot to page.

1.  **Installing X11 and Chromium   Step 1 of 2**

    - Install the X Window System (X11)
        Install xserver-xorg with the command 
        ```shell
        sudo apt-get install --no-install-recommends xserver-xorg
        ```
        Install xinit with the command 
        ```shell
        sudo apt-get install --no-install-recommends xinit
        ```
        Install x11-xserver-utils with the command 
        ```shell
        sudo apt-get install --no-install-recommends x11-xserver-utils
        ```

    - Install Chromium and kiosk dependencies
        Install chromium-browser with the command
        ```shell
        sudo apt-get install chromium-browser
        ```
        Install the kiosk dependencies with the command
        ```shell
        sudo apt-get install matchbox-window-manager xautomation unclutter
        ```

1.  **Configure boot and display options**

    - Create the kiosk startup script
        Create a new file called **kiosk** in the pi user's home folder with the command `nano ~/kiosk` and paste in the following template, updating the URL on the last line to your repo page:

        ```sh
          #!/bin/sh
          xset -dpms     # disable DPMS (Energy Star) features.
          xset s off     # disable screen saver
          xset s noblank # don't blank the video device
          matchbox-window-manager -use_titlebar no &
          unclutter &    # hide X mouse cursor unless mouse activated
          chromium-browser --display=:0 --kiosk --incognito --window-position=0,0 https://muddev.github.io/rasp-kiosk/index.html
        ```

        Make the kiosk script file executable with the command `chmod 755 ~/kiosk`

    - Add the kiosk script to .bashrc

        Initiation of the X Windows System and the kiosk script will be added to the pi user's .bashrc file which runs each time the Pi boots.

        Edit the **~/.bashrc** file with the command `nano ~/.bashrc` to include the following as the last line:

        ```sh
        xinit /home/pi/kiosk -- vt$(fgconsole)
        ```

    - Configure boot and display with the raspi-config tool
    
        From the command prompt on the Pi, enter the command `sudo raspi-config` which will open a text-based menu. Update the configuration:
        ```
          - Advanced Options -> Disable overscan compensation.
          - Boot Options -> Console Autologin [Yes]
        ```

        Upon completing the above, select **<Finish>** and select **<Yes>** when prompted to reboot. If a display is connected, following the boot sequence, the Chromium browser should open in kiosk mode and display the specified webpage.