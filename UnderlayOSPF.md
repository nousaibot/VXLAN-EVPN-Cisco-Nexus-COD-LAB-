### Настройка Uderlay OSPF

---

### Подсети
| Подсеть  | Префикс  | Роль |
|:------------ |:-------:|:-------:|
|10.10.0.0/16|/31|p2p Core to Spine|
|10.11.0.0/16|/31|p2p Spine to Leaf|
|10.0.0.0/16|/32|loopback Core|
|10.1.0.0/16|/32|loopback Sipne|
|10.2.0.0/16|/32|loopback Leaf|
---
![image](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/IMG/UnderlayOSPF.PNG)

---

###Core
|address |interface|peer
|:---------------:|:-----:|:----:|
|10.10.0.0/31|ether1/1|Spine1|
|10.10.0.2/31|ether1/2|Spine2|
|10.10.0.4/31|ether1/3|Spine3|
|10.0.0.1/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.0.1 255.255.255.255
 ip ospf 1 area 0
!
interface Ethernet0/1
 description Spine1
 ip address 10.10.0.0 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
!
interface Ethernet0/2
 description Spine2
 ip address 10.10.0.2 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
!
interface Ethernet0/3
 description Spine3
 ip address 10.10.0.4 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 1
!
router ospf 1
 router-id 10.0.0.1
 passive-interface default
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
 </code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Core#sh ip ospf interface brief
Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
Lo0          1     0               10.0.0.1/32        1     LOOP  0/0
Et0/2        1     0               10.10.0.2/31       10    P2P   1/1
Et0/1        1     0               10.10.0.0/31       10    P2P   1/1
Et0/3        1     1               10.10.0.4/31       10    P2P   1/1
</code></pre>
<pre><code>
Core#sh ip ospf neighbor
Neighbor ID     Pri   State           Dead Time   Address         Interface
10.1.0.2          0   FULL/  -        00:00:38    10.10.0.3       Ethernet0/2
10.1.0.1          0   FULL/  -        00:00:31    10.10.0.1       Ethernet0/1
10.1.0.3          0   FULL/  -        00:00:35    10.10.0.5       Ethernet0/3
</code></pre>
<pre><code>
Core#sh ip ospf database
            OSPF Router with ID (10.0.0.1) (Process ID 1)
                Router Link States (Area 0)
Link ID         ADV Router      Age         Seq#       Checksum Link count
10.0.0.1        10.0.0.1        1749        0x800000E8 0x0094C3 5
10.1.0.1        10.1.0.1        1606        0x80000007 0x00DAAE 9
10.1.0.2        10.1.0.2        1606        0x80000007 0x000E50 9
10.2.0.1        10.2.0.1        1647        0x80000004 0x00D2FE 5
10.2.0.2        10.2.0.2        1624        0x80000004 0x001EA8 5
10.2.0.3        10.2.0.3        1608        0x80000005 0x006753 5
                Summary Net Link States (Area 0)
Link ID         ADV Router      Age         Seq#       Checksum
10.1.0.3        10.0.0.1        1427        0x80000001 0x00EA2E
10.2.0.4        10.0.0.1        1405        0x80000001 0x006688
10.10.0.4       10.0.0.1        1879        0x80000001 0x0064AC
10.11.0.12      10.0.0.1        1415        0x80000001 0x009946
                Router Link States (Area 1)
