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
router ospf 1
 router-id 10.0.0.1
 passive-interface default
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
!
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
 ip ospf 1 area 0
!
</code></pre>
</details>

<details>
  <summary>`Show OSPF status`</summary>
<pre><code>
Core#sh ip ospf interface brief
Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
Lo0          1     0               10.0.0.1/32        1     LOOP  0/0
Et0/3        1     0               10.10.0.4/31       10    P2P   1/1
Et0/2        1     0               10.10.0.2/31       10    P2P   1/1
Et0/1        1     0               10.10.0.0/31       10    P2P   1/1
</code></pre>
<pre><code>
Core#sh ip ospf neighbor
Neighbor ID     Pri   State           Dead Time   Address         Interface
10.1.0.3          0   FULL/  -        00:00:39    10.10.0.5       Ethernet0/3
10.1.0.2          0   FULL/  -        00:00:36    10.10.0.3       Ethernet0/2
10.1.0.1          0   FULL/  -        00:00:32    10.10.0.1       Ethernet0/1
</code></pre>
<pre><code>
Core#sh ip ospf database
            OSPF Router with ID (10.0.0.1) (Process ID 1)
                Router Link States (Area 0)
Link ID         ADV Router      Age         Seq#       Checksum Link count
10.0.0.1        10.0.0.1        155         0x80000013 0x00A31C 7
10.1.0.1        10.1.0.1        781         0x80000013 0x003E3F 9
10.1.0.2        10.1.0.2        631         0x80000015 0x00EB64 9
10.1.0.3        10.1.0.3        578         0x80000015 0x002486 5
10.2.0.1        10.2.0.1        1058        0x80000009 0x00626A 5
10.2.0.2        10.2.0.2        949         0x8000000A 0x007B45 5
10.2.0.3        10.2.0.3        924         0x80000009 0x00981E 5
10.2.0.4        10.2.0.4        575         0x80000009 0x0082D8 3
Core#
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
router ospf 1
  router-id 10.1.0.1
  passive-interface default
!
interface Loopback0
 ip address 10.1.0.1 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Leaf1
 ip address 10.11.0.0 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/2
 description Leaf2
 ip address 10.11.0.2 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/3
 description Leaf3
 ip address 10.11.0.4 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet0/7
 description Core
 ip address 10.10.0.1 255.255.255.254
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
Spine1# sh ip ospf interface brief
sh ip ospf neighbor
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
Spine1# sh ip ospf neighbor
 OSPF Process ID 1 VRF default
 Total number of neighbors: 4
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.2.0.1          1 FULL/ -          01:55:44 10.11.0.1       Eth1/1 
 10.2.0.2          1 FULL/ -          01:53:47 10.11.0.3       Eth1/2 
 10.2.0.3          1 FULL/ -          01:53:23 10.11.0.5       Eth1/3 
 10.0.0.1          1 FULL/ -          01:21:24 10.10.0.0       Eth1/7 
</code></pre>
<pre><code>
Spine1# sh ip ospf database
        OSPF Router with ID (10.1.0.1) (Process ID 1 VRF default)
                Router Link States (Area 0.0.0.0)
Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        583        0x80000013 0xa31c   7   
10.1.0.1        10.1.0.1        1208       0x80000013 0x3e3f   9   
10.1.0.2        10.1.0.2        1059       0x80000015 0xeb64   9   
10.1.0.3        10.1.0.3        1006       0x80000015 0x2486   5   
10.2.0.1        10.2.0.1        1484       0x80000009 0x626a   5   
10.2.0.2        10.2.0.2        1376       0x8000000a 0x7b45   5   
10.2.0.3        10.2.0.3        1351       0x80000009 0x981e   5   
10.2.0.4        10.2.0.4        1003       0x80000009 0x82d8   3   
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
router ospf 1
  router-id 10.1.0.2
  passive-interface default
!
interface Loopback0
 ip address 10.1.0.2 255.255.255.255
 ip router ospf 1 area 0.0.0.0
!
interface Ethernet1/1
 description Leaf1
 ip address 10.11.0.6 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/2
 description Leaf2
 ip address 10.11.0.8 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/3
 description Leaf3
 ip address 10.11.0.10 255.255.255.254
 ip ospf network point-to-point
 no ip ospf passive-interface
 ip router ospf 1 area 0.0.0.0
 ip ospf bfd
!
interface Ethernet1/7
 description Core
 ip address 10.10.0.3 255.255.255.254
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

</code></pre>
</details>