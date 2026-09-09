# 🌐 Network Infrastructure Lab

## 📌 Présentation

Ce repository présente un projet d'infrastructure réseau inspiré de situations et de compétences mises en œuvre au cours de mon parcours professionnel.

L'objectif est de documenter de manière simple et structurée plusieurs tâches liées à l'administration et à l'exploitation d'une infrastructure réseau :

- organisation et restructuration d'une baie informatique ;
- câblage et brassage réseau ;
- configuration de VLAN ;
- configuration DHCP ;
- administration sécurisée via SSH ;
- configuration de switches Cisco ;
- configuration et intégration de bornes Wi-Fi ;
- segmentation réseau ;
- vérification de la connectivité ;
- diagnostic et troubleshooting ;
- amélioration de la conformité et de la sécurité des équipements réseau.

> ⚠️ Ce projet est entièrement anonymisé.  
> Les adresses IP, noms d'équipements, VLAN et configurations présentés ici sont fictifs ou adaptés à un environnement de laboratoire.

---

# 🎯 Objectifs

Les principaux objectifs de ce projet sont :

- structurer une infrastructure réseau de manière logique ;
- segmenter les différents usages grâce aux VLAN ;
- sécuriser l'administration des équipements ;
- documenter les configurations essentielles ;
- identifier rapidement l'origine d'une panne réseau ;
- améliorer la lisibilité et la maintenabilité d'une infrastructure ;
- appliquer des bonnes pratiques réseau et sécurité ;
- mettre en place une méthodologie de troubleshooting cohérente.

---

# 🏗️ Architecture réseau

L'architecture ci-dessous représente un environnement générique de type entreprise.

```text
                         INTERNET
                            |
                            |
                       +----------+
                       | FIREWALL |
                       +-----+----+
                             |
                             |
                      +------+------+
                      | CORE SWITCH |
                      +------+------+
                             |
             +---------------+---------------+
             |               |               |
             |               |               |
      +------+-----+   +------+-----+   +------+-----+
      | ACCESS SW1 |   | ACCESS SW2 |   | ACCESS SW3 |
      +------+-----+   +------+-----+   +------+-----+
             |               |               |
          USERS            SERVERS           WIFI
             |                               |
        PCs / VoIP                      Access Points
```

Cette architecture permet de séparer les différents types de ressources tout en conservant une infrastructure simple à administrer.

---

# 🔀 Segmentation réseau

La segmentation réseau permet d'isoler les différents usages de l'infrastructure.

Exemple de plan VLAN :

| VLAN | Nom | Réseau | Fonction |
|---|---|---|---|
| 10 | MANAGEMENT | 192.168.10.0/24 | Administration réseau |
| 20 | USERS | 192.168.20.0/24 | Postes utilisateurs |
| 30 | SERVERS | 192.168.30.0/24 | Serveurs |
| 40 | WIFI | 192.168.40.0/24 | Wi-Fi interne |
| 50 | GUEST | 192.168.50.0/24 | Wi-Fi visiteurs |
| 60 | VOIP | 192.168.60.0/24 | Téléphonie IP |

Cette segmentation permet notamment :

- d'améliorer la sécurité ;
- de limiter les domaines de broadcast ;
- de faciliter l'administration ;
- d'isoler les équipements critiques ;
- de contrôler les communications entre différentes zones ;
- de simplifier le troubleshooting ;
- d'éviter qu'un réseau invité puisse accéder directement au réseau interne.

---

# 🗺️ Exemple de plan d'adressage

Exemple d'adressage pour le VLAN Management :

| Équipement | Adresse IP |
|---|---|
| Firewall | 192.168.10.1 |
| Core Switch | 192.168.10.10 |
| Access Switch 01 | 192.168.10.11 |
| Access Switch 02 | 192.168.10.12 |
| Access Point 01 | 192.168.10.50 |
| Access Point 02 | 192.168.10.51 |

Les adresses utilisées dans ce repository sont fictives.

---

# 🔧 Création des VLAN

Exemple de création de VLAN sur un équipement Cisco :

