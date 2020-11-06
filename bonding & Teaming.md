# Bonding & Teaming (Virtual Network Interface) #

## How to create a bonding interface? ##

<Test: bond1 on machines 64,65 using eth1+eth2 in active-backup mode (centos 6)>

1. Create the configuration file for the first bond interface(bondname)
```
[root ~]# touch /etc/sysconfig/network-scripts/ifcfg-bond0
[root ~]# vi /etc/sysconfig/network-scripts/ifcfg-bond0

DEVICE=bond0
TYPE=Bond
BONDING_MASTER=yes
IPADDR=10.0.0.64
NETMASK=255.255.255.0
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
NAME=bond0
BONDING_OPTS="downdelay=0 miimon=100 mode=active-backup updelay=0"
```
-> Bondname can be anything you want. I just named bond0

2. Configure slave 1
```
[root ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
MASTER=bond0
SLAVE=yes

Ifup ifcfg-eth1
```
-> Don't forget to active the interface, after configuring a bonding network

3. Configure slave 2
```
[root ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth2
DEVICE=eth2
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
MASTER=bond0
SLAVE=yes

[root ~]# ifup ifcfg-eth2
bond0: Adding slave eth2
bond0: Enslaving eth2 as an active interface with an up link
```
-> Don't forget to active the interface, after configuring a bonding network

4. Check the bonding configuration 
```
[root ~]# cat /proc/net/bonding/bond0
Bonding Mode: fault-tolerance (active-backup)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: Unknown
Duplex: Unknown
Link Failure Count: 0
Permanent HW addr: 52:54:00:dd:8c:85
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: Unknown
Duplex: Unknown
Link Failure Count: 0
Permanent HW addr: 52:54:00:30:59:65
Slave queue ID: 0
```
-> Check the configuration on both machines. If you have several machines then you should check the configuration on 
each machines.

5. Check the network configuration
```
[root ~]# ip a
3: eth1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master bond0 state UP qlen 1000
    link/ether 52:54:00:dd:8c:85 brd ff:ff:ff:ff:ff:ff
4: eth2: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master bond0 state UP qlen 1000
    link/ether 52:54:00:dd:8c:85 brd ff:ff:ff:ff:ff:ff
5: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
    link/ether 52:54:00:dd:8c:85 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.64/24 brd 10.0.0.255 scope global bond0
    inet6 fe80::5054:ff:fedd:8c85/64 scope link valid_lft forever preferred_lft forever
```
-> Bonding has been configured well

6. Check if the network works fine
```
[root 64~]# ping 10.0.0.65
PING 10.0.0.65 (10.0.0.65) 56(84) bytes of data.
64 bytes from 10.0.0.65: icmp_seq=1 ttl=64 time=0.144 ms
64 bytes from 10.0.0.65: icmp_seq=2 ttl=64 time=0.279 ms

[root 65~]# ping 10.0.0.64
PING 10.0.0.64 (10.0.0.64) 56(84) bytes of data.
64 bytes from 10.0.0.64: icmp_seq=1 ttl=64 time=0.620 ms
64 bytes from 10.0.0.64: icmp_seq=2 ttl=64 time=0.254 ms
```

<How to set/change an active slave in a bonding system>

```
[root~]# ifenslave -c [bondname][interface name]
[90745.912467] Bond0: making interface eth2 the new active one
```
-> How to change an active slave to a different one
-> The interface name should be the one you want to set as an active slave in a bonding system

```
[root~]# cat /proc/net/bonding/[bondname]

Bonding Mode: fault-tolerance (active-backup)
Primary Slave: None
Currently Active Slave: eth2
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: Unknown
Duplex: Unknown
Link Failure Count: 0
Permanent HW addr: 52:54:00:c5:4d:af
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: Unknown
Duplex: Unknown
Link Failure Count: 0
Permanent HW addr: 52:54:00:93:83:91
Slave queue ID: 0
```
-> The active slave is changed from eth1 to eth2 

## How to create a teaming interface? ##

<Test: Configure team2 on machines 68,69 using ens8+ens9 in activebackup mode (centos 8)>

1. Configure teaming on machines 

