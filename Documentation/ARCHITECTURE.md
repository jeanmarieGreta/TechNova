# Architecture cible du Projet-1

## Vue d'ensemble

Le projet est construit autour d'une architecture simple et réaliste pour un niveau **TSSR**.

## Architecture recommandée

### Serveur 1 - Windows Server

- **Rôles** :
  - Active Directory Domain Services
  - DNS
  - DHCP
- **Fonctions** :
  - gestion des utilisateurs
  - gestion des groupes
  - centralisation de l'authentification
  - résolution de noms interne
  - distribution des adresses IP

### Serveur 2 - Debian / Ubuntu

- **Service principal** : **Nextcloud**
- **Composants** :
  - Apache
  - PHP
  - MariaDB
  - Redis
- **Fonctions** :
  - stockage collaboratif
  - partage de documents
  - gestion des accès

### Serveur 3 - Debian / Ubuntu

- **Service principal** : **Rocket.Chat**
- **Fonctions** :
  - messagerie interne
  - canaux d'équipe
  - communication d'équipe

### Serveur 4 - Debian / Ubuntu

- **Service principal** : **Jitsi Meet**
- **Fonctions** :
  - visioconférence
  - réunions audio / vidéo

### Serveur 5 - Debian / Ubuntu *(optionnel)*

- **Service principal** : **n8n**
- **Fonctions** :
  - automatisation
  - synchronisation
  - notifications
  - workflows

---

## Nommage conseillé

- **Domaine AD** : `technova.local`
- **Nextcloud** : `nextcloud.technova.local`
- **Rocket.Chat** : `rocket.technova.local`
- **Jitsi Meet** : `meet.technova.local`
- **n8n** : `n8n.technova.local`

---

## Plan d'adressage conseillé

Le projet doit gagner en lisibilité avec un plan IP explicite.

### Réseau retenu

- **Réseau principal** : `192.168.192.0/24`
- **Passerelle** : `192.168.192.2`
- **DNS / Contrôleur de domaine** : `192.168.192.10`

### Répartition recommandée

| Équipement | Rôle | Adresse IP |
|---|---|---|
| `SRV-AD` | AD / DNS / DHCP | `192.168.192.10` |
| `SRV-NEXTCLOUD` | Nextcloud | `192.168.192.20` |
| `SRV-CHAT` | Rocket.Chat | `192.168.192.30` |
| `SRV-JITSI` | Jitsi Meet | `192.168.192.40` |
| `SRV-N8N` | n8n *(optionnel)* | `192.168.192.50` |

### Plage DHCP

- **Début** : `192.168.192.100`
- **Fin** : `192.168.192.200`
- **Masque** : `255.255.255.0`

### Remarque importante

Le script *01-Creation_AD.ps1* utilise déjà la plage `192.168.192.0/24`.

Une ancienne version du projet utilisait un autre plan d'adressage ; l'ensemble a été harmonisé et la référence à retenir dans le projet est :

- **`192.168.192.0/24`**

---

## Dépendances entre les services

L'ordre de déploiement est important.

1. **AD / DNS / DHCP**
2. **Création des utilisateurs et groupes**
3. **DNS des services**
4. **Certificats**
5. **Installation des applications**
6. **Intégrations LDAP / SSO**
7. **Automatisation**

---

## Intégrations attendues

### Active Directory

- source d'identité principale
- base pour les utilisateurs, groupes et mails

### Rocket.Chat

- peut être alimenté par l'AD
- peut être structuré à partir des groupes AD

### Jitsi Meet

- peut être restreint par authentification
- peut être intégré à l'écosystème collaboratif

### Nextcloud

- stockage central
- partage documentaire
- gestion des permissions par groupes

### n8n *(optionnel)*

- moteur d'automatisation
- utile si le projet veut montrer une dimension plus avancée

---

## Objectif pédagogique

Cette architecture permet de travailler :

- **l'administration système**
- **les services réseau**
- **la gestion d'identité**
- **la sécurité**
- **la documentation**
- **l'automatisation**