```cisco
configure terminal

vlan 10
 name MANAGEMENT

vlan 20
 name USERS

vlan 30
 name SERVERS

vlan 40
 name WIFI

vlan 50
 name GUEST

vlan 60
 name VOIP

end
```

Vérification :

```cisco
show vlan brief
```

Cette commande permet de contrôler :

- les VLAN existants ;
- leur état ;
- les ports associés.

---

# 🔌 Configuration d'un port Access

Un port Access est généralement utilisé pour connecter un équipement appartenant à un seul VLAN.

Exemple :

```cisco
configure terminal

interface gigabitethernet1/0/10

 description USER-PC-01

 switchport mode access
 switchport access vlan 20

 spanning-tree portfast

exit
```

Le terminal connecté au port appartient alors au VLAN `USERS`.

Vérification :

```cisco
show vlan brief
```

ou :

```cisco
show interfaces switchport
```

---

# 🔗 Configuration d'un port Trunk

Un port Trunk permet de transporter plusieurs VLAN entre différents équipements réseau.

Exemple :

```cisco
configure terminal

interface gigabitethernet1/0/48

 description UPLINK-CORE

 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60

exit
```

Vérification :

```cisco
show interfaces trunk
```

Points à contrôler :

- le port est bien en mode trunk ;
- les VLAN nécessaires sont autorisés ;
- le VLAN existe sur les équipements concernés ;
- la configuration est cohérente sur les deux extrémités.

---

# 🌍 DHCP

Le DHCP permet d'attribuer automatiquement les paramètres réseau aux équipements clients.

Exemple pour le VLAN utilisateurs :

```text
VLAN : USERS

Network:
192.168.20.0/24

Gateway:
192.168.20.1

DNS:
192.168.30.10

DHCP Pool:
192.168.20.50 - 192.168.20.200
```

Exemple pour le VLAN Wi-Fi :

```text
VLAN : WIFI

Network:
192.168.40.0/24

Gateway:
192.168.40.1

DHCP Pool:
192.168.40.50 - 192.168.40.220
```

Les serveurs et équipements réseau peuvent utiliser :

- des adresses IP statiques ;
- ou des réservations DHCP.

---

# 🔐 Administration SSH

L'administration distante des équipements réseau doit être réalisée avec un protocole sécurisé.

SSH est privilégié à Telnet.

Exemple de configuration :

```cisco
configure terminal

hostname SW-CORE-01

ip domain-name lab.local

username admin privilege 15 secret CHANGE_ME

crypto key generate rsa

ip ssh version 2

line vty 0 4

 login local
 transport input ssh

exit
```

Vérification :

```cisco
show ip ssh
```

Connexion :

```bash
ssh admin@192.168.10.10
```

---

# 🔒 Sécurisation de l'administration

Quelques bonnes pratiques :

- utiliser SSH ;
- désactiver Telnet ;
- utiliser un VLAN Management dédié ;
- limiter les équipements autorisés à administrer les switches ;
- utiliser des comptes individuels lorsque cela est possible ;
- utiliser des mots de passe forts ;
- désactiver les ports inutilisés ;
- documenter les interfaces ;
- sauvegarder les configurations.

---

# 📶 Infrastructure Wi-Fi

Une infrastructure Wi-Fi professionnelle peut être composée de plusieurs points d'accès reliés à des switches PoE.

Exemple :

```text
                CORE SWITCH
                    |
                    |
               ACCESS SWITCH
                    |
         +----------+----------+
         |          |          |
        AP01       AP02       AP03
```

Les bornes peuvent diffuser plusieurs SSID.

Exemple :

| SSID | VLAN | Fonction |
|---|---:|---|
| CORPORATE | 40 | Réseau interne |
| GUEST | 50 | Réseau visiteurs |

Cette séparation permet d'isoler les terminaux invités des ressources internes.

---

# 📡 Fonctionnement Wi-Fi

Exemple de chemin réseau :

```text
Laptop
  |
  | Wi-Fi
  |
Access Point
  |
  | Ethernet / PoE
  |
Access Switch
  |
  | Trunk
  |
Core Switch
  |
Firewall / Gateway
```