```
[root~]nmtui
```

1. Edit a connection
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


2. Add Team
                          ┌───────────────────────────┐
                          │                           │
                          │ ┌─────────────┐           │
                          │ │ Ethernet  ↑ │ <Add>     │
                          │ │   eth0    ▒ │           │
         ┌──────────────────────┤ New Connection ├──────────────────────┐
         │                                                              │
         │ Select the type of connection you wish to create.            │
         │                                                              │
         │                        Wi-Fi       ↑                         │
         │                        Bond        ▒                         │
         │                        Bridge      ▮                         │
         │                        IP tunnel   ▒                         │
         │                        Team        ↓                         │
         │                                                              │
         │                                            <Cancel> <Create> │
         │                                                              │
         └──────────────────────────────────────────────────────────────┘

3. Edit profile name and device name
   ┌───────────────────────────┤ Edit Connection ├───────────────────────────┐
   │                                                                        ↑│
   │         Profile name team0___________________________________          ▮│
   │               Device team0___________________________________          ▒│
   │                                                                        ▒│
   │ ╤ TEAM                                                        <Hide>   ▒│
   │ │ Slaves                                                               ▒│
   │ │ ┌────────────────────────────────────────────────────┐               ▒│
   │ │ │                                                  ↑ │ <Add>         ▒│
   │ │ │                                                  ▮ │               ▒│
   │ │ │                                                  ▒ │ <Edit...>     ▒│
   │ │ │                                                  ▒ │               ▒│
   │ │ │                                                  ▒ │ <Delete>      ▒│
   │ │ │                                                  ↓ │               ▒│
   │ │ └────────────────────────────────────────────────────┘               ▒│
   
   
4.Add team device's slaves
   ┌───────────────────────────┤ Edit Connection ├───────────────────────────┐
   │                                                                        ↑│
   │         Profile name team0___________________________________          ▮│
   │               Device team0___________________________________          ▒│
   │                                                                        ▒│
   │ ╤ TE┌──────────────────────┤ New Connection ├──────────────────────┐   ▒│
   │ │ Sl│                                                              │   ▒│
   │ │ ┌─│ Select the type of slave connection you wish to add.         │   ▒│
   │ │ │ │                                                              │   ▒│
   │ │ │ │                        Ethernet    ↑                         │   ▒│
   │ │ │ │                        InfiniBand  ▮                         │   ▒│
   │ │ │ │                        Wi-Fi       ▒                         │   ▒│
   │ │ │ │                        VLAN        ▒                         │   ▒│
   │ │ │ │                                    ↓                         │   ▒│
   │ │ └─│                                                              │   ▒│
   │ │   │                                            <Cancel> <Create> │   ▒│
   │ │ JS│                                                              │   ▒│
   │ │   └──────────────────────────────────────────────────────────────┘   ▒│
   
5.Edit slave device's name
   ┌─┌─────────────────────────┤ Edit Connection ├──────────────────────────┐
   │ │                                                                      │
   │ │         Profile name ens9____________________________________        │
   │ │               Device ens9____________________________________        │
   │ │                                                                      │
   │ │ ═ ETHERNET                                                    <Show> │
   │ │                                                                      │
   │ │ ╤ TEAM PORT                                                   <Hide> │
   │ │ │ JSON configuration                                                 │
   │ │ │                                                                    │
   │ │ │                                                                    │
   │ │ │ <Edit...>                                                          │
   │ │ └                                                                    │
   │ │ [X] Automatically connect                                            │
   │ │ [X] Available to all users                                           │
   │ │                                                                      │
   │ │                                                        <Cancel> <OK> │
   │ │                                                                      │
   │ │                                                                      │
   │ │                                                                      │
   │ │                                                                      │
   └─└──────────────────────────────────────────────────────────────────────┘
-> Do the same thing on the other slave device