Link ID         ADV Router      Age         Seq#       Checksum Link count
10.0.0.1        10.0.0.1        1442        0x80000002 0x00813A 2
10.1.0.3        10.1.0.3        1416        0x80000006 0x005465 5
10.2.0.4        10.2.0.4        1418        0x80000005 0x008AD4 3
                Summary Net Link States (Area 1)
Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.1        10.0.0.1        1883        0x80000002 0x00A480
10.1.0.1        10.0.0.1        1774        0x80000001 0x00FE1C
10.1.0.2        10.0.0.1        1745        0x80000001 0x00F425
10.2.0.1        10.0.0.1        1644        0x80000001 0x00846D
10.2.0.2        10.0.0.1        1623        0x80000001 0x007A76
10.2.0.3        10.0.0.1        1590        0x80000001 0x00707F
10.10.0.0       10.0.0.1        1883        0x80000002 0x008A89
10.10.0.2       10.0.0.1        1883        0x80000002 0x00769B
10.11.0.0       10.0.0.1        1774        0x80000001 0x0012D9
10.11.0.2       10.0.0.1        1774        0x80000001 0x00FDEB
10.11.0.4       10.0.0.1        1774        0x80000001 0x00E9FD
10.11.0.6       10.0.0.1        1745        0x80000001 0x00D510
10.11.0.8       10.0.0.1        1745        0x80000001 0x00C122
10.11.0.10      10.0.0.1        1745        0x80000001 0x00AD34
</code></pre>
</details>

<details>
  <summary>`Ping Traceroute test`</summary>
<pre><code>
Core#ping 10.1.0.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.0.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
Core#
Core#ping 10.1.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
Core#
Core#ping 10.1.0.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.0.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
Core#
Core#ping 10.2.0.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms
Core#ping 10.2.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms
Core#ping 10.2.0.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms
Core#ping 10.2.0.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.4, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/4/11 ms
-------------------------------------------------------------

![image](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/IMG/CoreTraceroute.png)

Dump [interface Ethernet0/1](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/Dump/CoreInterface0-1.pcapng)

Dump [interface Ethernet0/2](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/Dump/CoreInterface0-2.pcapng)

</code></pre>
</details>

---

###Spine1
 | address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.0/31|ether1/1|Leaf1|
|10.11.0.2/31|ether1/2|Leaf2|
|10.11.0.4/31|ether1/3|Leaf3|
|10.10.0.1/31|ether1/7|Core|
|10.1.0.1/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Leaf1
  ip address 10.11.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.2/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.4/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.1/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.1.0.1/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 10.1.0.1
  passive-interface default
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Spine1# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 5
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/2                  3      0.0.0.0         40     P2P      1         up  
 Eth1/3                  4      0.0.0.0         40     P2P      1         up  
 Eth1/7                  5      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Spine1# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 4
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.1          1 FULL/ -          00:46:26 10.11.0.1       Eth1/1 
 10.2.0.2          1 FULL/ -          00:46:00 10.11.0.3       Eth1/2 
 10.2.0.3          1 FULL/ -          00:45:42 10.11.0.5       Eth1/3 
 10.0.0.1          1 FULL/ -          00:48:36 10.10.0.0       Eth1/7 
</code></pre>
<pre><code>
Spine1# sh ip ospf database
        OSPF Router with ID (10.1.0.1) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1005       0x800000e9 0x92c4   5   
10.1.0.1        10.1.0.1        923        0x80000008 0xd8af   9   
10.1.0.2        10.1.0.2        932        0x80000008 0x0c51   9   
10.2.0.1        10.2.0.1        969        0x80000005 0xd0ff   5   
10.2.0.2        10.2.0.2        942        0x80000005 0x1ca9   5   
10.2.0.3        10.2.0.3        931        0x80000006 0x6554   5   
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.3        10.0.0.1        751        0x80000002 0xe82f
10.2.0.4        10.0.0.1        508        0x80000002 0x6489
10.10.0.4       10.0.0.1        1006       0x80000002 0x62ad
10.11.0.12      10.0.0.1        751        0x80000002 0x9747
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Spine1# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=254 time=2.061 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=254 time=1.546 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=254 time=1.578 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=254 time=1.315 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=254 time=1.811 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 1.315/1.662/2.061 ms
Spine1# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=253 time=3.493 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=253 time=2.637 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=253 time=2.703 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=253 time=2.671 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=253 time=2.727 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.637/2.846/3.493 ms
Spine1# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=253 time=4.326 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=253 time=2.608 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=253 time=2.468 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=253 time=2.464 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=253 time=2.932 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.464/2.959/4.326 ms
Spine1# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=254 time=3.862 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=254 time=2.776 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=254 time=2.775 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=254 time=2.829 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=254 time=3.167 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.775/3.081/3.862 ms
Spine1# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=254 time=3.985 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=254 time=3.319 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=254 time=3.546 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=254 time=3.055 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=254 time=3.372 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.055/3.455/3.985 ms
Spine1# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=254 time=3.891 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=254 time=3.494 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=254 time=3.203 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=254 time=3.56 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=254 time=3.451 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.203/3.519/3.891 ms
Spine1# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=252 time=6.987 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=252 time=4.762 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=252 time=4.958 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=252 time=4.527 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=252 time=4.761 ms
--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.527/5.199/6.987 ms
</code></pre>
</details>