Lorsqu'un problème Wi-Fi apparaît, plusieurs éléments doivent être vérifiés :

- connexion au SSID ;
- authentification ;
- signal radio ;
- VLAN ;
- DHCP ;
- passerelle ;
- DNS ;
- accès Internet.

---

# 🗄️ Organisation d'une baie informatique

La gestion physique de l'infrastructure fait également partie de l'administration réseau.

Les principales actions peuvent inclure :

- identification des équipements ;
- brassage des ports ;
- rangement des câbles ;
- remplacement de câbles ;
- utilisation de guides-câbles ;
- étiquetage ;
- documentation ;
- vérification des uplinks ;
- suppression des câbles inutilisés.

Exemple d'organisation :

```text
+----------------------------------+
| Patch Panel                      |
+----------------------------------+
| Guide-câbles                     |
+----------------------------------+
| Switch Access                    |
+----------------------------------+
| Guide-câbles                     |
+----------------------------------+
| Switch Core                      |
+----------------------------------+
| Firewall                         |
+----------------------------------+
| PDU                              |
+----------------------------------+
```

Une baie correctement organisée permet :

- de réduire le temps d'intervention ;
- de limiter les erreurs ;
- d'améliorer la maintenance ;
- de faciliter le troubleshooting ;
- d'améliorer la lisibilité de l'infrastructure.

---

# 🔄 STP / RSTP

Le Spanning Tree Protocol permet de protéger une infrastructure Ethernet contre les boucles de niveau 2.

Les principaux éléments à surveiller sont :

- Root Bridge ;
- Root Port ;
- Designated Port ;
- ports bloqués ;
- changements de topologie ;
- BPDU ;
- ports instables.

Commandes utiles :

```cisco
show spanning-tree
```

```cisco
show spanning-tree detail
```

```cisco
show spanning-tree interface gigabitethernet1/0/10
```

---

# ⚠️ Exemple de problème : boucle réseau

Une boucle de niveau 2 peut entraîner :

- une charge CPU importante ;
- des broadcasts excessifs ;
- une forte instabilité ;
- des pertes de paquets ;
- des changements STP fréquents ;
- des MAC address flapping ;
- des pertes de connectivité.

Méthodologie :

```text
1. Identifier les équipements concernés
2. Vérifier les logs
3. Vérifier STP
4. Identifier les ports ayant beaucoup de changements
5. Vérifier les uplinks
6. Contrôler les compteurs d'interfaces
7. Identifier les ports instables
8. Isoler progressivement le lien problématique
```

---

# 🧪 Méthodologie de troubleshooting réseau

Le dépannage doit être effectué de manière structurée afin d'éviter les modifications inutiles.

---

## 1. Vérification physique

Contrôler :

- câble réseau ;
- voyant du port ;
- état de l'équipement ;
- négociation ;
- vitesse ;
- duplex.

Commande :

```cisco
show interfaces status
```

---

## 2. Vérification de l'interface

```cisco
show interfaces gigabitethernet1/0/10
```

Éléments à contrôler :

- état `up/down` ;
- erreurs ;
- drops ;
- erreurs CRC ;
- vitesse ;
- duplex.

---

## 3. Vérification du VLAN

```cisco
show vlan brief
```

Vérifier que le port appartient au bon VLAN.

---

## 4. Vérification du trunk

```cisco
show interfaces trunk
```

Vérifier :

- que le VLAN existe ;
- qu'il est autorisé sur le trunk ;
- que le trunk est opérationnel.

---

## 5. Vérification de l'adresse IP

Sous Windows :

```powershell
ipconfig /all
```

Sous Linux :

```bash
ip addr
```

Points à contrôler :

- adresse IP ;
- masque ;
- passerelle ;
- DNS ;
- serveur DHCP.

---

## 6. Test de la pile TCP/IP

```bash
ping 127.0.0.1
```

Permet de vérifier la pile réseau locale.

---

## 7. Test de la passerelle

```bash
ping 192.168.20.1
```

Si la passerelle ne répond pas :

- vérifier le VLAN ;
- vérifier le port ;
- vérifier l'adresse IP ;
- vérifier le routage.

---

