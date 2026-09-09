# 🌐 Network Infrastructure Lab

## 📌 Présentation

Ce repository présente un laboratoire d'infrastructure réseau inspiré de situations rencontrées dans un environnement professionnel.

L'objectif est de mettre en pratique la conception, la configuration, la segmentation et le dépannage d'une infrastructure réseau d'entreprise.

Le projet couvre notamment :

- la segmentation réseau avec des VLAN ;
- la configuration de switches ;
- les ports Access et Trunk ;
- le routage entre différents réseaux ;
- le DHCP ;
- l'administration sécurisée en SSH ;
- l'intégration d'une infrastructure Wi-Fi ;
- les mécanismes STP / RSTP ;
- le diagnostic et le troubleshooting réseau ;
- la documentation d'une infrastructure.

> ⚠️ Ce laboratoire est entièrement anonymisé.
> Les noms d'équipements, adresses IP, VLAN et configurations présentés dans ce repository sont fictifs ou adaptés à un environnement de lab.

---

# 🎯 Objectifs du projet

Ce projet a pour objectif de démontrer ma capacité à :

- concevoir une architecture réseau ;
- segmenter une infrastructure en plusieurs zones ;
- configurer des équipements réseau ;
- sécuriser l'administration des équipements ;
- diagnostiquer des problèmes de connectivité ;
- identifier des anomalies réseau ;
- documenter une infrastructure ;
- appliquer des bonnes pratiques d'exploitation réseau.

---

# 🏗️ Architecture

L'environnement de laboratoire représente une infrastructure d'entreprise composée de plusieurs zones réseau.

```text
                         Internet
                            │
                            │
                       ┌──────────┐
                       │ Firewall │
                       └────┬─────┘
                            │
                            │
                     ┌──────┴──────┐
                     │ Core Switch │
                     └──────┬──────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              │             │             │
        ┌─────┴─────┐ ┌─────┴─────┐ ┌─────┴─────┐
        │ Access SW │ │ Access SW │ │ Wi-Fi / AP │
        └───────────┘ └───────────┘ └───────────┘
              │             │             │
         Workstations    Servers       Wireless
