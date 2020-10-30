# How to configure network in RHEL version 6, 7 and 8 #

### How to configure network in RHEL version 6? ###

 ***RHEL6 doesn't have NetworkManager service***

In EL 6, you should create a network configuration file manually and type commands like this:

```
vi /etc/sysconfig/network-scripts/ifcfg-[interface name]

Ex)
Device="eth1"
BOOTPROTO="none"
ONBOOT="yes"
TYPE="Ethernet"
IPADDR=10.x.x.x
NETMASK=255.x.x.x
```

```
ifup ifcfg-[interface name]
```
=> Activate the interface

```
ip a
```
=> Checking if the interface has the right IP address


### How to add DNS server in network configuration file in RHEL 6? ### 

```
vi /etc/sysconfig/network-scripts/ifcfg-[interface name]

Ex)
DEVICE="eth1"
BOOTPROTO="none"
ONBOOT="yes"
IPADDR=10.3.0.57
NETMASK=255.255.255.0
GATEWAY=10.3.0.56 
DNS=8.8.8.8 
```
=> The interface will find where the existing DNS server is 

=> Multiple DNS can be added with numeric numbers
Ex) 
DNS1=8.8.8.8 
DNS2=8.8.4.4
DNS3=1.1.1.1

```
cat /etc/resolv.conf
nameserver 8.8.8.8
```
=> For checking, what DNS server the system is using now

**less /usr/share/doc/initscripts-9.003.61/sysconfig.txt**

-> Help page for ifcfg-[interface name] & route-[interface name]

-> Help page for configuration file of networking (ip address, gateway...)


### How to configure network in RHEL version 7? ###

 ***In EL 7, You can configure the network either manually like version 6 or `nmtui` command***
 
 
 1. Click Edit a connection
 
                           ┌─┤ NetworkManager TUI ├──┐
                           │                         │
                           │ Please select an option │
                           │                         │
                           │ Edit a connection       │
                           │ Activate a connection   │
                           │ Set system hostname     │
                           │                         │
                           │ Quit                    │
                           │                         │
                           │                    <OK> │
                           │                         │
                           └─────────────────────────┘


2. Click Add button 

                          │                           │
                          │ ┌─────────────┐           │
                          │ │ Ethernet  ↑ │ <Add>     │
                          │ │   eth0    ▒ │           │
                          │ │           ▒ │ <Edit...> │
                          │ │           ▒ │           │
                          │ │           ▒ │ <Delete>  │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▒ │           │
                          │ │           ▮ │           │
                          │ │           ↓ │ <Back>    │
                          │ └─────────────┘           │
                          │                           │
                          └───────────────────────────┘
 
3. Choose type
              
                          │ ┌─────────────┐           │
                          │ │ Ethernet  ↑ │ <Add>     │
                          │ │   eth0    ▒ │           │
         ┌──────────────────────┤ New Connection ├──────────────────────┐
         │                                                              │
         │ Select the type of connection you wish to create.            │
         │                                                              │
         │                        DSL         ↑                         │
         │                        Ethernet    ▮                         │
         │                        InfiniBand  ▒                         │
         │                        Wi-Fi       ▒                         │
         │                        Bond        ↓                         │
         │                                                              │
         │                                            <Cancel> <Create> │
         │                                                              │
         └──────────────────────────────────────────────────────────────┘

                          │ └─────────────┘           │
                          │                           │


4. Name the interface name
```
   ┌───────────────────────────┤ Edit Connection ├───────────────────────────┐
   │                                                                        ↑│
   │         Profile name eth1____________________________________          ▮│
   │               Device eth1____________________________________          ▒│
   │                                                                        ▒│
   │ ═ ETHERNET                                                    <Show>   ▒│
   │                                                                        ▒│
   │ = IPv4 CONFIGURATION <Manual>                                 <Hide>   ▒│
   │            Addresses 100.10.10.10_____________ <Remove>                ▒│
   │                      <Add...>                                          ▒│
```  
  
-> You can set any name you want on Profile name but the Device name must be existed in real

-> Save and go back to the first option

5. Click Activate a connection

                           ┌─┤ NetworkManager TUI ├──┐
                           │                         │
                           │ Please select an option │
                           │                         │
                           │ Edit a connection       │
                           │ Activate a connection   │
                           │ Set system hostname     │
                           │                         │
                           │ Quit                    │
                           │                         │
                           │                    <OK> │
                           │                         │
                           └─────────────────────────┘
                           

6. Enter on the interface you just added to activate the interface

                          │                            │
                          │ ┌───────────┐              │
                          │ │ Wired   ↑ │ <Deactivate> │
                          │ │ * eth0  ▒ │              │
                          │ │   eth1  ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▮ │              │
                          │ │         ↓ │ <Back>       │
                          │ └───────────┘              │
                          │                            │
                          └────────────────────────────┘

7. After then, you will see the asterisk mark is on the interface you just activated(* = activated)
 
                          │ ┌───────────┐              │
                          │ │ Wired   ↑ │ <Deactivate> │
                          │ │   eth0  ▒ │              │
                          │ │ * eth1  ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▒ │              │
                          │ │         ▮ │              │
                          │ │         ↓ │ <Back>       │
                          │ └───────────┘              │
                          │                            │
                          └────────────────────────────┘

8.

```
ip a
```
-> Checking if the address and interface you set has been configured correctly



### How to configure network in RHEL version 8? ###






