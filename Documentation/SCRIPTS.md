# Index des scripts

## But

Ce document explique rapidement le rôle des scripts du projet et l'ordre dans lequel les utiliser.

---

## 1. Scripts Windows !

### 01-Creation_AD.ps1

**Rôle** :

- installe le rôle AD DS
- crée le domaine `technova.local`
- crée les OU
- crée les groupes
- crée les utilisateurs
- configure le DHCP

**Quand l'utiliser** :

- au début du projet
- sur le serveur Windows principal

**Prérequis** :

- Windows Server
- droits administrateur
- IP serveur définie

---

### 02-mailUtil.ps1

**Rôle** :

- ajoute ou corrige les adresses mail des utilisateurs AD

**Quand l'utiliser** :

- après la création des utilisateurs
- avant les intégrations applicatives

---

### ActivTLS.ps1

**Rôle** :

- active TLS 1.2 côté Windows / .NET / SCHANNEL

**Quand l'utiliser** :

- si l'environnement Windows a besoin d'une correction TLS
- avant certains échanges sécurisés

---

### desactivTls.ps1

**Rôle** :

- désactive TLS 1.2

**Quand l'utiliser** :

- seulement pour retour arrière ou test

**Attention** :

- ce script ne doit pas être utilisé sans raison claire

---

## 2. Scripts Linux

### run_ad_scripts.sh

**Rôle** :

- monte un partage réseau en lecture seule
- récupère les scripts `.sh` déposés par l'administrateur Windows
- les copie en local et les exécute
- marque chaque script exécuté (par checksum) pour ne pas le relancer si inchangé
- simule le comportement d'une **GPO de démarrage** pour les machines Debian

**Quand l'utiliser** :

- quand on veut pousser des scripts depuis le serveur AD vers les machines Debian du domaine
- quand on veut centraliser l'administration des machines Linux via le contrôleur de domaine

**Prérequis** :

- machine jointe au domaine
- partage `Scripts_Linux` accessible sur le DC
- fichier `/etc/import-ad-certs.credentials` présent

---

### run-ad-scripts.service

**Rôle** :

- lance automatiquement `run_ad_scripts.sh` au démarrage

**Quand l'utiliser** :

- après installation du script shell dans `/usr/local/bin/`

---

### import_ad_certs.sh

**Rôle** :

- monte un partage réseau
- copie des certificats internes
- met à jour le magasin de certificats Linux
- tente l'import dans Firefox / Chrome
- s'appuie sur un fichier d'identifiants externe

**Quand l'utiliser** :

- si les serveurs Linux utilisent des certificats internes liés au domaine
- le script peut être distribué via le partage AD, puis copié localement

**Prérequis** :

- machine jointe au domaine
- partage réseau accessible
- certificats disponibles
- fichier `/etc/import-ad-certs.credentials` présent

---

### import-ad-certs.service

**Rôle** :

- lance automatiquement `import_ad_certs.sh` au démarrage

**Quand l'utiliser** :

- après installation du script shell dans le bon chemin

---

## 3. Ordre global des scripts

1. `01-Creation_AD.ps1`
2. `02-mailUtil.ps1`
3. `ActivTLS.ps1` *(si nécessaire)*
4. installation des services Linux
5. `import_ad_certs.sh` + `import-ad-certs.service` *(si utilisé)*
6. `run_ad_scripts.sh` + `run-ad-scripts.service` *(si utilisé)*

---

## 4. Bonnes pratiques

- modifier les identifiants en dur avant démonstration ou production
- tester chaque script séparément
- lancer les scripts dans le bon ordre
- documenter les paramètres réellement utilisés
- conserver les scripts dans `script/` sans les disperser
