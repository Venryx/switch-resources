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
	6.1) From VirtualBox menu bar, click Devices -> “Insert Guest Additions CD Image”.  
	6.2) Double-click the "VBox_GAs_6.1.18" shortcut on the desktop.  
	6.3) Right click the white background in the file explorer, and press "Open Terminal Here".  
	6.4) Inside the XUbuntu VM, open a terminal in the virtual CD directory. (go there in File Manager, then right-click)  
	6.5) Run: `sudo apt update`  
	6.6) Run: `sudo apt install build-essential dkms linux-headers-$(uname -r)`  
	6.7) Run: `sudo sh ./VBoxLinuxAdditions.run --nox11`  
	6.8) Reboot the VM. (manually, or from terminal: `sudo shutdown -r now`)  
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
12) If the Bluetooth pairing popup does not show up after several seconds (at the top-right of the screen), try opening XUbuntu's Bluetooth dropdown, pressing "Set up new device", then pressing Cancel; this appears to "kickstart" the bluetooth pairing process, for cases where it gets stuck.
13) Press "Pair" in the now-visible pairing popup. The simulated pro controller should show up in the Switch controller list. (if not, wait for the next pairing popup, then press Pair again)
14) Connect any other desired controllers. (I was only able to connect 1 joycon; any more and the simulated controller disconnects as soon as you leave the change-grip-order panel.)
15) Wait ~8 seconds, then exit out of the panel. (the wait seems to improve the connection's chance of persisting)
16) If the simulated controller disconnects, open the change-grip-order panel and try again. (success rate was ~50% for me, with the waiting)
17) Done! With the NXBT terminal focused (ie. last thing clicked on), pressing keys on your keyboard should trigger button presses on your switch. If your simulated controller's connection persists to this point, it should continue to work even in games. (I tested it in Smash Bros Ultimate, plenty of times, without any disconnects.)

Notes:
* There is one significant limitation currently: If you connect more than 1 physical joycon alongside the simulated controller, the simulated controller will disconnect. So if you're using a simulated controller and want to do local multiplayer, you are limited to 1 simulated controller + 1 joycon.
  * EDIT: Actually, I've found that you can connect a third controller by using a [Magic-NS adapter](https://smile.amazon.com/Mayflash-Magic-NS-Wireless-Controller-Nintendo/dp/B079B5KHWQ). I've confirmed that it can be used alongside 1 simulated controller and 1 joycon; however, it seems to make the simulated controller require more connection attempts before it succeeds (it usually disconnects after leaving the change-grip-order panel -- but eventually it'll work, and stay connected after that), and I don't know the limit of how many Magic-NS devices can be used alongside.
  * It's also possible that if you have two sets of joycons, then having two lefts or two rights could work alongside it; the issue seems to come from the Switch detecting a left and right joycon as a pair, which somehow breaks the simulated controller's connection.