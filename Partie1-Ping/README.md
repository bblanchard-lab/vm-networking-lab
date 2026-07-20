## Partie 1 : Validation de la connectivité réseau (Ping) et configuration ICMP

L'objectif de cette première phase est de configurer et de valider la connectivité réseau entre deux machines virtuelles (VM) Windows 10 complètement isolées dans un laboratoire. Ce lab permet de manipuler la segmentation réseau sur un hyperviseur et de configurer le pare-feu Windows proprement, sans désactiver les protections de sécurité par défaut.

### Environnement de test
* **Hyperviseur :** VMware Workstation Pro
* **VM-01 (Machine A) :** Windows 10 | IP : `192.168.10.10/24`
* **VM-02 (Machine B) :** Windows 10 | IP : `192.168.10.20/24`

---

### Étapes de configuration

#### 1. Segmentation et isolation réseau sur l'hyperviseur
* Configuration d'un commutateur virtuel privé et isolé (**LAN Segment** ou **Host-Only** via un VMnet dédié) pour bloquer tout trafic vers l'extérieur ou le réseau local physique.
* Modification de la carte réseau (NIC) de chaque VM dans VMware pour basculer du mode NAT/Bridged vers notre profil réseau isolé.

#### 2. Configuration des adresses IP statiques
* Ouverture des connexions réseau avec la commande `ncpa.cpl`.
* Configuration manuelle du protocole IPv4 sur les cartes réseau :
    * **VM-01 :** IP `192.168.10.10` | Masque `255.255.255.0`
    * **VM-02 :** IP `192.168.10.20` | Masque `255.255.255.0`
* **Validation :** Exécution de la commande `ipconfig` dans l'invite de commandes (CMD) de chaque machine pour confirmer que les adresses sont bien appliquées.

#### 3. Comportement par défaut du pare-feu Windows
* **Test initial :** Lancement d'un `ping 192.168.10.20` depuis la VM-01.
* **Résultat :** Le ping échoue avec le message *"Délai d'attente de la demande dépassé"*.
* **Pourquoi ça bloque :** Par défaut, le pare-feu Windows bloque toutes les requêtes ICMP (ping) entrantes. C'est une protection de base pour éviter qu'une machine soit visible ou scannée sur un réseau.

#### 4. Configuration sécurisée du pare-feu (Pas de désactivation complète)
Désactiver complètement un pare-feu est une mauvaise pratique de sécurité. À la place, la règle a été ciblée précisément :
* Ouverture du pare-feu avancé avec la commande `wf.msc`.
* Dans la section **Règles de trafic entrant (Inbound Rules)**, localisation et activation de la règle native suivante :
    * **Nom de la règle :** *Partage de fichiers et d'imprimantes (Demande d'écho - Trafic entrant ICMPv4)*
    * **Action :** Autoriser la règle uniquement pour le profil réseau approprié (Privé).

#### 5. Validation finale et preuves de succès
* Réexécution du ping entre la VM-01 et la VM-02 dans les deux sens.
* Le trafic passe désormais correctement avec 0% de perte de paquets.

![Preuve photo - Succès du Ping](./images/Ping-Succes.png)
