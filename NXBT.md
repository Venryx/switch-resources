# NXBT Setup (working for Switch v12+)

Context:
* Switch firmware update 12.0.0 caused connection issues for all of the controller simulation/emulation solutions: [joycontrol (desktop)](https://github.com/mart1nro/joycontrol), [NXBT (desktop)](https://github.com/Brikwerk/nxbt), [JoyCon Droid (Android)](https://play.google.com/store/apps/details?id=com.rdapps.gamepad), [Switch Pro Controller (Android)](https://play.google.com/store/apps/details?id=com.moyck.switchpro)
* The developers of the apps began research on what changes are needed to restore functionality. Main threads: [joycontrol issue](https://github.com/mart1nro/joycontrol/issues/104), [joycontrol fork issue](https://github.com/Poohl/joycontrol/issues/3), [NXBT issue](https://github.com/Brikwerk/nxbt/issues/18), [JoyCon Droid issue](https://github.com/YouTubePlays/JoyConDroid/issues/57), [JoyCon Droid Discord](https://discord.gg/Tn9CudzQcZ)
* Some success was achieved for [joycontrol](https://github.com/Poohl/joycontrol/issues/3#issuecomment-821082464) and [NXBT](https://github.com/Brikwerk/nxbt/issues/18#issuecomment-817268531). (As of now, not for the Android apps, since the Android API [may not offer the level of control required to fix the issue](./Archives/JoyConDroid_DiscordMessages.md#android-bluetooth-issue-hid-profilesservices).)
* I tried the patched versions of both joycontrol and NXBT, but could only get the NXBT version working. The below are the steps that worked for me. (see my [initial post here](https://github.com/Brikwerk/nxbt/issues/18#issuecomment-822151271))
* **Note: NXBT only supports the "simulated/emulated pro controller" functionality, *not* the Amiibo-related functions**.

Resources:
* Virtual-box setup was based mainly on: https://gist.github.com/colemickens/b08d1a339f4483c6b3c08e739d6cf5d0 (and [this video](https://www.youtube.com/watch?v=zvVNwrseZhg))

Steps:
1) Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
2) Restart your host/physical computer. (In my case, I had to restart twice! Restarting only once gave a `-5633` error code when launching the VM.)
3) Download the XUbuntu iso file (torrent is faster): https://mirror.us.leaseweb.net/ubuntu-cdimage/xubuntu/releases/20.04/release
4) Create a new virtual machine. (shown in [video at 6:07](https://youtu.be/zvVNwrseZhg?t=247))
5) Install Xubuntu in the virtual machine, then restart the VM. (shown in [video at 7:10](https://youtu.be/zvVNwrseZhg?t=430))
6) Install guest additions tools (enables clipboard sharing), then restart: https://linuxize.com/post/how-to-install-virtualbox-guest-additions-in-ubuntu
7) Enable clipboard sharing: Devices->Shared Clipboard->Host To Guest (makes future steps easier)
8) This is probably not necessary, but in my case I ran these commands in the terminal (to match with the colemickens guide), one line at a time:
```
sudo apt-get update -qy
sudo apt-get upgrade -qy
sudo apt-get install -qy git python3-pip libglib2.0-dev libhidapi-hidraw0 libhidapi-libusb0 libdbus-1-dev
sudo pip3 install hid aioconsole crc8 dbus-python
```
9) Mark your Bluetooth adapter to be passed through to the Linux VM: mark/check Devices->USB->YOUR_BLUETOOTH_ADAPTER. (I use the [ZEXMTE Bluetooth dongle](https://smile.amazon.com/gp/product/B0775YF36R), which shows up as "Cambridge Silicon Radio, Ltd CSR 8510 A10".)
10) Install the v12 branch of NXBT (needed for Switch v12+): `sudo pip3 install git+http://github.com/Brikwerk/nxbt.git@abb966d438be79678b1b23579b06517995246618`
11) Run NXBT: `sudo nxbt tui` (`sudo nxbt webapp` also works, but its connection process appears more finicky)
12) If the Bluetooth pairing popup does not show up after several seconds (at the top-right of the screen), try opening XUbuntu's Bluetooth dropdown, pressing "Set up new device", then pressing Cancel; this appears to "kickstart" the bluetooth pairing process, for cases where it gets stuck.
13) Press "Pair" in the now-visible pairing popup. The simulated pro controller should show up in the Switch controller list. (if not, wait for the next pairing popup, then press Pair again)
14) Connect any other desired controllers. (I was only able to connect 1 joycon; any more and the simulated controller disconnects as soon as you leave the change-grip-order page.)
15) Wait ~8 seconds, then press A to close the page. (the wait seems to improve the connection's chance of persisting)
16) If the simulated controller disconnects, open the change-grip-order page and try again. (success rate was ~50% for me, with the waiting)
17) Done! With the NXBT terminal focused (ie. last thing clicked on), pressing keys on your keyboard should trigger button presses on your switch. If your simulated controller's connection persists to this point, it should continue to work even in games. (I tested it in Smash Bros Ultimate, plenty of times, without any disconnects.)

Notes:
* There is one significant limitation currently: If you connect more than 1 physical joycon alongside the simulated controller, the simulated controller will disconnect. So, you can only have up to two players, if you're using a simulated controller. (Well, it's possible that if you have two sets of joycons, then having two lefts or two rights could work alongside it, as the issue seems to come from the Switch detecting a left and right joycon as a pair, which somehow breaks the simulated controller's connection. It's also possible that using a third, wired controller [eg. Magic-NS] could work; I haven't tried it yet.)