# How to add network interface on vm? #

1. Stop the vm
```
sapark ~ $ fast-vm stop 103
setlocale: No such file or directory
[103][inf] Stopping VM fastvm-centos-8.2-103 now
setlocale: No such file or directory
Domain fastvm-centos-8.2-103 destroyed
```

2. Figure out the name of the vm, using command 'fast-vm info [vm name]'
```
sapark ~ $ fast-vm info 103
setlocale: No such file or directory
[103][inf] IP: 192.168.2.103
[103][inf] vm_name: fastvm-centos-8.2-103
```

3. Edit the vm configuration using command 'virsh edit [vm name]'
```
sapark ~ $ virsh edit fastvm-centos-8.2-103
```

4. Find 'Network' section using '/' option and add the code to add an empty network interface for a test
```
<interface type='network'>
<source network='empty'/>
<model type='virtio'/>
</interface>
```
=> After saving the configuration file, you can open it again and you can see the network interface got a MAC address automatically

```
<interface type='network'>
-> <mac address='52:54:00:bd:d3:72'/>
<source network='empty'/>
<model type='virtio'/>
-> <address type='pci' domain='0x0000' bus='0x00' slot='0x08' function='0x0'/>
```

5. Save the configuration and start the vm
```
sapark@sobo ~ $ fast-vm start 103
```

6. Check if the network interfaces got added
```
[root@fastvm-centos-8-2-103 ~]# ip ad
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:1b:15:3e brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.103/24 brd 192.168.2.255 scope global dynamic noprefixroute ens2
       valid_lft 3580sec preferred_lft 3580sec
    inet6 fe80::36fe:3a7f:f55b:380a/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
-> 3: ens8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:bd:d3:72 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::397f:f594:83b1:bc00/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
-> 4: ens9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:52:b0:34 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::f411:dabf:6069:8526/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
-> You can see that the two empty network interfaces added (ens8, 9)