---

###Spine2
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.6/31|ether1/1|Leaf1|
|10.11.0.8/31|ether1/2|Leaf2|
|10.11.0.10/31|ether1/3|Leaf3|
|10.10.0.3/31|ether1/7|Core|
|10.1.0.2/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Leaf1
  ip address 10.11.0.6/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.8/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.10/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/7
  description Core
  no switchport
  ip address 10.10.0.3/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.1.0.2/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 10.1.0.2
  passive-interface default
</code></pre>
</details>
  
<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Spine2# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 5
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/2                  3      0.0.0.0         40     P2P      1         up  
 Eth1/3                  4      0.0.0.0         40     P2P      1         up  
 Eth1/7                  5      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Spine2# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 4
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.1          1 FULL/ -          01:00:41 10.11.0.7       Eth1/1 
 10.2.0.2          1 FULL/ -          01:00:24 10.11.0.9       Eth1/2 
 10.2.0.3          1 FULL/ -          01:00:01 10.11.0.11      Eth1/3 
 10.0.0.1          1 FULL/ -          01:02:26 10.10.0.2       Eth1/7 
</code></pre>
<pre><code>
Spine2# sh ip ospf database
        OSPF Router with ID (10.1.0.2) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1864       0x800000e9 0x92c4   5   
10.1.0.1        10.1.0.1        1783       0x80000008 0xd8af   9   
10.1.0.2        10.1.0.2        1788       0x80000008 0x0c51   9   
10.2.0.1        10.2.0.1        6          0x80000006 0xce01   5   
10.2.0.2        10.2.0.2        1800       0x80000005 0x1ca9   5   
10.2.0.3        10.2.0.3        1790       0x80000006 0x6554   5   
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.3        10.0.0.1        1609       0x80000002 0xe82f
10.2.0.4        10.0.0.1        1366       0x80000002 0x6489
10.10.0.4       10.0.0.1        1864       0x80000002 0x62ad
10.11.0.12      10.0.0.1        1609       0x80000002 0x9747
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Spine2# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=254 time=3.006 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=254 time=2.726 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=254 time=1.942 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=254 time=1.826 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=254 time=1.61 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 1.61/2.221/3.006 ms
Spine2# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=253 time=3.975 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=253 time=2.91 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=253 time=2.937 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=253 time=2.962 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=253 time=2.813 ms
--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.813/3.119/3.975 ms
Spine2# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=253 time=3.806 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=253 time=2.685 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=253 time=2.533 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=253 time=2.824 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=253 time=3.682 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.533/3.105/3.806 ms
Spine2# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=254 time=4.156 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=254 time=2.357 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=254 time=2.461 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=254 time=2.651 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=254 time=2.492 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.357/2.823/4.156 ms
Spine2# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=254 time=3.013 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=254 time=4.148 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=254 time=1.922 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=254 time=2.775 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=254 time=2.95 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 1.922/2.961/4.148 ms
Spine2# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=254 time=2.98 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=254 time=3.18 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=254 time=3.124 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=254 time=3.26 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=254 time=3.071 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.98/3.123/3.26 ms
Spine2# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=252 time=5.92 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=252 time=4.636 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=252 time=4.249 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=252 time=4.637 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=252 time=4.879 ms
--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.249/4.864/5.92 ms
</code></pre>
</details>