## 8. Test d'un autre réseau

```bash
ping 192.168.30.10
```

Permet de vérifier la communication entre plusieurs réseaux.

---

## 9. Test Internet

```bash
ping 1.1.1.1
```

Si cette adresse répond mais qu'un domaine ne fonctionne pas, le problème peut être lié au DNS.

---

## 10. Test DNS

```bash
nslookup example.com
```

Sous Linux :

```bash
dig example.com
```

---

# 🔍 Recherche d'un équipement avec une adresse MAC

La table MAC permet d'identifier sur quel port un équipement est connecté.

Commande :

```cisco
show mac address-table
```

Exemple :

```text
Vlan    Mac Address       Type       Ports
----    -----------       --------   -----
20      0011.2233.4455    DYNAMIC    Gi1/0/10
```

Dans cet exemple, l'équipement se trouve sur le port `Gi1/0/10`.

---

# 📊 Analyse des interfaces

Commande :

```cisco
show interfaces counters
```

Éléments à surveiller :

- erreurs ;
- drops ;
- collisions ;
- erreurs CRC ;
- trafic inhabituel ;
- volumes de paquets importants.

---

# 🔌 Gestion des ports inutilisés

Les ports inutilisés doivent idéalement être désactivés.

Exemple :

```cisco
configure terminal

interface gigabitethernet1/0/20

 description UNUSED

 shutdown

exit
```

Cela réduit le risque de connexion non autorisée.

---

# 📝 Documentation des interfaces

Il est recommandé d'ajouter une description aux ports.

Exemple pour un utilisateur :

```cisco
interface gigabitethernet1/0/10

 description USER-PC-01
```

Exemple pour un uplink :

```cisco
interface gigabitethernet1/0/48

 description UPLINK-CORE
```

Cette documentation facilite les interventions et le dépannage.

---

# 🧰 Commandes Cisco utiles

## VLAN

```cisco
show vlan brief
```

## Interfaces

```cisco
show interfaces status
```

```cisco
show interfaces
```

## Trunks

```cisco
show interfaces trunk
```

## Table MAC

```cisco
show mac address-table
```

## ARP

```cisco
show arp
```

## Spanning Tree

```cisco
show spanning-tree
```

## Logs

```cisco
show logging
```

## Configuration

```cisco
show running-config
```

## Informations système

```cisco
show version
```

---

# 🔐 Bonnes pratiques réseau

## Segmentation

Séparer les différents usages :

```text
Management
Users
Servers
Wi-Fi
Guests
VoIP
```

---

## Administration

Privilégier :

```text
SSH
VLAN Management
Comptes individuels
Mots de passe forts
Accès restreints
```

---

## Ports

- désactiver les ports inutilisés ;
- documenter les interfaces ;
- limiter les VLAN présents sur les trunks ;
- contrôler les ports uplink ;
- éviter les configurations inutiles.

---

## Wi-Fi

- séparer le Wi-Fi interne du Wi-Fi invité ;
- associer les SSID à des VLAN dédiés ;
- limiter l'accès du réseau Guest ;
- documenter les points d'accès ;
- vérifier régulièrement leur connectivité.

---

# ✅ Checklist de mise en conformité

## Switch

- [ ] Hostname défini
- [ ] Adresse Management configurée
- [ ] SSH activé
- [ ] Telnet désactivé
- [ ] VLAN configurés
- [ ] Trunks vérifiés
- [ ] Ports inutilisés désactivés
- [ ] Interfaces documentées
- [ ] STP vérifié
- [ ] Configuration sauvegardée

## Wi-Fi

- [ ] SSID identifiés
- [ ] VLAN associés
- [ ] Réseau Guest séparé
- [ ] Points d'accès documentés
- [ ] Adressage vérifié

## Documentation

- [ ] Plan IP
- [ ] Plan VLAN
- [ ] Liste des équipements
- [ ] Uplinks documentés
- [ ] Ports critiques identifiés

---

# 🛠️ Exemple de démarche d'incident

Exemple :

```text
Un utilisateur est connecté au réseau mais n'a pas accès à Internet.
```

Méthodologie :

