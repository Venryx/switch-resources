This page is for holding web-accessible copies of messages from the JoyCon Droid Discord, so they can be referenced quickly.

JoyCon Droid Discord: https://discord.gg/Tn9CudzQcZ

### Android Bluetooth Issue (hid profiles/services)

(from #general channel, 2021-04-17)
> **dtrunk90:** The left physical/right App Joycon Trick only works for amiibo Problems afaik. Switch fw 12 is pickier about the Bluetooth device. Every Bluetooth device publishes Information about possible profiles/Services available. And if there is for example Headset profile/service available switch will refuse that device. And to manipulate the possible Bluetooth Profiles on an Android device you need root Access.
>
> **c6rlos:** So it detects that my phone has headphone capabilities? Therefore it doesn't connect?
>
> **xItsLegendx:** I guess so. And if you want to edit your bluetooth profiles you need to root your phone
>
> **dtrunk90:** Exactly. That's what different Sources say at least. I'm currently trying to create a magisk module and playing with the Bluetooth Profiles and checking if connection works after disabling every Profile except the human interface device (HID) Profile which is needed to act as a gamepad.
>
> I recently checked which Profiles a joycon offers and there was only the HID Profile.
>
> **AleDub:** So we cant use joycon droid?
>
> **dtrunk90:** If these Informations are all true and i can verify that with my magisk module you cant use it with switch fw 12 on a non rooted device which wasn't paired before the switch update anymore, yes. But we're still checking if that's the case.