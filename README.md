# WannSea VPN
Configuration for our VPN setup. It is deployed via the [docker-compose.yml](docker-compose.yml).
Due to the (presumably VPN-related) connection issues experienced in the 2023 season, we provide 4 different VPN protocols:
- WireGuard
- IPSec (IKEv2)
- IPSec (L2TP)
- IPSec (Cisco)

-------

# What VPN to use
## WireGuard
WireGuard is the newest and fastest VPN solution provided. However being new we experienced some weird connectivity issues (which needs to be tested regarding the other protocols).
From my preliminary tests this has improved and mainly is due to the bad WireGuard client implementation of the Mac App Store version. Linux and HomeBrew clients work good from my point of view.
WireGuard does however need preconfigured peers which each get their own static IP address and certificate. For transparency for all of our team members, if you decide to use WireGuard you may use it, but **UPDATE THIS README** and tell everyone which certificate is yours to avoid issues. An advantage/disadvantage is that WireGuard has no idea of a "connection"/"session". You may hav a completely interrupted connection but your client still says "connected". This helps during instable connectivity phases but also can be quite confusing in other scenarios where the VPN is causing connectivity issues.

## IPSec
The IPSec variants are all a bit older but also more tested. IKEv2 is mostly similar to WireGuard, while L2TP and Cisco may disconnect sometimes when on an instable internet connection.
The advantage is that you get allocated an IP dynamically, so we are able to use a shared certificate (IKEv2) or username/password (L2TP/Cisco) for multiple connections.
*Note: I read somewhere that L2TP may not work when being behind the same NAT, so this may be an issue when being on the same WiFi for example*

------

# Setup
We store all the needed password/certificate information needed for connection in our WannSea password KeePass in the TubCloud. You can also look in ~/wireguard or ~/ipsec on our VPS directly but the Keepass has also some setup information.
## WireGuard
There are a lot of different ways to connect to our WireGuard server depending on your platform. If you are using a platform not documented here, please try to find a tutorial online.

### MacOS / Linux
For macOS and Linux variants i suggest using the wg-quick tool.
```
Mac: brew install wireguard
Ubuntu: apt-get install wireguard
other Distros: tbd or google
```
Then you just need to pick a free peer from the list down below (**AND UPDATE THIS README**), get the corresponding .conf file from our KeePass DB and place it at /etc/wireguard/wg0.conf. 
Now you are able to activate the VPN by running `wg-quick up wg0` and deactivate by `wg-quick down wg0`.

You can test the connection by running `ping 10.13.13.1` which pings our VPS.

## IPSec
Fortunately the IPSec server solution we use has an excellent connection guide for almost every platform and IPSec variant: 
[Setup IPSec](https://github.com/hwdsl2/setup-ipsec-vpn)

### IKEv2
[Setup IPSec/IKEv2 Clients](https://github.com/hwdsl2/setup-ipsec-vpn/blob/master/docs/ikev2-howto.md#configure-ikev2-vpn-clients)

### L2TP
[Setup IPSec/L2TP Clients](https://github.com/hwdsl2/setup-ipsec-vpn/blob/master/docs/clients.md)

### Cisco
[Setup IPSec/Cisco Clients](https://github.com/hwdsl2/setup-ipsec-vpn/blob/master/docs/clients-xauth.md)

------

# VPN IP Networks
(Mostly) Each VPN Protocol has their own IP address range but **ARE** reachable via each other.
The networks are as follows:
| VPN         | Network           |
|-------------|-------------------|
| WireGuard   | 10.13.13.0/24     |
| IPSec/IKEv2 | 192.168.43.0/24   |
| IPSec/L2TP  | 192.168.42.0/24   |
| IPSec/Cisco | 192.168.43.0/24   |

------

# WireGuard IP Assignments
Currently there are 10 possible peers which are assigned as follows:

| IP          | Peer              |
|-------------|-------------------|
| 10.13.13.1  | VPN Server Host   |
| 10.13.13.2  | Raspberry Pi 4 CM |
| 10.13.13.3  | Kolja             |
| 10.13.13.4  | Felix             |
| 10.13.13.5  | Octoprint         |
| 10.13.13.6  | Paul              |
| 10.13.13.7  | Raspberry Pi 5    |
| 10.13.13.8  | -                 |
| 10.13.13.9  | -                 |
| 10.13.13.10 | -                 |

If this list is exhausted contact @kkieslich or increase the `PEERS` env variable in the docker-compose.yml manually. You just need to edit and commit this file, the changes are automatically deployed by GitHub Actions!