```text
1. Vérifier le câble
2. Vérifier le port du switch
3. Vérifier le VLAN
4. Vérifier l'adresse IP
5. Vérifier la passerelle
6. Vérifier le DHCP
7. Tester la passerelle
8. Tester une adresse IP externe
9. Tester le DNS
10. Vérifier le routage ou le firewall si nécessaire
```

Cette méthode permet de progresser couche par couche.

---

# 🔎 Exemple de diagnostic d'un port instable

Lorsqu'un port effectue régulièrement des transitions `UP/DOWN`, plusieurs causes peuvent être envisagées :

- câble défectueux ;
- équipement instable ;
- alimentation ;
- carte réseau ;
- négociation ;
- boucle réseau ;
- problème physique.

Commandes utiles :

```cisco
show interfaces status
```

```cisco
show logging
```

```cisco
show interfaces gigabitethernet1/0/10
```

```cisco
show interfaces counters
```

---

# 🧠 Compétences mises en œuvre

## Réseau

- TCP/IP
- VLAN
- DHCP
- SSH
- Switching
- Trunking
- STP / RSTP
- Wi-Fi
- Ethernet

## Cisco

- configuration de switches ;
- configuration de ports ;
- gestion des VLAN ;
- gestion des trunks ;
- analyse des interfaces ;
- analyse de la table MAC ;
- troubleshooting réseau.

## Infrastructure

- organisation de baie ;
- câblage ;
- brassage ;
- identification d'équipements ;
- documentation réseau ;
- gestion des uplinks.

## Troubleshooting

- perte de connectivité ;
- problème DHCP ;
- problème VLAN ;
- problème trunk ;
- problème DNS ;
- problème d'interface ;
- boucle réseau ;
- analyse STP ;
- port instable.

## Sécurité

- segmentation réseau ;
- administration SSH ;
- séparation du réseau Management ;
- désactivation des ports inutilisés ;
- mise en conformité des équipements.

---

# 🔗 Intégration avec mes autres projets

Ce projet réseau peut être associé à plusieurs autres projets de mon portfolio.

## Centreon

Supervision de :

```text
Switches
Access Points
Interfaces
CPU
Mémoire
Disponibilité
```

## Graylog

Centralisation et analyse de :

```text
Syslog
Logs réseau
Logs firewall
Événements système
```

## Ansible

Automatisation de :

```text
Configurations
Déploiements
Standardisation
Contrôles
```

L'ensemble permet de représenter une infrastructure plus globale :

```text
Infrastructure réseau
        |
        +---- Monitoring
        |
        +---- Logging
        |
        +---- Automation
        |
        +---- Security
```

---

# 🚀 Évolutions possibles

Ce projet pourra évoluer avec :

- routage inter-VLAN ;
- ACL ;
- firewall ;
- SNMP ;
- supervision réseau ;
- centralisation Syslog ;
- automatisation Ansible ;
- haute disponibilité ;
- VPN ;
- sauvegarde automatique des configurations ;
- monitoring des interfaces ;
- Infrastructure as Code ;
- génération automatique de documentation.

---

# 🔒 Confidentialité

Ce repository s'inspire de problématiques et de compétences rencontrées durant mon parcours professionnel.

Afin de respecter la confidentialité :

- aucune adresse IP réelle n'est utilisée ;
- aucun hostname réel n'est publié ;
- aucun mot de passe réel n'est présent ;
- aucun nom d'utilisateur réel n'est utilisé ;
- aucun schéma de production réel n'est reproduit ;
- aucun nom d'entreprise n'est présent dans les configurations ;
- aucune configuration sensible n'est publiée.

Les exemples sont volontairement génériques et adaptés à un environnement de laboratoire.

---

# 👤 Auteur

**Medhy Marouf**

Infrastructure • Systems • Networks • Cybersecurity • Automation

---

# 📄 Licence

Ce repository est publié dans le cadre d'un portfolio personnel et à des fins pédagogiques.

Les configurations et exemples présents dans ce projet servent à illustrer des compétences techniques et ne doivent pas être considérés comme des configurations directement prêtes à être utilisées dans un environnement de production.
