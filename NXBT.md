# NXBT Setup (working for Switch v12+)

Context:
* Switch firmware update 12.0.0 caused connection issues for all of the software-based controller simulation/emulation solutions: [joycontrol (desktop)](https://github.com/mart1nro/joycontrol), [NXBT (desktop)](https://github.com/Brikwerk/nxbt), [JoyCon Droid (Android)](https://play.google.com/store/apps/details?id=com.rdapps.gamepad), [Switch Pro Controller (Android)](https://play.google.com/store/apps/details?id=com.moyck.switchpro)
* The v12 update also caused problems for *some* of the 3rd-party hardware controllers/adapters (one was mentioned on Discord), but most remain working fine (eg. the [Magic-NS adapter](https://www.amazon.com/Mayflash-Magic-NS-Wireless-Controller-Nintendo/dp/B079B5KHWQ), which I use to connect an Xbox wireless controller).
* The developers of the apps began research on what changes are needed to restore functionality. Main threads: [joycontrol issue](https://github.com/mart1nro/joycontrol/issues/104), [joycontrol fork issue](https://github.com/Poohl/joycontrol/issues/3), [NXBT issue](https://github.com/Brikwerk/nxbt/issues/18), [JoyCon Droid issue](https://github.com/YouTubePlays/JoyConDroid/issues/57), [JoyCon Droid Discord](https://discord.gg/Tn9CudzQcZ)
* Some success was achieved for [joycontrol](https://github.com/Poohl/joycontrol/issues/3#issuecomment-821082464) and [NXBT](https://github.com/Brikwerk/nxbt/issues/18#issuecomment-817268531). (As of now, not for the Android apps, since the Android API [may not offer the level of control required to fix the issue](./Archives/JoyConDroid_DiscordMessages.md#android-bluetooth-issue-hid-profilesservices).)
* I tried the patched versions of both joycontrol and NXBT, but could only get the NXBT version working. The below are the steps that worked for me. (see my [initial post here](https://github.com/Brikwerk/nxbt/issues/18#issuecomment-822151271))
* **Note: NXBT only supports the "simulated/emulated pro controller" functionality, *not* the Amiibo-related functions**.

Resources:
* Virtual-box setup was based mainly on: https://gist.github.com/colemickens/b08d1a339f4483c6b3c08e739d6cf5d0 (and [this video](https://www.youtube.com/watch?v=zvVNwrseZhg))

Steps:
1) Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
2) Restart your host/physical computer. (In my case, I had to restart twice! Restarting only once gave a `-5633` error code when launching the VM.)
3) Download the XUbuntu iso file (torrent is fastest): https://mirror.us.leaseweb.net/ubuntu-cdimage/xubuntu/releases/20.04/release
4) Create a new virtual machine for XUbuntu.  
	4.1) Press the "New" button in the VirtualBox toolbar.  
	4.2) Configure the VM to have the values seen in [the video, starting at 6:07](https://youtu.be/zvVNwrseZhg?t=247).    
	4.3) [Opt] Set the VM's Settings->System->Processor->Processor(s) value to 2 or higher. (enables significantly faster VM startup)  
5) Install XUbuntu in the virtual machine, then restart the VM.  
	5.1) Follow the steps shown in [the video, starting at 7:10](https://youtu.be/zvVNwrseZhg?t=430).  
	* You can ignore the "Incomplete language support" message on completion. (I did, and saw no negative side-effects)  
6) [Opt] Install guest additions tools (enables clipboard sharing), then restart VM. (based on [this article](https://linuxize.com/post/how-to-install-virtualbox-guest-additions-in-ubuntu))  
	6.1) From VirtualBox menu bar, click Devices -> ???Insert Guest Additions CD Image???.  
	6.2) Double-click the "VBox_GAs_6.1.18" shortcut on the desktop.  
	6.3) Right click the white background in the file explorer, and press "Open Terminal Here".  
	6.4) Run: `sudo apt update`  
	6.5) Run: `sudo apt install build-essential dkms linux-headers-$(uname -r)`  
	6.6) Run: `sudo sh ./VBoxLinuxAdditions.run --nox11`  
	6.7) Reboot the VM. (manually, or from terminal: `sudo shutdown -r now`)  