---

###Spine3
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.12/31|ether1/1|Leaf4|
|10.10.0.5/31|ether1/7|Core|
|10.1.0.3/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Leaf4
  ip address 10.11.0.12/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.5/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface loopback0
  ip address 10.1.0.3/32
  ip router ospf 1 area 0.0.0.1
!
router ospf 1
  router-id 10.1.0.3
  passive-interface default
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Spine3# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  3      0.0.0.1         40     P2P      1         up  
 Eth1/7                  2      0.0.0.1         40     P2P      1         up  
 Lo0                     1      0.0.0.1         1      LOOPBACK 0         up  
</code></pre>
<pre><code> 
Spine3# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.4          1 FULL/ -          01:04:57 10.11.0.13      Eth1/1 
 10.0.0.1          1 FULL/ -          01:05:23 10.10.0.4       Eth1/7 
</code></pre>
<pre><code>
Spine3# sh ip ospf database
        OSPF Router with ID (10.1.0.3) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        95         0x80000004 0x7d3c   2   
10.1.0.3        10.1.0.3        256        0x80000008 0x5067   5   
10.2.0.4        10.2.0.4        262        0x80000007 0x86d6   3   
                Summary Network Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum 
10.0.0.1        10.0.0.1        348        0x80000004 0xa082
10.1.0.1        10.0.0.1        348        0x80000003 0xfa1e
10.1.0.2        10.0.0.1        348        0x80000003 0xf027
10.2.0.1        10.0.0.1        95         0x80000003 0x806f
10.2.0.2        10.0.0.1        95         0x80000003 0x7678
10.2.0.3        10.0.0.1        95         0x80000003 0x6c81
10.10.0.0       10.0.0.1        348        0x80000004 0x868b
10.10.0.2       10.0.0.1        348        0x80000004 0x729d
10.11.0.0       10.0.0.1        348        0x80000003 0x0edb
10.11.0.2       10.0.0.1        348        0x80000003 0xf9ed
10.11.0.4       10.0.0.1        348        0x80000003 0xe5ff
10.11.0.6       10.0.0.1        348        0x80000003 0xd112
10.11.0.8       10.0.0.1        348        0x80000003 0xbd24
10.11.0.10      10.0.0.1        348        0x80000003 0xa936
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Spine3# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=254 time=3.004 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=254 time=1.653 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=254 time=1.519 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=254 time=3.419 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=254 time=2.199 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 1.519/2.358/3.419 ms
Spine3# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=253 time=4.72 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=253 time=3.366 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=253 time=3.475 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=253 time=3.575 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=253 time=3.444 ms
--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.366/3.715/4.72 ms
Spine3# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=253 time=3.766 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=253 time=3.27 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=253 time=2.886 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=253 time=2.742 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=253 time=2.848 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.742/3.102/3.766 ms
Spine3# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=252 time=6.016 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=252 time=4.834 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=252 time=5.117 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=252 time=4.986 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=252 time=5 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.834/5.19/6.016 ms
Spine3# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=252 time=6.208 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=252 time=4.873 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=252 time=4.772 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=252 time=4.846 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=252 time=4.996 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.772/5.138/6.208 ms
Spine3# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=252 time=5.967 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=252 time=5.116 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=252 time=13.824 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=252 time=5.9 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=252 time=4.649 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.649/7.091/13.824 ms
Spine3# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=254 time=9.492 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=254 time=3.307 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=254 time=3.55 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=254 time=3.121 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=254 time=2.764 ms
--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.764/4.446/9.492 ms
</code></pre>
</details>

---

