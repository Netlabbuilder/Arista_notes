- To configure an IP addresses on an interface, this interface must be configured as a routed port:

  - Default configuration on interface Ethernet1:
    
    ```
    ceos1#show run int ethernet 1
    interface Ethernet1
    
    ceos1#show int status
    Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
    Et1               connected    1        full   1G     EbraTestPhyPort
    Ma0               connected    routed   a-full a-1G   10/100/1000
    
    ceos1#show int ethernet 1
    Ethernet1 is up, line protocol is up (connected)
      Hardware is Ethernet, address is aac1.abb2.d13a (bia aac1.abb2.d13a)
      Ethernet MTU 9214 bytes, BW 1000000 kbit
      Full-duplex, 1Gb/s, auto negotiation: off, uni-link: n/a
      Up 1 hour, 23 minutes, 9 seconds
      Loopback Mode : None
      2 link status changes since last clear
      Last clearing of "show interface" counters never
      5 minutes input rate 61 bps (0.0% with framing overhead), 0 packets/sec
      5 minutes output rate 0 bps (0.0% with framing overhead), 0 packets/sec
         169 packets input, 39877 bytes
         Received 0 broadcasts, 169 multicast
         0 runts, 0 giants
         0 input errors, 0 CRC, 0 alignment, 0 symbol, 0 input discards
         0 PAUSE input
         0 packets output, 0 bytes
         Sent 0 broadcasts, 0 multicast
         0 output errors, 0 collisions
         0 late collision, 0 deferred, 0 output discards
         0 PAUSE output
    ```
  - Configure interface Ethernet1 as a routed port (using `no switchport`) with an IPv4 IP address:
    ```
    ceos1#conf t
    ceos1(config)#interface ethernet 1
    ceos1(config-if-Et1)#no switchport
    ceos1(config-if-Et1)#ip address 10.0.0.1 255.255.255.252
    ceos1(config-if-Et1)#end
    ceos1#
    
    ceos1#show run int ethernet 1
    interface Ethernet1
       no switchport
       ip address 10.0.0.1/30
    
    ceos1#show int status
    Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
    Et1               connected    routed   full   1G     EbraTestPhyPort
    Ma0               connected    routed   a-full a-1G   10/100/1000
    
    ceos1#show int ethernet 1
    Ethernet1 is up, line protocol is up (connected)
      Hardware is Ethernet, address is aac1.abb2.d13a (bia aac1.abb2.d13a)
      Internet address is 10.0.0.1/30
      Broadcast address is 255.255.255.255
      IP MTU 1500 bytes (default), BW 1000000 kbit
      Full-duplex, 1Gb/s, auto negotiation: off, uni-link: n/a
      Up 1 hour, 30 minutes, 32 seconds
      Loopback Mode : None
      2 link status changes since last clear
      Last clearing of "show interface" counters never
      5 minutes input rate 333 bps (0.0% with framing overhead), 0 packets/sec
      5 minutes output rate 0 bps (0.0% with framing overhead), 0 packets/sec
         306 packets input, 58453 bytes
         Received 0 broadcasts, 306 multicast
         0 runts, 0 giants
         0 input errors, 0 CRC, 0 alignment, 0 symbol, 0 input discards
         0 PAUSE input
         0 packets output, 0 bytes
         Sent 0 broadcasts, 0 multicast
         0 output errors, 0 collisions
         0 late collision, 0 deferred, 0 output discards
         0 PAUSE output
    ceos1#
    ```
  - When we configure an IP address on a non-routed port interface, the IP address will be ignored. Although the IP address is shown in the running configuration, it is not applied when we check the interface status or details:
    ```
    ceos2#show int status
    Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
    Et1               connected    1        full   1G     EbraTestPhyPort
    Ma0               connected    routed   a-full a-1G   10/100/1000
    
    ceos2#conf t
    ceos2(config)#int ethernet 1
    ceos2(config-if-Et1)#ip address 10.0.0.2 255.255.255.252
    ! IP configuration will be ignored while interface Ethernet1 is not a routed port.
    ceos2(config-if-Et1)#end
    
    ceos2#show run int eth1
    interface Ethernet1
       ip address 10.0.0.2/30
    ceos2#
    
    ceos2#show int status
    Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
    Et1               connected    1        full   1G     EbraTestPhyPort
    Ma0               connected    routed   a-full a-1G   10/100/1000
    
    ceos2#show int ethernet 1
    Ethernet1 is up, line protocol is up (connected)
      Hardware is Ethernet, address is aac1.ab09.2c3c (bia aac1.ab09.2c3c)
      Ethernet MTU 9214 bytes, BW 1000000 kbit
      Full-duplex, 1Gb/s, auto negotiation: off, uni-link: n/a
      Up 1 hour, 38 minutes, 25 seconds
      Loopback Mode : None
      2 link status changes since last clear
      Last clearing of "show interface" counters never
      5 minutes input rate 106 bps (0.0% with framing overhead), 0 packets/sec
      5 minutes output rate 0 bps (0.0% with framing overhead), 0 packets/sec
         2724 packets input, 358052 bytes
         Received 0 broadcasts, 2724 multicast
         0 runts, 0 giants
         0 input errors, 0 CRC, 0 alignment, 0 symbol, 0 input discards
         0 PAUSE input
         0 packets output, 0 bytes
         Sent 0 broadcasts, 0 multicast
         0 output errors, 0 collisions
         0 late collision, 0 deferred, 0 output discards
         0 PAUSE output
    ```
  - To make the IP address applied on the interface, we can apply `no switchport` command on that interface:
    ```
    ceos2(config)#int ethernet 1
    ceos2(config-if-Et1)#no switchport
    ceos2(config-if-Et1)#end
    
    ceos2#show int status
    Port       Name   Status       Vlan     Duplex Speed  Type            Flags Encapsulation
    Et1               connected    routed   full   1G     EbraTestPhyPort
    Ma0               connected    routed   a-full a-1G   10/100/1000
    
    ceos2#show ip int brief
                                                                                  Address
    Interface         IP Address           Status       Protocol           MTU    Owner
    ----------------- -------------------- ------------ -------------- ---------- -------
    Ethernet1         10.0.0.2/30          up           up                1500
    Management0       172.20.20.3/24       up           up                1500
    
    ceos2#
    ```
