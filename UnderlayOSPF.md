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
![image](https://user-images.githubusercontent.com/58727249/138097448-c7264291-f8f9-4d17-b183-f7aecfd1ae8d.png)

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
  <summary>`Show ping test`</summary>
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
</code></pre>

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
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.2/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.4/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
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
  ip router ospf 1 area 0.0.0.1
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
 Eth1/1                  2      0.0.0.1         40     P2P      1         up  
 Eth1/2                  3      0.0.0.1         40     P2P      1         up  
 Eth1/3                  4      0.0.0.1         40     P2P      1         up  
 Eth1/7                  5      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.1         1      LOOPBACK 0         up
</code></pre>
<pre><code>
Spine1# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 4
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.1          1 FULL/ -          4d04h    10.11.0.1       Eth1/1 
 10.2.0.2          1 FULL/ -          4d04h    10.11.0.3       Eth1/2 
 10.2.0.3          1 FULL/ -          4d04h    10.11.0.5       Eth1/3 
 10.0.0.1          1 FULL/ -          4d05h    10.10.0.0       Eth1/7
</code></pre>
<pre><code>
Spine1# sh ip ospf database
        OSPF Router with ID (10.1.0.1) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1289       0x800000bb 0x52c4   7   
10.1.0.1        10.1.0.1        1469       0x800000cd 0xbe1d   2   
10.1.0.2        10.1.0.2        427        0x800000cd 0xf6de   2   
10.1.0.3        10.1.0.3        963        0x800000cb 0x339e   2  
-
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.1        10.1.0.1        1479       0x800000c9 0xe298
10.1.0.1        10.1.0.2        227        0x800000c9 0xff2a
10.1.0.2        10.1.0.1        219        0x800000c9 0xfb2e
10.1.0.2        10.1.0.2        437        0x800000c9 0xd2a6
10.1.0.3        10.1.0.3        963        0x800000ca 0xc0b5
10.2.0.1        10.1.0.1        239        0x800000c9 0x68e9
10.2.0.1        10.1.0.2        227        0x800000c9 0x62ee
10.2.0.2        10.1.0.1        89         0x800000ca 0x5cf3
10.2.0.2        10.1.0.2        97         0x800000c9 0x58f7
10.2.0.3        10.1.0.1        1569       0x800000c8 0x56fa
10.2.0.3        10.1.0.2        1568       0x800000c9 0x4e01
10.2.0.4        10.1.0.3        803        0x800000c9 0x3e0f
10.11.0.0       10.1.0.1        1479       0x800000c9 0xf556
10.11.0.0       10.1.0.2        227        0x800000c9 0x81a1
10.11.0.2       10.1.0.1        1479       0x800000c9 0xe168
10.11.0.2       10.1.0.2        97         0x800000ca 0x6bb4
10.11.0.4       10.1.0.1        1479       0x800000c9 0xcd7a
10.11.0.4       10.1.0.2        1568       0x800000c9 0x59c5
10.11.0.6       10.1.0.1        239        0x800000c9 0x4bd2
10.11.0.6       10.1.0.2        437        0x800000c9 0xb391
10.11.0.8       10.1.0.1        89         0x800000ca 0x35e5
10.11.0.8       10.1.0.2        427        0x800000c9 0x9fa3
10.11.0.10      10.1.0.1        1569       0x800000c9 0x23f6
10.11.0.10      10.1.0.2        427        0x800000c9 0x8bb5
10.11.0.12      10.1.0.3        963        0x800000ca 0x6fcd
-
                Router Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.1.0.1        10.1.0.1        1569       0x800000d0 0xfa65   7   
10.1.0.2        10.1.0.2        1568       0x800000ce 0x98a2   7   
10.2.0.1        10.2.0.1        244        0x800000cd 0x3fc8   5   
10.2.0.2        10.2.0.2        97         0x800000cc 0x8c71   5   
10.2.0.3        10.2.0.3        1568       0x800000cb 0xd91a   5   
-
                Summary Network Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum 
10.0.0.1        10.1.0.1        1469       0x800000c9 0x80d3
10.0.0.1        10.1.0.2        427        0x800000c9 0x7ad8
10.1.0.3        10.1.0.1        1469       0x800000c9 0xc482
10.1.0.3        10.1.0.2        427        0x800000c9 0xbe87
10.2.0.4        10.1.0.1        809        0x800000c9 0x40dc
10.2.0.4        10.1.0.2        427        0x800000c9 0x3ae1
10.10.0.0       10.1.0.1        1479       0x800000c9 0x024b
10.10.0.0       10.1.0.2        427        0x800000c9 0x60e1
10.10.0.2       10.1.0.1        1469       0x800000c9 0x52ee
10.10.0.2       10.1.0.2        427        0x800000c9 0xe762
10.10.0.4       10.1.0.1        1469       0x800000c9 0x3e01
10.10.0.4       10.1.0.2        427        0x800000c9 0x3806
10.11.0.12      10.1.0.1        1469       0x800000c9 0x739a
10.11.0.12      10.1.0.2        427        0x800000c9 0x6d9f
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
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.8/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.10/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.1
  ip ospf bfd
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.3/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  ip ospf bfd
!
interface loopback0
  ip address 10.1.0.2/32
  ip router ospf 1 area 0.0.0.1
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
 Eth1/1                  1      0.0.0.0         40     P2P      1         up  
 Eth1/2                  2      0.0.0.0         40     P2P      1         up  
 Eth1/3                  3      0.0.0.0         40     P2P      1         up  
 Eth1/7                  4      0.0.0.0         40     P2P      1         up  
 Lo0                     5      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Spine2# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 4
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.1          1 FULL/ -          02:12:05 10.11.0.7       Eth1/1 
 10.2.0.2          1 FULL/ -          02:10:13 10.11.0.9       Eth1/2 
 10.2.0.3          1 FULL/ -          02:09:55 10.11.0.11      Eth1/3 
 10.0.0.1          1 FULL/ -          01:37:48 10.10.0.2       Eth1/7 
</code></pre>
<pre><code>
Spine2# sh ip ospf database
        OSPF Router with ID (10.1.0.2) (Process ID 1 VRF default)
-
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        415        0x800000bc 0x50c5   7   
10.1.0.1        10.1.0.1        792        0x800000ce 0xbc1e   2   
10.1.0.2        10.1.0.2        1567       0x800000cd 0xf6de   2   
10.1.0.3        10.1.0.3        284        0x800000cc 0x319f   2   
-
                Summary Network Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum 
10.1.0.1        10.1.0.1        802        0x800000ca 0xe099
10.1.0.1        10.1.0.2        1367       0x800000c9 0xff2a
10.1.0.2        10.1.0.1        1362       0x800000c9 0xfb2e
10.1.0.2        10.1.0.2        1577       0x800000c9 0xd2a6
10.1.0.3        10.1.0.3        284        0x800000cb 0xbeb6
10.2.0.1        10.1.0.1        1382       0x800000c9 0x68e9
10.2.0.1        10.1.0.2        1367       0x800000c9 0x62ee
10.2.0.2        10.1.0.1        1232       0x800000ca 0x5cf3
10.2.0.2        10.1.0.2        1237       0x800000c9 0x58f7
10.2.0.3        10.1.0.1        892        0x800000c9 0x54fb
10.2.0.3        10.1.0.2        886        0x800000ca 0x4c02
10.2.0.4        10.1.0.3        124        0x800000ca 0x3c10
10.11.0.0       10.1.0.1        802        0x800000ca 0xf357
10.11.0.0       10.1.0.2        1367       0x800000c9 0x81a1
10.11.0.2       10.1.0.1        802        0x800000ca 0xdf69
10.11.0.2       10.1.0.2        1237       0x800000ca 0x6bb4
10.11.0.4       10.1.0.1        802        0x800000ca 0xcb7b
10.11.0.4       10.1.0.2        886        0x800000ca 0x57c6
10.11.0.6       10.1.0.1        1382       0x800000c9 0x4bd2
10.11.0.6       10.1.0.2        1577       0x800000c9 0xb391
10.11.0.8       10.1.0.1        1232       0x800000ca 0x35e5
10.11.0.8       10.1.0.2        1567       0x800000c9 0x9fa3
10.11.0.10      10.1.0.1        892        0x800000ca 0x21f7
10.11.0.10      10.1.0.2        1567       0x800000c9 0x8bb5
10.11.0.12      10.1.0.3        284        0x800000cb 0x6dce
-
                Router Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.1.0.1        10.1.0.1        892        0x800000d1 0xf866   7   
10.1.0.2        10.1.0.2        886        0x800000cf 0x96a3   7   
10.2.0.1        10.2.0.1        1385       0x800000cd 0x3fc8   5   
10.2.0.2        10.2.0.2        1239       0x800000cc 0x8c71   5   
10.2.0.3        10.2.0.3        889        0x800000cc 0xd71b   5   
-
                Summary Network Link States (Area 0.0.0.1)
Link ID         ADV Router      Age        Seq#       Checksum 
10.0.0.1        10.1.0.1        792        0x800000ca 0x7ed4
10.0.0.1        10.1.0.2        1567       0x800000c9 0x7ad8
10.1.0.3        10.1.0.1        792        0x800000ca 0xc283
10.1.0.3        10.1.0.2        1567       0x800000c9 0xbe87
10.2.0.4        10.1.0.1        132        0x800000ca 0x3edd
10.2.0.4        10.1.0.2        1567       0x800000c9 0x3ae1
10.10.0.0       10.1.0.1        802        0x800000ca 0xff4c
10.10.0.0       10.1.0.2        1567       0x800000c9 0x60e1
10.10.0.2       10.1.0.1        792        0x800000ca 0x50ef
10.10.0.2       10.1.0.2        1567       0x800000c9 0xe762
10.10.0.4       10.1.0.1        792        0x800000ca 0x3c02
10.10.0.4       10.1.0.2        1567       0x800000c9 0x3806
10.11.0.12      10.1.0.1        792        0x800000ca 0x719b
10.11.0.12      10.1.0.2        1567       0x800000c9 0x6d9f
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
router ospf 1
  router-id 10.1.0.3
  passive-interface default
!
interface Loopback0
 ip address 10.1.0.3 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Leaf4
 ip address 10.11.0.12 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/7
 description Core
 ip address 10.10.0.5 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
</code></pre>
</details>

</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Spine3# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Eth1/7                  3      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up 
</code></pre>
<pre><code> 
sh ip ospf database
Spine3# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.4          1 FULL/ -          01:22:09 10.11.0.13      Eth1/1 
 10.0.0.1          1 FULL/ -          01:22:11 10.10.0.4       Eth1/7 
</code></pre>
<pre><code>
switch# sh ip ospf database
        OSPF Router with ID (10.1.0.3) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        872        0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        1498       0x80000013 0x3e3f   9   
10.1.0.2        10.1.0.2        1348       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1292       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        1775       0x80000009 0x626a   5   
10.2.0.2        10.2.0.2        1666       0x8000000a 0x7b45   5   
10.2.0.3        10.2.0.3        1641       0x80000009 0x981e   5   
10.2.0.4        10.2.0.4        1290       0x80000009 0x82d8   3 
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
router ospf 1
  router-id 10.2.0.1
  passive-interface default
!
interface Loopback0
 ip address 10.2.0.1 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.1 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/2
 description Spine2
 ip address 10.11.0.7 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf1# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  1      0.0.0.0         40     P2P      1         up  
 Eth1/2                  2      0.0.0.0         40     P2P      1         up  
 Lo0                     3      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Leaf1# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          02:00:44 10.11.0.0       Eth1/1 
 10.1.0.2          1 FULL/ -          02:00:38 10.11.0.6       Eth1/2 
</code></pre>
<pre><code>
Leaf1# sh ip ospf database
        OSPF Router with ID (10.2.0.1) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        884        0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        1508       0x80000013 0x3e3f   9   
10.1.0.2        10.1.0.2        1358       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1307       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        1783       0x80000009 0x626a   5   
10.2.0.2        10.2.0.2        1676       0x8000000a 0x7b45   5   
10.2.0.3        10.2.0.3        1651       0x80000009 0x981e   5   
10.2.0.4        10.2.0.4        1304       0x80000009 0x82d8   3 
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
router ospf 1
  router-id 10.2.0.2
  passive-interface default
!
interface Loopback0
 ip address 10.2.0.2 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.3 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/2
 description Spine2
 ip address 10.11.0.9 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf2# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  1      0.0.0.0         40     P2P      1         up  
 Eth1/2                  2      0.0.0.0         40     P2P      1         up  
 Lo0                     3      0.0.0.0         1      LOOPBACK 0         up 
</code></pre>
<pre><code> 
Leaf2# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          02:03:26 10.11.0.2       Eth1/1 
 10.1.0.2          1 FULL/ -          02:03:25 10.11.0.8       Eth1/2 
</code></pre>
<pre><code>
Leaf2# sh ip ospf database
        OSPF Router with ID (10.2.0.2) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1162       0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        1787       0x80000013 0x3e3f   9   
10.1.0.2        10.1.0.2        1636       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1585       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        243        0x8000000a 0x606b   5   
10.2.0.2        10.2.0.2        132        0x8000000b 0x7946   5   
10.2.0.3        10.2.0.3        109        0x8000000a 0x961f   5   
10.2.0.4        10.2.0.4        1582       0x80000009 0x82d8   3  
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
router ospf 1
  router-id 10.2.0.3
  passive-interface default
!
interface Loopback0
 ip address 10.2.0.3 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.5 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/2
 description Spine2
 ip address 10.11.0.11 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf3# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 3
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  1      0.0.0.0         40     P2P      1         up  
 Eth1/2                  2      0.0.0.0         40     P2P      1         up  
 Lo0                     3      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Leaf3# sh ip ospf neighbor
sh ip ospf database
 OSPF Process ID 1 VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.1          1 FULL/ -          01:58:44 10.11.0.4       Eth1/1 
 10.1.0.2          1 FULL/ -          01:58:49 10.11.0.10      Eth1/2 
</code></pre>
<pre><code>
Leaf3# sh ip ospf database
        OSPF Router with ID (10.2.0.3) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        905        0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        1529       0x80000013 0x3e3f   9   
10.1.0.2        10.1.0.2        1378       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1327       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        1806       0x80000009 0x626a   5   
10.2.0.2        10.2.0.2        1697       0x8000000a 0x7b45   5   
10.2.0.3        10.2.0.3        1670       0x80000009 0x981e   5   
10.2.0.4        10.2.0.4        1324       0x80000009 0x82d8   3   
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
router ospf 1
  router-id 10.2.0.4
  passive-interface default
!
interface Loopback0
 ip address 10.2.0.4 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Spine3
 ip address 10.11.0.13 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Leaf4# sh ip ospf interface brief
 OSPF Process ID 1 VRF default
 Total number of interface: 2
 Interface               ID     Area            Cost   State    Neighbors Status
 Eth1/1                  2      0.0.0.0         40     P2P      1         up  
 Lo0                     1      0.0.0.0         1      LOOPBACK 0         up  
</code></pre>
<pre><code>
Leaf4# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 1
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.1.0.3          1 FULL/ -          01:29:06 10.11.0.12      Eth1/1 
</code></pre>
<pre><code>
Leaf4# sh ip ospf database
        OSPF Router with ID (10.2.0.4) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1290       0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        95         0x80000014 0x3c40   9   
10.1.0.2        10.1.0.2        1765       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1710       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        372        0x8000000a 0x606b   5   
10.2.0.2        10.2.0.2        263        0x8000000b 0x7946   5   
10.2.0.3        10.2.0.3        238        0x8000000a 0x961f   5   
10.2.0.4        10.2.0.4        1705       0x80000009 0x82d8   3   
</code></pre>
</details>