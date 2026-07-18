# Partie 2 : Configuration du Partage de Fichiers (SMB) et Sécurité NTFS

L'objectif de cette deuxième phase est de mettre en place un serveur de fichiers local (SMB) sécurisé sur la VM-01 et d'en valider l'accès à distance depuis la VM-02. Cette manipulation permet de maîtriser la dualité entre les permissions de partage et les autorisations de sécurité NTFS dans un environnement Workgroup.

## Environnement de test
* **Hyperviseur** : VMware Workstation PRO
* **VM-01 (Serveur de fichiers)** : Windows 10 | IP : 192.168.10.10 /24
* **VM-02 (Client)** : Windows 10 | IP : 192.168.10.20 /24

## Étapes de configuration

### 1. Préparation de la structure de fichiers sur VM-01
* Création d'un dossier racine nommé `C:\Lab-Partage`.
* Création de deux sous-dossiers pour tester l'étanchéité des permissions :
  * `C:\Lab-Partage\Public` (Accès en lecture/écriture pour le groupe Utilisateurs).
  * `C:\Lab-Partage\Confidentiel` (Accès restreint aux administrateurs).

### 2. Gestion des comptes d'utilisateurs locaux (Workgroup)
* Pour que l'authentification réseau fonctionne sans domaine, nous créons un compte utilisateur local identique sur les deux machines :
  * **Nom d'utilisateur** : `TechUser`
  * **Mot de passe** : `Soleil321`
* Ajout de l'utilisateur au groupe local approprié selon les besoins de validation.

### 3. Configuration du Partage Réseau (SMB)
* Ouverture des propriétés du dossier `Lab-Partage` -> Onglet Partage -> Partage avancé.
* Activation du partage sous le nom de ressource `Lab_Share$` (le symbole `$` permet de masquer le partage sur le réseau).
* **Permissions de partage** : Attribution du contrôle total à `Tout le monde` (la restriction fine sera gérée par la couche NTFS).

### 4. Configuration de la sécurité NTFS (Autorisations de sécurité)
* Propriétés du dossier -> Onglet Sécurité -> Avancé.
* Désactivation de l'héritage pour briser les permissions parentes de `C:\` et conversion des autorisations héritées en autorisations explicites.
* Configuration des listes de contrôle d'accès (ACL) :
  * **Pour le dossier Public** : Ajout de `TechUser` avec les droits de modification (Lecture, Écriture, Modification).
  * **Pour le dossier Confidentiel** : Suppression de `TechUser` de la liste pour ne laisser que le groupe `Administrateurs`.

### 5. Validation des accès depuis VM-02
* Connexion sur la VM-02 avec le compte `TechUser`.
* Ouverture de l'explorateur de fichiers et accès via le chemin UNC : `\\192.168.10.10\Lab_Share$`.
* **Tests de validation requis** :
  1. Tentative d'écriture d'un fichier texte dans le dossier `Public` (Doit réussir).
  2. Tentative d'accès au dossier `Confidentiel` (Doit afficher un message "Accès refusé").

## Dépannage (Troubleshooting)

Lors de la mise en place, une erreur système 67 (*Lab_Share$*) est survenue lors de la tentative de connexion depuis la **VM-02**. La résolution a nécessité les étapes de validation suivantes sur la **VM-01** :

* **Activation de la découverte réseau** : Le profil réseau a dû être configuré en *Privé* pour permettre l'activation du partage de fichiers et la découverte de réseau dans le Panneau de configuration.
* **Validation de la syntaxe UNC** : Correction du nom de partage pour cibler exactement le partage masqué configuré (`Lab-Share$`).

## Preuve de concept
![Validation des permissions SMB et NTFS](smb-permissions.png)