###Leaf1
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.1/31|ether1/1|Spine1|
|10.11.0.7/31|ether1/2|Spine2|
|10.2.0.1/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.1/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.7/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.2.0.1/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 10.2.0.1
  passive-interface default
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf1# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/2                  3      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Leaf1# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          01:17:08 10.11.0.0       Eth1/1 
 10.1.0.2          1 FULL/ -          01:17:05 10.11.0.6       Eth1/2 
</code></pre>
<pre><code>
Leaf1# sh ip ospf database
        OSPF Router with ID (10.2.0.1) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        847        0x800000ea 0x90c5   5   
10.1.0.1        10.1.0.1        945        0x80000009 0xd6b0   9   
10.1.0.2        10.1.0.2        952        0x80000009 0x0a52   9   
10.2.0.1        10.2.0.1        989        0x80000006 0xce01   5   
10.2.0.2        10.2.0.2        964        0x80000006 0x1aaa   5   
10.2.0.3        10.2.0.3        954        0x80000007 0x6355   5   
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.3        10.0.0.1        595        0x80000003 0xe630
10.2.0.4        10.0.0.1        338        0x80000003 0x628a
10.10.0.4       10.0.0.1        847        0x80000003 0x60ae
10.11.0.12      10.0.0.1        595        0x80000003 0x9548
</code></pre>
</details>

<details>
  <summary>`Ping Traceroute test`</summary>
<pre><code>
Leaf1# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=253 time=5.188 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=253 time=4.522 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=253 time=4.622 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=253 time=3.757 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=253 time=4.147 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.757/4.447/5.188 ms
Leaf1# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=254 time=3.018 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=254 time=2.785 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=254 time=2.461 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=254 time=2.638 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=254 time=2.546 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.461/2.689/3.018 ms
Leaf1# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=252 time=6.289 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=252 time=4.555 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=252 time=4.587 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=252 time=4.802 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=252 time=4.839 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.555/5.014/6.289 ms
Leaf1# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=255 time=1.119 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=255 time=0.789 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=255 time=1.266 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=255 time=1.006 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=255 time=1.244 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 0.789/1.084/1.266 ms
Leaf1# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=253 time=6.372 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=253 time=5.211 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=253 time=5.299 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=253 time=4.866 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=253 time=5.192 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.866/5.388/6.372 ms
Leaf1# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=253 time=5.894 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=253 time=4.394 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=253 time=4.437 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=253 time=4.338 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=253 time=4.76 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.338/4.764/5.894 ms
-------------------------------------------------------------
![image](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/IMG/Leaf1Traceroute.png)

Dump [interface Ethernet1/1](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/Dump/Leaf1Interface1-1.pcapng)

Dump [interface Ethernet1/2](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/Dump/Leaf1Interface1-2.pcapng)
</code></pre>
</details>

---

###Leaf2
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.3/31|ether1/1|Spine1|
|10.11.0.9/31|ether1/2|Spine2|
|10.2.0.2/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.3/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.9/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.2.0.2/32
  ip router ospf 1 area 0.0.0.0
!
  router-id 10.2.0.2
  passive-interface default
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf2# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/2                  3      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up 
</code></pre>
<pre><code> 
Leaf2# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          01:20:59 10.11.0.2       Eth1/1 
 10.1.0.2          1 FULL/ -          01:21:05 10.11.0.8       Eth1/2 
</code></pre>
<pre><code>
Leaf2# sh ip ospf database
        OSPF Router with ID (10.2.0.2) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1105       0x800000ea 0x90c5   5   
