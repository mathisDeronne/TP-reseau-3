# TP3 : On va router des trucs
## I. ARP
### 1. Echange ARP

🌞**Générer des requêtes ARP**
```
-[user@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.373 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.368 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=0.298 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=0.488 ms
64 bytes from 10.3.1.12: icmp_seq=5 ttl=64 time=1.24 ms
64 bytes from 10.3.1.12: icmp_seq=6 ttl=64 time=0.368 ms
64 bytes from 10.3.1.12: icmp_seq=7 ttl=64 time=0.394 ms
64 bytes from 10.3.1.12: icmp_seq=8 ttl=64 time=0.506 ms
64 bytes from 10.3.1.12: icmp_seq=9 ttl=64 time=0.353 ms
64 bytes from 10.3.1.12: icmp_seq=10 ttl=64 time=0.358 ms

--- 10.3.1.12 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9239ms
rtt min/avg/max/mdev = 0.298/0.474/1.239/0.261 ms
- pc John:
```
```
[user@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s3 lladdr 08:00:27:22:76:0c STALE
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:34 REACHABLE
```
- **pc Marcel**
```
[user@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:34 REACHABLE
10.3.1.11 dev enp0s3 lladdr 08:00:27:41:37:67 STALE
```
- **mac de Marcel**
sur la machine de Jhon : 08:00:27:22:76:0c
sur la machine de Marcel : 08:00:27:22:76:0c
- **mac de John** 
sur la machine de Marcel : 08:00:27:41:37:67
sur la machine de Jhon : 08:00:27:41:37:67

  - **pour voir la mac de Marcel dans le pc de Jhon :** 
  "ip neigh show"
  - **pour voir la mac de Marcel depuis le pc de Marcel :** 
  "ip a" 

### 2. Analyse de trames

🌞**Analyse de trames**

![totoyo](./toto.cpap)

## II. Routage

| Machine  | `10.3.1.0/24` | `10.3.2.0/24` |
|----------|---------------|---------------|
| `router` | `10.3.1.254`  | `10.3.2.254`  |
| `john`   | `10.3.1.11`   | no            |
| `marcel` | no            | `10.3.2.12`   |
### 1. Mise en place du routage

🌞**Activer le routage sur le noeud `router`**
```
[user@localhost ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
[sudo] password for user:
success
```

🌞**Ajouter les routes statiques nécessaires pour que `john` et `marcel` puissent se `ping`**

- **depuis le PC de John :**
``[user@localhost ~]$ sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s3 ``
- **depuis le PC de Marcel :**
``[user@localhost ~]$ sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s3``
- **Ping depuis le PC de John :** 
```
[user@localhost ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=0.540 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=0.951 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=0.655 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=0.919 ms
64 bytes from 10.3.2.12: icmp_seq=5 ttl=63 time=0.693 ms
64 bytes from 10.3.2.12: icmp_seq=6 ttl=63 time=0.652 ms
64 bytes from 10.3.2.12: icmp_seq=7 ttl=63 time=0.810 ms
64 bytes from 10.3.2.12: icmp_seq=8 ttl=63 time=0.896 ms
64 bytes from 10.3.2.12: icmp_seq=9 ttl=63 time=0.706 ms
64 bytes from 10.3.2.12: icmp_seq=10 ttl=63 time=0.723 ms
64 bytes from 10.3.2.12: icmp_seq=11 ttl=63 time=2.20 ms
64 bytes from 10.3.2.12: icmp_seq=12 ttl=63 time=0.513 ms
64 bytes from 10.3.2.12: icmp_seq=13 ttl=63 time=0.697 ms
^C
--- 10.3.2.12 ping statistics ---
13 packets transmitted, 13 received, 0% packet loss, time 12303ms
rtt min/avg/max/mdev = 0.513/0.842/2.198/0.412 ms
```
### 2. Analyse de trames

🌞**Analyse des échanges ARP**

