# Establish a VPN through SSH
## Introduction
I recently found[^1] that we can use SSH's built-in function to establish a VPN to home network, which can hugely simplify the setup, and has a lot of advantages such as security, and it helps masquerading our traffic from the outside network(for example, to against censorship).

This article is a detailed(a little tedious) discussion of the usage scenario. For a quick demonstration, skip ahead to the [video demo](#video-demo).
## Network topology
## Video demo
## Prerequisites
- Your home network needs a public IPV4 dynamic address that's not under any type of NAT. 
  - While STATIC public address usually costs a lot more money(and I don't recommend this, since when a hacker do a netscan and find your open IP/port pair, he will have plenty of time trying to break thru your router and SSH service, etc.), DYNAMIC address is enough, you can utilize DDNS to bind a static domain name to it(remember don't share this domain name to anyone, it's functionally identical to a STATIC address to hackers).
  - And some ISPs may give your router a WAN IP that's under NAT by default(you can imagine that your network is under some other router/s managed by the ISP, which you certainly don't have access to it's configuration interface, so you can't do port-forwading setup). It is said that hopefully you can apply for an unNAT address from the ISP with no charge, please consult the ISP in use, and good luck. If still can't get unNAT address, we have other options, such utilize some cloud vps(which has unNAT address) with low cost, to do the relay, but this is out of the descussion of this article, if you're interested, leave a comment.
  - We don't discuss IPV6 here, since a lot of entry-level routers still don't provide the so-called firewall function of IPV6, so you can't allow access of your home devices from outside of this network(the Internet). And if it has such functionality(usually as a single on-off switch), may open up the whole network to public access, then you can just access each devices thru their designated IPV6 address, and no need for the VPN in discussion. Furthermore, IPV6 in my opinion is less secured than IPV4, because we need to secure every service of every device, which is a very complex  and time-consuming project than relying on some router device's IPV4 NAT(so if the hacker can't break thru the router, he can't access the devices behind the router directly). 
- Setup DDNS for it(optional). As a recommendation, you can use [noip](https://www.noip.com/) to setup a domain name in no charge.
- Configure port forwarding for the SSH service of the running Linux server(in my case it's a raspberry pi) on router. Different routers have different configuration interface, please consult your device's manual. 
- Some trivial config to the sshd service, to avoid it dropping client connections too soon. Copy the following lines to /etc/ssh/sshd_config file, and do a `sudo service ssh restart`, then reconnect the SSH client to make the modifications take effect.
  ```
  TCPKeepAlive yes
  ClientAliveInterval 120
  ClientAliveCountMax 720
  ```
- For a Windows client(a laptop which I use to connect back to home network from outside via Internet), install [SSTap](https://sourceforge.net/projects/sstap/)(there may be many alternatives). 

## Steps
## Pros
### Convenience
- Nearly zero-configuration is needed, compared to the VPN service of proprietary network devices, or other equivalent software services. Even Windows VPN client configuration by itself is unfriendly to new users, as an example, those diverse combinations of different encryption options is much a headache to get familiar with.
- Only one port needs to be forwarded. As a comparison, previously if I don't want to spend the time setup a standard VPN service, I have to port-forward every service that I need to access out of home. 
  - Furthermore, some services can't be easily changed to different ports other than the default one(there maybe limitations on both/either side of the server/client, e.g. Windows file sharing service), hence it's impossible to port-forward same services from different devices, and many well known ports may be banned by the network infrastructure. But `ssh` we used here has no such limitation, you can port forward it to any unused port freely.

### Security
- All traffic is encrypted thru SSH. The encryption of SSH should be trustworthy, otherwise it won't be the de-facto standard of connecting Linux terminals. So we don't need to worry about if there're any services in use that are unencrypted or easily deciphered.
- Masquerade the traffic. Since even the most stringent network censorship system still needs to allow SSH protocol to pass thru, to ensure fundamental network management and operation. Standard VPN is known to behave very bad to this concern, since it usually uses standardized port number and has identifiable pattern, so tend to be easily banned by the censorship agency(e.g. some governments forbid personal vpn usage).

## Cons(Todo)
- UDP is unsupported in my setup. Since I've already logged in a rasbperry pi's terminal thru SSH, I can issue commands that rely on UDP there(such as ping devices, `wake on lan` devices, etc.), so there's no immediate need for me to get UDP up and running. Later if the need arises, I'll delve into it.
- This setup is untested against unstable networks, so I'm not sure what it will behave when underlying network connection is intermittent. Later I'd do some research on recovering vpn connection automatically(such as trying `autossh`).

## Conclusions

## References
[^1]:[基于ssh tunnel建立vpn - 听风小筑](https://lisongmin.github.io/os-systemd-ssh-vpn/)(I got to know `ssh -D` from this article, so the SSH server/client in this article is the same with mine. The main difference is that it uses `badvpn` as a Linux vpn client, while my client OS is Windows and I use `SSTap`)