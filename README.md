# TechNova

## Présentation
Ce dépôt contient le projet TechNova, un POC d'infrastructure collaborative pour une PME fictive. Le projet vise à monter une architecture sur site avec un contrôleur de domaine Windows Active Directory et plusieurs services Open Source : Nextcloud, Rocket.Chat, Jitsi Meet, et éventuellement n8n.

## Objectifs
- Centraliser l'identité avec Active Directory (`technova.local`)
- Fournir des services internes sécurisés pour la collaboration
- Automatiser et documenter le déploiement avec des scripts
- Proposer une architecture réaliste pour un projet TSSR

## Architecture cible
- Serveur Windows : AD DS, DNS, DHCP
- Serveur Debian/Ubuntu 1 : Nextcloud
- Serveur Debian/Ubuntu 2 : Rocket.Chat
- Serveur Debian/Ubuntu 3 : Jitsi Meet
- Serveur Debian/Ubuntu 4 (optionnel) : n8n

### Noms conseillés
- Domaine : `technova.local`
- Nextcloud : `nextcloud.technova.local`
- Rocket.Chat : `rocket.technova.local`
- Jitsi Meet : `meet.technova.local`
- n8n : `n8n.technova.local`

## Contenu du dépôt
- `Documentation/` : documents de cadrage, architecture, installation et index des scripts
- `Script/` : scripts PowerShell pour l'AD et la configuration Windows

### Scripts clés
- `Script/01-Creation_AD.ps1` : installe AD DS, DNS, DHCP, crée le domaine, les OU, groupes et utilisateurs
- `Script/02-mailUtil.ps1` : ajoute ou corrige les adresses mail des utilisateurs AD
- `Script/ActivTLS.ps1` : active TLS 1.2 sur Windows
- `Documentation/desactivTls.ps1` : désactive TLS 1.2 si nécessaire

## Usage recommandé
1. Préparer les machines et définir l'adressage IP
2. Lancer `Script/01-Creation_AD.ps1` sur le contrôleur de domaine Windows
3. Lancer `Script/02-mailUtil.ps1` pour compléter les adresses mail des utilisateurs
4. Installer les services Linux dans l'ordre : Nextcloud, Rocket.Chat, Jitsi Meet, n8n
5. Préparer le DNS interne et les certificats pour les services
6. Vérifier l'accès HTTPS, l'intégration AD/LDAP et le bon fonctionnement des services

## Documentation importante
- `Documentation/ARCHITECTURE.md` : architecture cible et plan d'adressage
- `Documentation/CONTEXTE_ENTREPRISE.md` : contexte métier et objectifs du projet
- `Documentation/INSTALLATION_COMPLETE.md` : procédure d'installation complète
- `Documentation/SCRIPTS.md` : description et ordre d'utilisation des scripts

## Bonnes pratiques
- Toujours tester les scripts dans un environnement isolé
- Ne pas laisser d'identifiants en clair dans les scripts en production
- Utiliser des certificats internes pour `technova.local`
- Respecter l'ordre de déploiement : AD → DNS → certificats → applications

## Licence
Ce dépôt est un projet pédagogique et ne contient pas de licence formelle. Adaptez-le selon les règles de votre établissement ou de votre organisation.
