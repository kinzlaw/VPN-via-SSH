# Establish a VPN through SSH
## Introduction
I recently found that we can use SSH's built-in function to establish a VPN to home network, which can hugely simplify the setup, and has a lot of advantages such as security  and it helps masquerade our traffic in some specific network environment.
## Network topology
## Video demo
## Prerequisites
- Your home network needs a public IPV4 dynamic address that's not under any type of NAT. 
  - While STATIC public address usually costs a lot more money(and I don't recommend this, since when a hacker do a netscan and find your open IP/port pair, he will have plenty of time trying to break thru your router and SSH service, etc.). DYNAMIC address is enough, you can utilize DDNS to bind a static domain name to it(remember don't share this domain name to anyone, it's functionally identical to a STATIC address to hackers).
  - And some ISPs may give your router a WAN IP that's under NAT by default(you can imagine that your network is under some other router/s managed by the ISP, which you certainly don't have access to it's configuration interface, so you can't do port-forwading setup). It is said that hopefully you can apply for an unNAT address from the ISP with no charge, please consult the ISP in use, and good luck. If still can't get unNAT address, we have other options, such utilize some cloud vps(which has unNAT address) with low cost, to do the trasmission 转发, but this is out of the descussion of this article, if you're interested, leave a comment.
  - We don't discuss IPV6 here, since a lot of entry-level routers still don't provide the so-called firewall function of IPV6, so you can't allow access of your home devices from outside of this network(the Internet). And if it has such functionality(usually as a single on-off switch), may open up the whole network to public access, then you can just access each devices thru their designated IPV6 address, and no need for the VPN in discussion. Furthermore, IPV6 in my opinion is less secured than IPV4, because we need to secure every service of every device, which is a very complex  and time-consuming project than relying on some router device's IPV4 NAT(so if the hacker can't break thru the router, he can't access the devices behind the router directly). 
- Setup DDNS for it(optional). 
- Configure port forwarding for the SSH service of the running Linux server(in my case it's a raspberry pi). 
- For a Windows client(a laptop which I use to connect back to home network from outside via Internet), install SSTap(there may be many 替代产品). 
- Some trivial config to the SSHD service, to avoid it dropping connection to client too soon.
  - As for the config of SSHD service, copy the following lines to /etc/ssh/sshd/config file, and do a 'sudo service sshd restart', then reconnect the SSH client to make to modifications take effect.

## Pros
### Convenience
- compared to proprietary network devices' VPN service setup, or other software service counterparts, just the Windows VPN client configuration by itself is unfriendly to new users, for example those diverse combinations of different encryption options

### Security
- regarding to all traffic are encrypted thru SSH, the encryption of SSH should be trustworthy, otherwise it won't be the de-facto standard of connecting Linux terminals. So we don't need to worry about if there're any protocols in use that are unencrypted or easily breakable
- since the most stringent network censorship system still needs to allow SSH protocol to pass thru, to ensure basic network management and operation, standard VPN is known to behave very bad to this concern, since it usually uses standardized port number and has very significant 明显的 近乎透明的 报文特征, is easily banned by the network infrastructure 网络审查机构

## Cons

## Conclusions