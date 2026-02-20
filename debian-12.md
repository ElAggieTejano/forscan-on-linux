# FORScan on Debian 12

1. Verify keyring directory exists. If it does not, generate the directory
```shell
sudo mkdir -pm755 /etc/apt/keyrings
```

2. Add wine key to apt's key rings
```shell
sudo wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
```

3. Add wine's source for Debian 12
```shell
sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/debian/dists/bookworm/winehq-bookworm.sources
```

4. Install wine
```shell
sudo apt-get update -y
sudo apt-get install -y --install-recommends wine
```

5. Download FORScan for Windows

6. Install FORScan using wine
```shell
# navigate to the directory where FORScan installer is located
wine FORScanSetupX.X.XX.release.exe
# where X.X.XX is the version to install
# at the time of writing this, the latest version was/is 2.3.70

# if launching the installer with wine fails, perform the following
sudo dpkg --add-architecture i386
sudo apt-get update -y
sudo apt-get install wine32:i386  wine -y

# attempt to launch FORScan installer with wine again
```

7. Follow the installation prompts.
  * When prompted for installation directory, the default installation directory will be `C:\Program Files\FORScan\`
  * `C:\` maps to `~/.wine/drive_c/`
  * Users may choose the default installation directory
  * OR select a different installation directory

8. Once installed, setup FORScan using the FORScan website to get a license

9. Ensure user is part of the dialout group. Run the following command as root or with sudo to add a user to the dialout group
```shell
sudo usermod -a -G dialout <USERNAME>
logout # logout required for user modifications to take effect
```

10. After logging back in, verify user is part of dial out group:
```shell
# will list all groups user is part of; dialout should be listed
groups | grep dialout
```

11. Connect OBDLinkEX (or similar OBD-II to USB) to computer

12. Verify device shows up as a device
```shell
ls -l /dev/ | grep "ttyUSB0"
```
  * If nothing is returned, the device is not being recognized. This is uncommon on a stock Debian 12 installation since the required kernel modules (ftdi_sio, cp210x, ch341) are included by default.
  * Try the following:
    * Run `sudo dmesg | tail -20` right after plugging in to check for errors
    * Verify the module is loaded: `sudo lsmod | grep ftdi_sio`
    * Manually load it if needed: `sudo modprobe ftdi_sio`

13. Additionally, execute the following command:
```shell
sudo dmesg | grep -i usb
```

14. Users should see a similar output to the following indicating the device was detected:
```shell
[29899.686364] usb 1-4: new full-speed USB device number 11 using xhci_hcd
[29899.844725] usb 1-4: New USB device found, idVendor=0403, idProduct=6015, bcdDevice=10.00
[29899.844737] usb 1-4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[29899.844742] usb 1-4: Product: OBDLink EX
[29899.844746] usb 1-4: Manufacturer: ScanTool.net LLC
[29899.844749] usb 1-4: SerialNumber: 223230324452
[29899.848411] ftdi_sio 1-4:1.0: FTDI USB Serial Device converter detected
[29899.848502] usb 1-4: Detected FT-X
[29899.849707] usb 1-4: FTDI USB Serial Device converter now attached to ttyUSB0
```

15. With the device being detected by the Linux machine, add a symlink for FORScan to detect the device as a COM port by running the following command:
```shell
ln -s /dev/ttyUSB0 ~/.wine/dosdevices/com5
```

16. Verify the symlink is present:
```shell
ls -l ~/.wine/dosdevices/ | grep com5
# should return the symlink generated from step 16
```

18. Connect the other end of the OBDLinkEX adapter to the vehicle's OBD-II port

19. The light on the adapter should turn green

20. Start the engine

21. Launch FORScan with wine by providing wine with the full path to `FORScan.exe`. For example, if users opted for the default installation directory (from Step 7) the command to launch FORScan is as follows:
```shell
wine ~/.wine/drive_c/Program\ Files/FORScan/FORScan.exe
```

22. On the left hand side, click the "gear" button to adjust the settings

23. With the settings panel open, select the "Connection" tab

24. Click on the "Connection type" drop down box and select "COM"

25. Under "COM settings", click on the "COM port" drop down box and select "COM 5"

26. On the left hand side, click the "vehicle" button

27. With the vehicle panel open, on the bottom of the panel click the "Connect to vehicle" button

28. When through with the application:
  * **BEFORE** closing the FORScan application
  * **BEFORE** disconnecting the cable from the OBD-II port
  * **ENSURE** to click the "Disconnect from vehicle" button to safely disconnect from vehicle
