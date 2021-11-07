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