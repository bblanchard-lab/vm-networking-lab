# Partie 1 : Validation de la Connectivité (Ping)

L'objectif de cette première phase est d'assurer la connectivité réseau de base entre deux machines virtuelles.

## Environnement de test
* **Hyperviseur : VMware Workstation PRO** 
* VM-01 (Machine A) : Windows 10 | IP : 192.168.10.10 /24
* VM-02 (Machine B) : Windows 10 | IP : 192.168.10.20 /24

## Étapes de configuration
1. Déploiement des machines virtuelles.
   * Création de deux machines virtuelles Windows 10 distinctes dans l'hyperviseur VMware Workstation Pro.
2. Isolation et configuration réseau de l'hyperviseur.
   * Modification des paramètres de la carte réseau (NIC) des deux VMs pour les basculer sur un réseau privé isolé (Host-Only / VMnet10). Cette configuration assure que le trafic reste confiné dans notre laboratoire.
3. Plan d'adressage IP statique.
   * Attribution manuelle d'adresses IPv4 privées dans le même sous-réseau (ex: 192.168.10.0/24).
   * Validation : Utilisation de la commande ipconfig dans l'invite de commandes (CMD) de chaque VM pour valider la bonne configuration des interfaces.
4. Configuration de la sécurité (Pare-feu Windows).
   * Par défaut, le pare-feu de Windows 10 bloque les requêtes ICMP entrantes (ce qui fait échouer le ping même si le réseau est bon).
   * Création d'une règle de trafic entrant dans le Pare-feu Windows avec fonctions avancées de sécurité pour autoriser spécifiquement : Partage de fichiers et d'imprimantes (Demande d'écho - Trafic entrant ICMPv4).

## Preuve de concept (Ping)
*![Ping Réussi](ping-succès.png)*
