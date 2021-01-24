# pihole/openvpn setup tutorial

Here is a quick tutorial to follow to create a pihole/vpn setup to use at home or in public on a phone.

The first thing you are going to want to do is make sure the pi has a static ip.

The way to do this is different for every router so you're going to need to look up how to do this.
Once this is done, port forward the pi's ip on UDP port 1194.

Make sure pihole is updated:
```
sudo apt update
sudo apt upgrade -y
```

Download openvpn, make it executable, and launch it:
```
wget https://git.io/vpn -O openvpn-install.sh
chmod 755 openvpn-install.sh
sudo ./openvpn-install.sh
```
The setup is going to ask what ip to use for the vpn.

Input the public ip here and press enter.
(search "what is my ip" on google to quickly find out what it is)

Press enter three times. (The defaults are fine.)

The setup is gonna ask for a client name. Choose a short name for the client such as `pihole`.

After a short amount of time, the set up will complete and inform you that the profile was created at:

`/root/[client name].ovpn`

Your going to want to get this file off of the pi one way or another.
My favorite way of doing this is with:

`sudo cp -i /root/[client name].ovpn /boot/[client name].ovpn`

This copies the file to the windows accessible partition of the SD card.

If you have pihole already installed and working, reconfigure it with:
`pihole -r`

Otherwise start installing pihole with:
```
curl -sSL https://install.pi-hole.net | sudo bash
```
The pihole setup is going to ask for an interface.

Choose `tun0` by selecting it and pressing space, then enter.

Press enter again to accept blocking ads over ipv4.

The following screen states:

`Do you want to use your current network settings as a static address?`

Select `No` and press enter.

Clear everthing in the following text box and enter: 

`10.8.0.1/24`

and press enter.

The next screen should have the ip of your router (typically 192.168.1.1) in the text box.
If the ip is not the ip of your router, delete what is in the text box and enter the ip of your router.

Press enter.

Press enter again to confirm the settings.

Select a upstream DNS server, press space, then enter.

Name note of the password mentioned.

Once the setup is complete, type `sudo nano /etc/openvpn/server.conf` (or `sudo nano /etc/openvpn/server/server.conf` if it doesn't work or is a new file) and comment out any dns lines:

```
#push "dhcp-option DNS 8.8.8.8"
#push "dhcp-option DNS 8.8.4.4"
```
Add the following under those lines:

```
push "dhcp-option DNS 10.8.0.1"
```
If you want only the dns traffic to go through the vpn, comment out the following line:

`# push "redirect-gateway def1 bypass-dhcp"`

To save the config file, press [Ctrl] + [X] and then [Y].

Run `sudo systemctl restart openvpn@server` to restart the vpn server.

Run `sudo shutdown now` and wait for the pi to shutdown.

Once its completely off, take the SD card out and put the SD card into a windows(or linux) computer and copy the [client name].ovpn (e.g. pihole.ovpn) profile in the root of the `boot` partition onto the computer.

Copy the profile into the android.

Put the SD card back into the pi and boot it up.

Setup OpenVPN via this [link](https://proprivacy.com/vpn/guides/openvpn-for-android).

Once you are connected to the vpn. Go on a website known for ads (using the vpn-connected device) such as [SpeedTest.Net](https://www.speedtest.net/).
If the ads don't load, it works!

Source documents:

[Pihole openvpn installation](https://docs.pi-hole.net/guides/vpn/openvpn/installation/)

[Cyberciti install pihole with openvpn](https://www.cyberciti.biz/faq/ubuntu-linux-install-pi-hole-with-a-openvpn/)

[Pihole only dns via vpn](https://docs.pi-hole.net/guides/vpn/openvpn/only-dns-via-vpn/)
