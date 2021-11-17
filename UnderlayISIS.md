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