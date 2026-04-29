# Procédure d'installation complète

## But du document

Ce document sert de **référence principale** pour réaliser le projet dans le bon ordre.

Il centralise :

- l'ordre d'installation
- les dépendances
- les étapes à réaliser
- les points de contrôle

---

## 1. Préparer l'infrastructure

### À faire

1. Créer les machines virtuelles ou serveurs
2. Définir l'adressage IP
3. Choisir le nom du domaine
4. Prévoir les noms DNS des services
5. Mettre à jour tous les systèmes
6. Sécuriser les accès administrateur
7. Définir quel service est porté par quelle machine

Le plan d'adressage, les rôles serveurs et la répartition des machines sont définis dans [./ARCHITECTURE.md](./ARCHITECTURE.md).

---

## 2. Installer le contrôleur de domaine

### Ordre

1. Installer Windows Server
2. Configurer une IP fixe
3. Vérifier le nom de machine
4. Lancer le script :
   - *01-Creation_AD.ps1*

### Ce que fait le script

- installe **AD DS**
- crée le domaine **`technova.local`**
- active **DNS**
- crée les **OU**
- crée les **groupes**
- crée les **utilisateurs**
- configure **DHCP**

### Vérifications

- ouverture de session domaine
- présence des OU
- présence des groupes
- présence des utilisateurs
- scope DHCP opérationnel

---

## 3. Compléter les comptes Active Directory

### À faire

Lancer :

- *02-mailUtil.ps1*

### Résultat attendu

Chaque utilisateur AD reçoit une adresse mail de type :

`prenom.nom@technova.local`

### Pourquoi cette étape est importante

Elle doit être faite **avant** les synchronisations applicatives, notamment Rocket.Chat.

---

## 4. Joindre les serveurs Debian au domaine

Le scénario demande une intégration AD côté Linux :

### Ordre

1. installer `realmd`, `sssd`, `adcli`, etc.
2. découvrir le domaine
3. joindre `technova.local`
4. tester un utilisateur AD
5. activer la création automatique des répertoires personnels

### Pourquoi à cette étape

La jointure au domaine nécessite un AD et un DNS opérationnels, mais doit être réalisée **avant** la création des enregistrements DNS applicatifs et des certificats, car les machines doivent être intégrées au domaine pour bénéficier du déploiement de confiance (GPO, magasin de certificats).

---

## 5. Préparer le DNS des services

Créer les enregistrements internes :

- `nextcloud.technova.local`
- `rocket.technova.local`
- `meet.technova.local`
- `n8n.technova.local` *(si utilisé)*

### Vérifications DNS

- résolution du contrôleur de domaine
- résolution des FQDN applicatifs
- cohérence entre noms DNS et IP des serveurs

---

## 6. Préparer les certificats

### Stratégie

