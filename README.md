### Схема распределения адресного пространства

| Подсеть  | Префикс  | Роль |
|:------------ |:-------:|:-------:|
|10.1.0.0/16|/30|p2p|
|10.0.0.0/16|/32|loopback|

---
###Core
|  address |interface |peer
|:---------------:|:-----:|:----:|
|10.1.0.1/30| ether 0/1 |Spine1|
| 10.1.0.5/30|ether 0/2  |Spine2|
| 10.1.0.9/30 |  ether 0/3|Spine3|
|10.0.0.1/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.0.1 255.255.255.255
!
interface Ethernet0/1
 description Spine1
 ip address 10.1.0.1 255.255.255.252
!
interface Ethernet0/2
 description Spine2
 ip address 10.1.0.5 255.255.255.252
!
interface Ethernet0/3
 description Spine3
 ip address 10.1.0.9 255.255.255.252
!
</code></pre>
</details>

---
###Spine1
 | address |interface |peer
|:---------------:|:-----:|:-----:|
|10.1.0.2/30| ether 0/24 |Core|
| 10.1.1.1/30|ether 0/1  |Leaf1|
| 10.1.1.5/30 | ether 0/2|Leaf2|
| 10.1.1.9/30 | ether 0/3|Leaf3|
|10.0.1.1/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.1.1 255.255.255.255
!
interface Ethernet0/1
 description Leaf1
 ip address 10.1.1.1 255.255.255.252
!
interface Ethernet0/2
 description Leaf2
 ip address 10.1.1.5 255.255.255.252
!
interface Ethernet0/3
 description Leaf3
 ip address 10.1.1.9 255.255.255.252
!
interface Ethernet0/24
 description Core
 ip address 10.1.0.2 255.255.255.252
!
</code></pre>
</details>

---
###Spine2
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.1.0.6/30| ether 0/24 |Core|
| 10.1.2.1/30|ether 0/1  |Leaf1|
| 10.1.2.5/30 |  ether 0/2|Leaf2|
| 10.1.2.9/30 |  ether 0/3|Leaf3|
|10.0.1.2/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.1.2 255.255.255.255
!
interface Ethernet0/1
 description Leaf1
 ip address 10.1.2.1 255.255.255.252
!
interface Ethernet0/2
 description Leaf2
 ip address 10.1.2.5 255.255.255.252
!
interface Ethernet0/3
 description Leaf3
 ip address 10.1.2.9 255.255.255.252
!
interface Ethernet0/24
 description Core
 ip address 10.1.0.6 255.255.255.252
!
</code></pre>
</details>

---
###Spine3
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
|10.1.0.10/30| ether 0/24 |Core|
| 10.1.4.13/30|ether 0/1  |Leaf4|
|10.0.1.3/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.1.3 255.255.255.255
!
interface Ethernet0/4
 description Leaf4
 ip address 10.1.4.13 255.255.255.252
!
interface Ethernet0/24
 description Core
 ip address 10.1.0.10 255.255.255.252
!
</code></pre>
</details>

---
###Leaf1
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
| 10.1.1.2/30|ether 0/1  |Spine1|
| 10.1.2.2/30 | ether 0/2|Spine2|
|10.0.2.1/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.2.1 255.255.255.255
!
interface Ethernet0/1
 description Spine1
 ip address 10.1.1.2 255.255.255.252
!
interface Ethernet0/2
 description Spine2
 ip address 10.1.2.2 255.255.255.252
!
</code></pre>
</details>

---
###Leaf2
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
| 10.1.1.6/30|ether 0/1  |Spine1|
| 10.1.2.6/30 | ether 0/2|Spine2|
|10.0.2.2/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.2.2 255.255.255.255
!
interface Ethernet0/1
 description Spine1
 ip address 10.1.1.6 255.255.255.252
!
interface Ethernet0/2
 description Spine2
 ip address 10.1.2.6 255.255.255.252
!
</code></pre>
</details>

---
###Leaf3
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
| 10.1.1.10/30|ether 0/1  |Spine1|
| 10.1.2.10/30 | ether 0/2|Spine2|
|10.0.2.3/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.2.3 255.255.255.255
!
interface Ethernet0/1
 description Spine1
 ip address 10.1.1.10 255.255.255.252
!
interface Ethernet0/2
 description Spine2
 ip address 10.1.2.10 255.255.255.252
!
</code></pre>
</details>

---
###Leaf4
 |  address |interface |peer
|:---------------:|:-----:|:-----:|
| 10.1.4.14/30|ether 0/4  |Spine3|
|10.0.2.4/32|loopback0 |
<details>
  <summary>`Show Config`</summary>
<pre><code>
interface Loopback0
 ip address 10.0.2.4 255.255.255.255
!
interface Ethernet0/4
 description Spine3
 ip address 10.1.4.14 255.255.255.252
!
</code></pre>
</details>