- **Depuis le PC de John :** 
```
[user@localhost ~]$ sudo ip neigh flush all
[sudo] password for user:
[user@localhost ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=0.918 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=0.639 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=0.705 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=0.756 ms
64 bytes from 10.3.2.12: icmp_seq=5 ttl=63 time=0.639 ms
^C
--- 10.3.2.12 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4125ms
rtt min/avg/max/mdev = 0.639/0.731/0.918/0.103 ms
```
- **Table ARP de John :** 
```
[user@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:0d REACHABLE
10.3.1.254 dev enp0s3 lladdr 08:00:27:05:6d:73 STALE
```
- **Table ARP de Marcel :**
```
[user@localhost ~]$ ip neigh show
10.3.2.1 dev enp0s3 lladdr 0a:00:27:00:00:15 DELAY
10.3.2.254 dev enp0s3 lladdr 08:00:27:dc:22:d1 STALE
```
- **Table ARP du Router**
```
[user@localhost ~]$ ip neigh show
10.3.1.11 dev enp0s8 lladdr 08:00:27:7e:f2:d8 STALE
10.3.2.1 dev enp0s9 lladdr 0a:00:27:00:00:15 DELAY
10.3.2.12 dev enp0s9 lladdr 08:00:27:22:76:0c STALE
```
- **essayez de déduire un peu les échanges ARP qui ont eu lieu :** 
On peut voir dans les tables ARP de John et Marcel qu'ils n'ont pas vraiment communiqué entre eux; Ils ont chacun envoyé le ping et le pong a leur passerelle (10.3.1.254 pour John et 10.3.2.254 pour Marcel) et la passerelle a transmis les informations au PC destinataire
**sens du ping (idem pour le pong mais dans l'autre sens):**
```
John(10.3.1.11) ---> Carte 1 routeur(10.3.1.254) ---> Carte 2 routeur(10.3.2.254) ---> Marcel(10.3.2.12)
``` 
- répétez l'opération précédente (vider les tables, puis `ping`), en lançant `tcpdump` sur `marcel`
- **écrivez, dans l'ordre, les échanges ARP qui ont eu lieu, puis le ping et le pong, je veux TOUTES les trames** utiles pour l'échange

Par exemple (copiez-collez ce tableau ce sera le plus simple) :

| ordre | type trame  | IP source | MAC source              | IP destination | MAC destination            |
|-------|-------------|-----------|-------------------------|----------------|----------------------------|
| 1     | Requête ARP | x         | `marcel` `AA:BB:CC:DD:EE` | x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x         | ?                       | x              | `marcel` `AA:BB:CC:DD:EE`    |
| ...   | ...         | ...       | ...                     |                |                            |
| ?     | Ping        | ?         | ?                       | ?              | ?                          |
| ?     | Pong        | ?         | ?                       | ?              | ?                          |

> Vous pourriez, par curiosité, lancer la capture sur `john` aussi, pour voir l'échange qu'il a effectué de son côté.

🦈 **Capture réseau `tp3_routage_marcel.pcapng`**

### 3. Accès internet

🌞**Donnez un accès internet à vos machines**

- ajoutez une carte NAT en 3ème inteface sur le `router` pour qu'il ait un accès internet
- ajoutez une route par défaut à `john` et `marcel`
  - vérifiez que vous avez accès internet avec un `ping`
  - le `ping` doit être vers une IP, PAS un nom de domaine
- donnez leur aussi l'adresse d'un serveur DNS qu'ils peuvent utiliser
  - vérifiez que vous avez une résolution de noms qui fonctionne avec `dig`
  - puis avec un `ping` vers un nom de domaine

🌞**Analyse de trames**

- effectuez un `ping 8.8.8.8` depuis `john`
- capturez le ping depuis `john` avec `tcpdump`
- analysez un ping aller et le retour qui correspond et mettez dans un tableau :

| ordre | type trame | IP source          | MAC source              | IP destination | MAC destination |     |
|-------|------------|--------------------|-------------------------|----------------|-----------------|-----|
| 1     | ping       | `marcel` `10.3.1.12` | `marcel` `AA:BB:CC:DD:EE` | `8.8.8.8`      | ?               |     |
| 2     | pong       | ...                | ...                     | ...            | ...             | ... |

🦈 **Capture réseau `tp3_routage_internet.pcapng`**

## III. DHCP

On reprend la config précédente, et on ajoutera à la fin de cette partie une 4ème machine pour effectuer des tests.

| Machine  | `10.3.1.0/24`              | `10.3.2.0/24` |
|----------|----------------------------|---------------|
| `router` | `10.3.1.254`               | `10.3.2.254`  |
| `john`   | `10.3.1.11`                | no            |
| `bob`    | oui mais pas d'IP statique | no            |
| `marcel` | no                         | `10.3.2.12`   |

```schema
   john               router              marcel
  ┌─────┐             ┌─────┐             ┌─────┐
  │     │    ┌───┐    │     │    ┌───┐    │     │
  │     ├────┤ho1├────┤     ├────┤ho2├────┤     │
  └─────┘    └─┬─┘    └─────┘    └───┘    └─────┘
   dhcp        │
  ┌─────┐      │
  │     │      │
  │     ├──────┘
  └─────┘
```

### 1. Mise en place du serveur DHCP

🌞**Sur la machine `john`, vous installerez et configurerez un serveur DHCP** (go Google "rocky linux dhcp server").

- installation du serveur sur `john`
- créer une machine `bob`
- faites lui récupérer une IP en DHCP à l'aide de votre serveur

> Il est possible d'utilise la commande `dhclient` pour forcer à la main, depuis la ligne de commande, la demande d'une IP en DHCP, ou renouveler complètement l'échange DHCP (voir `dhclient -h` puis call me et/ou Google si besoin d'aide).

🌞**Améliorer la configuration du DHCP**

- ajoutez de la configuration à votre DHCP pour qu'il donne aux clients, en plus de leur IP :
  - une route par défaut
  - un serveur DNS à utiliser
- récupérez de nouveau une IP en DHCP sur `bob` pour tester :
  - `bob` doit avoir une IP
    - vérifier avec une commande qu'il a récupéré son IP
    - vérifier qu'il peut `ping` sa passerelle
  - il doit avoir une route par défaut
    - vérifier la présence de la route avec une commande
    - vérifier que la route fonctionne avec un `ping` vers une IP
  - il doit connaître l'adresse d'un serveur DNS pour avoir de la résolution de noms
    - vérifier avec la commande `dig` que ça fonctionne
    - vérifier un `ping` vers un nom de domaine

### 2. Analyse de trames

🌞**Analyse de trames**

- lancer une capture à l'aide de `tcpdump` afin de capturer un échange DHCP
- demander une nouvelle IP afin de générer un échange DHCP
- exportez le fichier `.pcapng`

🦈 **Capture réseau `tp3_dhcp.pcapng`**

![totoyo](./words.txt)