Le projet utilise le domaine interne **`technova.local`** : les certificats publics (Let's Encrypt) ne sont pas possibles. La stratégie retenue est donc une **CA interne** (mkcert, PKI interne ou autre).

Si un vrai domaine public était utilisé, **Let's Encrypt** serait une alternative valide.

### Génération

1. créer une autorité de certification interne
2. générer un certificat pour chaque service :
   - `nextcloud.technova.local`
   - `rocket.technova.local`
   - `meet.technova.local`
   - `n8n.technova.local` *(si utilisé)*

### Pose sur les services

La pose se fait **pendant ou juste après l'installation de chaque service** (sections 7 à 10) :

- **Nextcloud** : certificat posé au niveau Apache/Nginx
- **Rocket.Chat** : certificat posé sur le reverse proxy Nginx
- **Jitsi Meet** : certificat posé pendant la configuration du service
- **n8n** : certificat posé sur le reverse proxy si utilisé

### Déploiement de la confiance côté clients

- **côté Windows** via GPO
- **côté Linux** via import dans le magasin système
- éventuellement dans les navigateurs

---

## 7. Installer Nextcloud

### Documentation principale

### Ordre conseillé

1. installer Apache, MariaDB, PHP et dépendances
2. créer la base de données
3. télécharger Nextcloud
4. placer les fichiers dans `/var/www/html/nextcloud`
5. attribuer les permissions
6. configurer Apache
7. terminer l'installation via l'interface web
8. poser le certificat / activer HTTPS
9. configurer Redis, cache, cron et sécurité

### Vérifications

- page de connexion accessible
- création du compte admin
- base de données OK
- HTTPS valide
- cron configuré
- pas d'alerte critique dans l'administration

---

## 8. Installer Rocket.Chat

### Documentation principale

### Ordre conseillé

1. mettre à jour le serveur
2. installer les dépendances
3. installer Docker / Docker Compose si ce mode est retenu
4. préparer le fichier `.env`
5. démarrer Rocket.Chat
6. installer et configurer Nginx
7. poser le certificat sur Nginx / activer HTTPS
8. créer le compte administrateur
9. vérifier l'accès API admin

### Vérifications

- interface web accessible
- connexion administrateur possible
- URL fonctionnelle en HTTPS
- reverse proxy opérationnel

---

## 9. Installer Jitsi Meet

### Documentation principale

### Ordre conseillé

1. préparer le nom d'hôte
2. installer les dépendances
3. ajouter le dépôt Jitsi
4. installer `jitsi-meet`
5. poser le certificat / configurer SSL
6. ouvrir les ports nécessaires
7. activer l'authentification si demandée
8. redémarrer les services

### Vérifications

- accès à l'interface web
- création d'une réunion
- audio et vidéo fonctionnels
- authentification opérationnelle si activée

---

## 10. Installer n8n *(optionnel)*

Le projet mentionne l'automatisation ; le tutoriel n8n peut donc être utilisé comme annexe utile.

### Documentation principale

### Ordre conseillé

1. installer Node.js
2. installer `n8n`
3. créer un utilisateur système dédié
4. créer le service systemd
5. configurer Nginx si nécessaire
6. poser le certificat / activer HTTPS

### Vérifications

- accès à l'interface n8n
- service actif au démarrage
- authentification en place

---

## 11. Réaliser les intégrations

### Intégration Active Directory / LDAP

Configurer selon le besoin :

- Rocket.Chat ↔ LDAP / AD
- Jitsi ↔ LDAP si retenu
- Nextcloud ↔ LDAP / AD si retenu

### Important

Ne faire cette étape **qu'après** :

1. AD opérationnel
2. DNS prêt
3. certificats posés sur les services
4. services installés

### Import des certificats côté Linux

Si des certificats internes AD sont utilisés :

1. copier :
   - *import_ad_certs.sh*
   - *import-ad-certs.service*
2. récupérer le script depuis le partage AD si c'est le mode de distribution retenu
3. installer le script en local dans `/usr/local/bin/`
4. créer le fichier `/etc/import-ad-certs.credentials`
5. sécuriser ses droits (`chmod 600`)
6. activer le service systemd
7. tester la mise à jour du magasin de certificats

### À synchroniser dans chaque application

L'intégration LDAP ne sert pas seulement à permettre la connexion : il faut aussi définir **quelles données AD doivent être reprises** dans chaque application.

### Éléments communs à synchroniser

Dans tous les cas, il faut au minimum prévoir :

- les **comptes utilisateurs**
- les **groupes AD**
- les **noms / prénoms**
- les **identifiants de connexion**
- les **adresses mail**
- éventuellement l'**appartenance à des OU** si elle sert au filtrage

### Rocket.Chat

Pour Rocket.Chat, la synchronisation LDAP doit surtout porter sur :

- les **comptes utilisateurs** autorisés à se connecter
- les **adresses mail** des utilisateurs
- les **groupes AD** utilisés pour les rôles ou permissions
- les éventuels **administrateurs** ou **modérateurs**

Points d'attention :

- les **canaux Rocket.Chat** ne sont pas des objets LDAP natifs ; ils doivent souvent être **créés côté application**
- en revanche, les **groupes AD** peuvent servir à décider **qui accède à quel canal**
- il faut donc définir une correspondance du type :
  - groupe AD `Direction` → canal privé Direction
  - groupe AD `Support` → canal Support
  - groupe AD `Technique` → canal Technique
- il faut aussi décider si les utilisateurs doivent être :
  - créés automatiquement à la première connexion
  - synchronisés régulièrement
  - désactivés si le compte AD est désactivé

### Nextcloud

Pour Nextcloud, la synchronisation doit porter sur :

- les **comptes utilisateurs**
- les **groupes AD**
- les **noms affichés**
- les **adresses mail**

Les groupes peuvent ensuite servir à :

- gérer les **droits d'accès** aux dossiers
- partager des fichiers ou répertoires à une équipe
- limiter certaines fonctionnalités à certains profils

Exemples :

- groupe AD `RH` → accès au dossier RH
- groupe AD `Technique` → accès au dossier Documentation technique
- groupe AD `Direction` → accès aux documents de pilotage

### Jitsi Meet

Pour Jitsi, LDAP sert surtout à la **gestion de l'authentification** si cette option est retenue.

En général, on synchronise surtout :

- les **comptes autorisés à créer des réunions**
- éventuellement un **groupe AD** réservé aux animateurs / organisateurs
- les **identifiants utilisateurs**

À noter :

- les **salles Jitsi** ne sont pas synchronisées depuis LDAP
- les droits portent surtout sur **qui peut créer ou modérer une réunion**
- les participants externes peuvent rester possibles selon le niveau de sécurité retenu

### n8n *(si utilisé)*

Pour n8n, la synchronisation LDAP dépend du mode d'authentification retenu.

Si une intégration annuaire est mise en place, il faut surtout prévoir :

- les **comptes autorisés à accéder à n8n**
- les **administrateurs de la plateforme**
- éventuellement des **groupes AD** pour séparer administration et usage simple

### Ce qu'il faut décider avant la démonstration

Avant la validation finale, l'équipe doit être capable d'expliquer clairement :

- **quels comptes AD** sont synchronisés dans chaque application
- **quels groupes AD** sont utilisés
- **quels droits** ces groupes donnent
- **quels canaux / espaces / dossiers** doivent exister dans chaque outil
- **quelles créations sont automatiques** et **quelles créations restent manuelles**
- **ce qui se passe quand un compte AD est désactivé**

---

## 12. Validation technique

Utiliser la *CHECKLIST_VALIDATION.pdf* pour valider l'ensemble du déploiement.

---

## 13. Documentation et passation

### À produire

1. une documentation technique administrateur
2. un guide utilisateur simple
3. un plan de tests
4. une synthèse de déploiement
5. un retour d'expérience

### Conseils

- garder les tutoriels en annexe
- utiliser ce document comme **référence unique**
- garder les scripts séparés dans `script/`

---

## 14. Résumé ultra-court

L'ordre final à respecter est :

1. **Infrastructure**
2. **AD / DNS / DHCP**
3. **Utilisateurs / groupes / mails**
4. **Jointure Debian au domaine**
5. **DNS des services**
6. **Certificats (génération)**
7. **Nextcloud**
8. **Rocket.Chat**
9. **Jitsi**
10. **n8n** *(optionnel)*
11. **Déploiement de confiance côté clients**
12. **Intégrations LDAP / scripts**
13. **Tests**
14. **Documentation**
