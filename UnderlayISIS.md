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

<details>
  <summary>`Ping Traceroute test`</summary>
<pre><code>

</code></pre>
</details>

---