10.1.0.1        10.1.0.1        1202       0x80000009 0xd6b0   9   
10.1.0.2        10.1.0.2        1209       0x80000009 0x0a52   9   
10.2.0.1        10.2.0.1        1248       0x80000006 0xce01   5   
10.2.0.2        10.2.0.2        1219       0x80000006 0x1aaa   5   
10.2.0.3        10.2.0.3        1211       0x80000007 0x6355   5   
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.3        10.0.0.1        852        0x80000003 0xe630
10.2.0.4        10.0.0.1        595        0x80000003 0x628a
10.10.0.4       10.0.0.1        1105       0x80000003 0x60ae
10.11.0.12      10.0.0.1        852        0x80000003 0x9548
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Leaf2# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=253 time=5.591 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=253 time=3.515 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=253 time=3.63 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=253 time=3.655 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=253 time=3.583 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.515/3.994/5.591 ms
Leaf2# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=254 time=5.405 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=254 time=2.136 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=254 time=2.504 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=254 time=2.26 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=254 time=2.492 ms
--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.136/2.959/5.405 ms
Leaf2# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=254 time=4.166 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=254 time=3.261 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=254 time=3.719 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=254 time=3.525 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=254 time=4.982 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.261/3.93/4.982 ms
Leaf2# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=252 time=6.309 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=252 time=4.865 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=252 time=4.895 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=252 time=5.265 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=252 time=4.882 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.865/5.243/6.309 ms
Leaf2# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=253 time=7.038 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=253 time=4.425 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=253 time=4.59 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=253 time=4.629 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=253 time=4.361 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.361/5.008/7.038 ms
Leaf2# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=253 time=5.391 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=253 time=4.447 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=253 time=4.294 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=253 time=4.304 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=253 time=4.446 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.294/4.576/5.391 ms
Leaf2# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=251 time=8.658 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=251 time=7.915 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=251 time=7.044 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=251 time=6.335 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=251 time=6.852 ms
--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 6.335/7.36/8.658 ms
</code></pre>
</details>

---

###Leaf3
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.5/31|ether1/1|Spine1|
|10.11.0.11/31|ether1/2|Spine2|
|10.2.0.3/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.5/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.11/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.2.0.3/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 10.2.0.3
  passive-interface default
  </code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf3# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/2                  3      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up 
</code></pre>
<pre><code>
Leaf3# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          01:23:56 10.11.0.4       Eth1/1 
 10.1.0.2          1 FULL/ -          01:23:56 10.11.0.10      Eth1/2 
</code></pre>
<pre><code>
Leaf3# sh ip ospf database
        OSPF Router with ID (10.2.0.3) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1299       0x800000ea 0x90c5   5   
10.1.0.1        10.1.0.1        1397       0x80000009 0xd6b0   9   
10.1.0.2        10.1.0.2        1404       0x80000009 0x0a52   9   
10.2.0.1        10.2.0.1        1442       0x80000006 0xce01   5   
10.2.0.2        10.2.0.2        1415       0x80000006 0x1aaa   5   
10.2.0.3        10.2.0.3        1403       0x80000007 0x6355   5   
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.3        10.0.0.1        1046       0x80000003 0xe630
10.2.0.4        10.0.0.1        789        0x80000003 0x628a
10.10.0.4       10.0.0.1        1299       0x80000003 0x60ae
10.11.0.12      10.0.0.1        1046       0x80000003 0x9548
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Leaf3# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=253 time=5.119 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=253 time=4.527 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=253 time=3.909 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=253 time=4.069 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=253 time=4.004 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.909/4.325/5.119 ms
Leaf3# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=254 time=3.137 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=254 time=2.478 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=254 time=2.444 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=254 time=2.455 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=254 time=2.533 ms
--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.444/2.609/3.137 ms
Leaf3# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=254 time=3.074 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=254 time=2.591 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=254 time=2.507 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=254 time=2.464 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=254 time=2.557 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.464/2.638/3.074 ms
Leaf3# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=252 time=6.781 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=252 time=5 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=252 time=4.521 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=252 time=5.092 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=252 time=5.036 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.521/5.285/6.781 ms
Leaf3# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=253 time=6.46 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=253 time=5.16 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=253 time=4.695 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=253 time=5.54 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=253 time=4.638 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.638/5.298/6.46 ms
Leaf3# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=253 time=5.416 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=253 time=4.547 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=253 time=4.292 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=253 time=4.307 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=253 time=4.427 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.292/4.597/5.416 ms
Leaf3# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=251 time=16.007 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=251 time=7.378 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=251 time=7.183 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=251 time=7.176 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=251 time=6.843 ms
--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 6.843/8.917/16.007 ms
</code></pre>
</details>