7) [Opt] Enable clipboard sharing: Devices->Shared Clipboard->Host To Guest (makes future steps easier)
8) This is probably not necessary, but in my case I ran these commands in the VM terminal (to match with the colemickens guide), one line at a time:
```
sudo apt-get update -qy
sudo apt-get upgrade -qy
sudo apt-get install -qy git python3-pip libglib2.0-dev libhidapi-hidraw0 libhidapi-libusb0 libdbus-1-dev
sudo pip3 install hid aioconsole crc8 dbus-python
```
9) Get your Bluetooth adapter working within the Linux VM. (ie. Bluetooth icon showing in the Linux taskbar)  
	9.1) Mark your Bluetooth adapter to be passed through to the Linux VM: mark/enable Devices->USB->YOUR_BLUETOOTH_ADAPTER. (I use the [ZEXMTE Bluetooth dongle](https://smile.amazon.com/gp/product/B0775YF36R), which shows up as "Cambridge Silicon Radio, Ltd CSR 8510 A10".)  
	9.2) If Bluetooth fails to start within the VM, try some of the following:  
	* Install the VirtualBox Extension Pack, from the [same page as VirtualBox](https://www.virtualbox.org/wiki/Downloads), then restart host PC. (Make sure to download the version matching your VirtualBox version.)
	* Try following [the steps here](https://forums.virtualbox.org/viewtopic.php?f=6&t=39104#p176270), except just renaming the "UpperFilters" entry (eg. to "UpperFilters_Disabled") rather than deleting it. (The Bluetooth was not working in the VM on my 2nd computer, and this is what fixed it for me; note that the registry change may have side-effects, so be prepared to revert the entry's deletion/name-change if necessary.)
	* Try adding your Bluetooth adapter to the "USB Device Filters" section in the VM settings (and then restarting your VM and/or host PC), rather than using the "on the fly" menu option.
	* For your host PC's Bluetooth settings, try with it enabled, and try with it disabled; behavior can differ per device.
	* If none of the above fix the issue, try some of the other troubleshooting tips [mentioned here](https://forums.virtualbox.org/viewtopic.php?f=35&t=82639#p390402).
10) Install the v12 branch of NXBT (needed for Switch v12+): `sudo pip3 install git+http://github.com/Brikwerk/nxbt.git@abb966d438be79678b1b23579b06517995246618`
11) Run NXBT: `sudo nxbt tui` (`sudo nxbt webapp` also works, but its connection process appears more finicky)
12) Use a single joycon controller (more than 1 will not work; see note at bottom) to open the "Change Grip/Order" panel on your Switch.
13) If the Bluetooth pairing popup does not show up in the VM after several seconds (at the top-right of the screen), try opening XUbuntu's Bluetooth dropdown, pressing "Set up new device", then pressing Cancel; this appears to "kickstart" the bluetooth pairing process, for cases where it gets stuck.
14) Press "Pair" in the now-visible pairing popup. The simulated pro controller should show up in the Switch controller list. (if not, wait for the next pairing popup, then press Pair again)
15) Wait a bit, then exit out of the panel. (waiting longer *maybe* improves the connection's persist-chance; not sure)
16) If the simulated controller disconnects, open the change-grip-order panel and try again. (success rate was ~50% for me, with the waiting)
17) Enter into the game you want to play.
18) Connect any physical controllers you want to use alongside. (**1 Joycon max**, otherwise the simulated controller disconnects; wired controllers work fine though, so add as many Magic-NS or Gamecube controllers as you want)
19) Done! With the NXBT terminal focused (ie. last thing clicked on), pressing keys on your keyboard should trigger button presses on your switch. If your simulated controller's connection persists to this point, it should continue to work indefinitely. (I've used it in Smash Bros Ultimate many times, without any disconnects past the change-grip-order panel.)

Notes:
	* As mentioned above, there is a limitation currently: If you connect more than 1 physical joycon alongside the simulated controller, the simulated controller will disconnect. So if you want more than 2 players (1 simulated controller + 1 joycon), use wired controllers/adapters like the [Magic-NS BT adapter](https://www.amazon.com/Mayflash-Magic-NS-Wireless-Controller-Nintendo/dp/B079B5KHWQ), or a Gamecube adapter and controllers (for example, I use [this pack](https://smile.amazon.com/Controllers-Extension-Cables-4-Port-Adapter-Set/dp/B07MSHR6JJ)).