### Настройка Uderlay ISIS

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
![image](https://raw.githubusercontent.com/nousaibot/VXLAN-EVPN-CiscoNexus-COD-LAB/main/IMG/UnderlayISIS.PNG)

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
 ip router isis 1
!
interface Ethernet0/1
 description Spine1
 ip address 10.10.0.0 255.255.255.254
 ip router isis 1
 isis network point-to-point 
!
interface Ethernet0/2
 description Spine2
 ip address 10.10.0.2 255.255.255.254
 ip router isis 1
 isis network point-to-point 
!
interface Ethernet0/3
 description Spine3
 ip address 10.10.0.4 255.255.255.254
 ip router isis 1
 isis network point-to-point 
!
router isis 1
 net 49.0007.0100.0000.0001.00
 is-type level-2-only
 metric-style wide
 </code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>
-
<details>
  <summary>`Ping Traceroute test`</summary>
<pre><code>
-
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
feature isis
interface Ethernet1/1
  description Leaf1
  ip address 10.11.0.0/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.2/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.4/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.1/31
  isis network point-to-point
  isis circuit-type level-2
  ip router isis 1
!
interface loopback0
  ip address 10.1.0.1/32
  ip router isis 1
!
router isis 1
  net 49.0001.0100.0100.0001.00
  address-family ipv4 unicast
    distribute level-1 into level-2 all
</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

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
feature isis
interface Ethernet1/1
  description Leaf1
  ip address 10.11.0.6/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/2
  description Leaf2
  ip address 10.11.0.8/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/3
  description Leaf3
  ip address 10.11.0.10/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.3/31
  isis network point-to-point
  isis circuit-type level-2
  ip router isis 1
!
interface loopback0
  ip address 10.1.0.2/32
  ip router isis 1
!
router isis 1
  net 49.0001.0100.0100.0002.00
  address-family ipv4 unicast
    distribute level-1 into level-2 all
</code></pre>
</details>
  
<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
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
feature isis
interface Ethernet1/1
  description Leaf4
  ip address 10.11.0.12/31
  isis network point-to-point
  isis circuit-type level-1
  ip router isis 1
!
interface Ethernet1/7
  description Core
  ip address 10.10.0.5/31
  isis network point-to-point
  isis circuit-type level-2
  ip router isis 1
!
interface loopback0
  ip address 10.1.0.3/32
  ip router isis 1
!
router isis 1
  net 49.0002.0100.0100.0003.00
  address-family ipv4 unicast
    distribute level-1 into level-2 all
</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
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
feature isis
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.1/31
  isis network point-to-point
  ip router isis 1
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.7/31
  isis network point-to-point
  ip router isis 1
!
interface loopback0
  ip address 10.2.0.1/32
  ip router isis 1
!
router isis 1
  net 49.0001.0100.0200.0001.00
  is-type level-1
  address-family ipv4 unicast
</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping Traceroute test`</summary>
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
feature isis
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.3/31
  isis network point-to-point
  ip router isis 1
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.9/31
  isis network point-to-point
  ip router isis 1
!
interface loopback0
  ip address 10.2.0.2/32
  ip router isis 1
!
router isis 1
  net 49.0001.0100.0200.0002.00
  is-type level-1
  address-family ipv4 unicast
</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
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
feature isis
interface Ethernet1/1
  description Spine1
  ip address 10.11.0.5/31
  isis network point-to-point
  ip router isis 1
!
interface Ethernet1/2
  description Spine2
  ip address 10.11.0.11/31
  isis network point-to-point
  ip router isis 1
!
interface loopback0
  ip address 10.2.0.3/32
  ip router isis 1
!
router isis 1
  net 49.0001.0100.0200.0003.00
  is-type level-1
  address-family ipv4 unicast
  </code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
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
feature isis
interface Ethernet1/1
  description Spine3
  no switchport
  ip address 10.11.0.13/31
  isis network point-to-point
  ip router isis 1
!
interface loopback0
  ip address 10.2.0.4/32
  ip router isis 1
!
router isis 1
  net 49.0002.0100.0200.0004.00
  is-type level-1
  address-family ipv4 unicast
</code></pre>
</details>

<details>
  <summary>`Show ISIS status`</summary>
<pre><code>

</code></pre>
</details>

<details>
  <summary>`Ping test`</summary>
<pre><code>

</code></pre>
</details>