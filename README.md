# Lab 01 - Adressage IPv4 et routage entre deux réseaux

Deux réseaux reliés par un routeur 2911, deux PC par réseau derrière
un switch 2960.

![topologie](images/topologie.png)

## Adressage

| Équipement | Interface | Adresse | Passerelle |
| --- | --- | --- | --- |
| R1 | G0/0 | 192.168.10.1/24 | - |
| R1 | G0/1 | 192.168.20.1/24 | - |
| PC0 | NIC | 192.168.10.10/24 | 192.168.10.1 |
| PC1 | NIC | 192.168.10.11/24 | 192.168.10.1 |
| PC2 | NIC | 192.168.20.10/24 | 192.168.20.1 |
| PC3 | NIC | 192.168.20.11/24 | 192.168.20.1 |

## Config R1

```
hostname R1
interface g0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
interface g0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
```

Config complète dans [configs/R1.txt](configs/R1.txt).

## Tests

- PC0 vers PC1 (même réseau) : ok
- PC0 vers PC2 (autre réseau) : ok
- `show ip interface brief` : G0/0 et G0/1 en up/up

## Pannes testées

- PC0 sans passerelle : le ping vers PC1 passe, celui vers PC2 échoue.
- PC0 avec un masque en /16 : il croit que PC2 est dans son réseau, fait
  une requête ARP directe que le routeur ne transmet pas. Échec.
- PC2 sans passerelle : il reçoit le ping de PC0 mais ne peut pas
  répondre. Vu de PC0 ça ressemble à une panne réseau alors que le
  problème est sur la destination.

## À retenir

Pour sortir de son réseau, un PC envoie la trame à la MAC de sa
passerelle, pas à celle du destinataire. L'IP de destination ne change
pas en route, la MAC change à chaque saut.