---

###Leaf4
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.11.0.13/31|ether1/1|Spine3|
|10.2.0.4/32|loopback0|
<details>
  <summary>`Show Config`</summary>
<pre><code>
feature ospf
feature bfd
interface Ethernet1/1
  description Spine3
  ip address 10.11.0.13/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface loopback0
  ip address 10.2.0.4/32
  ip router ospf 1 area 0.0.0.1
!
router ospf 1
  router-id 10.2.0.4
  passive-interface default  
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf4# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 2
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.1         40     P2P      1         up  
 Lo0                     1      0.0.0.1         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Leaf4# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 1
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.3          1 FULL/ -          01:25:01 10.11.0.12      Eth1/1 
</code></pre>
<pre><code>
Leaf4# sh ip ospf database
        OSPF Router with ID (10.2.0.4) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1300       0x80000004 0x7d3c   2   
10.1.0.3        10.1.0.3        1461       0x80000008 0x5067   5   
10.2.0.4        10.2.0.4        1465       0x80000007 0x86d6   3   
                Summary Network Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum 
10.0.0.1        10.0.0.1        1553       0x80000004 0xa082
10.1.0.1        10.0.0.1        1553       0x80000003 0xfa1e
10.1.0.2        10.0.0.1        1553       0x80000003 0xf027
10.2.0.1        10.0.0.1        1300       0x80000003 0x806f
10.2.0.2        10.0.0.1        1300       0x80000003 0x7678
10.2.0.3        10.0.0.1        1300       0x80000003 0x6c81
10.10.0.0       10.0.0.1        1553       0x80000004 0x868b
10.10.0.2       10.0.0.1        1553       0x80000004 0x729d
10.11.0.0       10.0.0.1        1553       0x80000003 0x0edb
10.11.0.2       10.0.0.1        1553       0x80000003 0xf9ed
10.11.0.4       10.0.0.1        1553       0x80000003 0xe5ff
10.11.0.6       10.0.0.1        1553       0x80000003 0xd112
10.11.0.8       10.0.0.1        1553       0x80000003 0xbd24
10.11.0.10      10.0.0.1        1553       0x80000003 0xa936
</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>
Leaf4# ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=253 time=5.197 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=253 time=3.839 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=253 time=4.197 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=253 time=9.527 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=253 time=5.036 ms
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.839/5.559/9.527 ms
Leaf4# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=252 time=6.434 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=252 time=4.757 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=252 time=5.059 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=252 time=5.146 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=252 time=4.895 ms
--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.757/5.258/6.434 ms
Leaf4# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=252 time=7.658 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=252 time=5.419 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=252 time=6.655 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=252 time=6.593 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=252 time=5.985 ms
--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 5.419/6.462/7.658 ms
Leaf4# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=254 time=3.619 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=254 time=2.542 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=254 time=3.249 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=254 time=3.305 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=254 time=3.246 ms
--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.542/3.192/3.619 ms
Leaf4# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=251 time=10.917 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=251 time=8.66 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=251 time=8.955 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=251 time=8.958 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=251 time=8.615 ms
--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 8.615/9.22/10.917 ms
Leaf4# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=251 time=15.453 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=251 time=7.433 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=251 time=7.21 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=251 time=7.74 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=251 time=7.858 ms
--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 7.21/9.138/15.453 ms
Leaf4# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=251 time=9.176 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=251 time=7.284 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=251 time=7.217 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=251 time=7.052 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=251 time=7.02 ms
--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 7.02/7.549/9.176 ms
</code></pre>
</details>