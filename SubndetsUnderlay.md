### Схема распределения адресного пространства

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
!
interface Ethernet0/1
 description Spine1
 ip address 10.10.0.0 255.255.255.254
!
interface Ethernet0/2
 description Spine2
 ip address 10.10.0.2 255.255.255.254
!
interface Ethernet0/3
 description Spine3
 ip address 10.10.0.4 255.255.255.254
!
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
interface Loopback0
 ip address 10.1.0.1 255.255.255.255
!
interface Ethernet1/1
 description Leaf1
 ip address 10.11.0.0 255.255.255.254
!
interface Ethernet1/2
 description Leaf2
 ip address 10.11.0.2 255.255.255.254
!
interface Ethernet1/3
 description Leaf3
 ip address 10.11.0.4 255.255.255.254
!
interface Ethernet0/7
 description Core
 ip address 10.10.0.1 255.255.255.254
!
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
interface Loopback0
 ip address 10.1.0.2 255.255.255.255
!
interface Ethernet1/1
 description Leaf1
 ip address 10.11.0.6 255.255.255.254
!
interface Ethernet1/2
 description Leaf2
 ip address 10.11.0.8 255.255.255.254
!
interface Ethernet1/3
 description Leaf3
 ip address 10.11.0.10 255.255.255.254
!
interface Ethernet1/7
 description Core
 ip address 10.10.0.3 255.255.255.254
!
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
interface Loopback0
 ip address 10.1.0.3 255.255.255.255
!
interface Ethernet1/1
 description Leaf4
 ip address 10.11.0.12 255.255.255.254
!
interface Ethernet1/7
 description Core
 ip address 10.10.0.5 255.255.255.254
!
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
interface Loopback0
 ip address 10.2.0.1 255.255.255.255
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.1 255.255.255.254
!
interface Ethernet1/2
 description Spine2
 ip address 10.11.0.7 255.255.255.254
!
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
interface Loopback0
 ip address 10.2.0.2 255.255.255.255
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.3 255.255.255.254
!
interface Ethernet1/2
 description Spine2
 ip address 10.10.0.9 255.255.255.254
!
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
interface Loopback0
 ip address 10.2.0.3 255.255.255.255
!
interface Ethernet1/1
 description Spine1
 ip address 10.11.0.5 255.255.255.254
!
interface Ethernet1/2
 description Spine2
 ip address 10.11.0.11 255.255.255.254
!
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
interface Loopback0
 ip address 10.2.0.4 255.255.255.255
!
interface Ethernet1/1
 description Spine3
 ip address 10.0.0.14 255.255.255.254
!
</code></pre>
</details>