6. Edit JSON configuration
   ┌───────────────────────────┤ Edit Connection ├───────────────────────────┐
   │                                                                        ↑│
   │ ╤ TEAM                                                        <Hide>   ▒│
   │ │ Slaves                                                               ▒│
   │ │ ┌────────────────────────────────────────────────────┐               ▒│
   │ │ │ ens9                                             ↑ │ <Add>         ▒│
   │ │ │ ens10                                            ▒ │               ▒│
   │ │ │                                                  ▒ │ <Edit...>     ▮│
   │ │ │                                                  ▒ │               ▒│
   │ │ │                                                  ▮ │ <Delete>      ▒│
   │ │ │                                                  ↓ │               ▒│
   │ │ └────────────────────────────────────────────────────┘               ▒│
   │ │                                                                      ▒│
   │ │ JSON configuration                                                   ▒│
   │ │                                                                      ▒│
   │ │                                                                      ▒│
   │ │ <Edit...>                                                            ▒│
   │ └                                                                      ▒│
   │                                                                        ▒│
   │ ═ IPv4 CONFIGURATION <Automatic>                              <Show>   ▒│
   │                                                                        ↓│
   └─────────────────────────────────────────────────────────────────────────┘

7. Write the JSON code
{"runner": {"name": "activebackup"}}
~
~
~
:wq

8.Save the setting
   ┌───────────────────────────┤ Edit Connection ├───────────────────────────┐
   │                                                                        ↑│
   │         Profile name team0___________________________________          ▮│
   │               Device team0___________________________________          ▒│
   │                                                                        ▒│
   │ ╤ TEAM                                                        <Hide>   ▒│
   │ │ Slaves                                                               ▒│
   │ │ ┌────────────────────────────────────────────────────┐               ▒│
   │ │ │ ens9                                             ↑ │ <Add>         ▒│
   │ │ │ ens9                                             ▒ │               ▒│
   │ │ │                                                  ▒ │ <Edit...>     ▒│
   │ │ │                                                  ▒ │               ▒│
   │ │ │                                                  ▮ │ <Delete>      ▒│
   │ │ │                                                  ↓ │               ▒│
   │ │ └────────────────────────────────────────────────────┘               ▒│
   │ │                                                                      ▒│
   │ │ JSON configuration                                                   ▒│
   │ │   {"runner": {"name": "activebackup"}}                               ▒│
   │ │                                                                      ▒│
   │ │ <Edit...>                                                            ▒│
   │                                                                        ↓│
   └─────────────────────────────────────────────────────────────────────────┘

9. Activate team device

                       ┌──────────────────────────────────┐
                       │                                  │
                       │ ┌─────────────────┐              │
                       │ │ Wired         ↑ │ <Deactivate> │
                       │ │ * eth0        ▒ │              │
                       │ │               ▒ │              │
                       │ │ Team (team0)  ▒ │              │
                       │ │ * team0       ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▒ │              │
                       │ │               ▮ │              │
                       │ │               ↓ │ <Back>       │
                       │ └─────────────────┘              │
                       │                                  │
                       └──────────────────────────────────┘
10. Check the state 
```
teamdctl team0 state
```

11. Check the teaming interface
```
[root ~]# cd /etc/sysconfig/network-scripts/
[root network-scripts]# cat ifcfg-eth1
NAME=eth1
UUID=b8b26227-7976-4b88-b48a-b82037af0f62
DEVICE=eth1
ONBOOT=yes
TEAM_MASTER=team0
DEVICETYPE=TeamPort
TEAM_MASTER_UUID=8c14ea35-1fe9-4865-a955-5909cab49bef
```

12. Check the interface
```
[root network-scripts]# cat ifcfg-eth2
NAME=eth2
UUID=a6a6d77d-3518-485c-9907-8255107790d2
DEVICE=eth2
ONBOOT=yes
TEAM_MASTER=team0
DEVICETYPE=TeamPort
TEAM_MASTER_UUID=8c14ea35-1fe9-4865-a955-5909cab49bef
```

13. Check the interface
```
[root@fastvm-centos-7-8-67 network-scripts]# cat ifcfg-team0
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
IPADDR=10.0.0.67
PREFIX=24
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
NAME=team0
UUID=8c14ea35-1fe9-4865-a955-5909cab49bef
DEVICE=team0
ONBOOT=yes
DEVICETYPE=Team
TEAM_CONFIG=$'{\"runner\": {\"name\": \"activebackup\"}}\n'
```

