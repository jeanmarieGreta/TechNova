# Contexte Entreprise — TechNova

## 1. Présentation de l'entreprise
**TechNova** est une PME technologique en pleine croissance, spécialisée dans le développement de solutions logicielles innovantes et le conseil IT. Fondée il y a 5 ans, l'entreprise compte aujourd'hui **45 collaborateurs** basés au siège social, avec une part croissante de télétravail.

### Structure organisationnelle (45 collaborateurs)
L'entreprise est structurée en 7 pôles de compétences :
- **Direction** (5 pers.) : Pilotage stratégique.
- **Ressources Humaines** (4 pers.) : Gestion administrative et recrutement.
- **IT / Systèmes** (6 pers.) : Maintenance et infrastructure (votre équipe).
- **Développement** (12 pers.) : Production logicielle.
- **Support client** (8 pers.) : Assistance technique.
- **Commercial** (7 pers.) : Développement des ventes.
- **Marketing** (3 pers.) : Communication et image de marque.

---

## 2. Situation actuelle et Problématiques
Malgré son expertise technologique, l'infrastructure interne de TechNova n'a pas suivi la croissance de l'effectif. L'entreprise fait face à plusieurs défis critiques :

1.  **Silos d'information et Shadow IT** : Faute d'outils officiels, les employés utilisent des solutions personnelles (WhatsApp pour la messagerie, Google Drive ou Dropbox personnels pour les fichiers).
2.  **Absence de centralisation** : Il n'existe pas d'annuaire d'entreprise. Chaque service gère ses accès de manière isolée, rendant l'administration complexe et chronophage.
3.  **Risques de Sécurité** : La dispersion des données sur des serveurs tiers (Cloud public non maîtrisé) et l'absence de traçabilité des accès font peser un risque juridique (RGPD) et technique (fuites de données).
4.  **Inefficacité Opérationnelle** : Le processus d'onboarding d'un nouveau collaborateur est manuel (création de comptes séparés sur chaque outil), prenant environ 8 minutes par utilisateur avec un taux d'erreur de 11 %.

---

## 3. Objectifs du Projet
La direction a validé le projet d'intégration d'une **Suite Collaborative Interne** baptisée "**TechNova Connect**". Les objectifs principaux sont :

- **Centraliser l'identité** : Déployer un annuaire Active Directory pour gérer les comptes et les groupes de manière unique.
- **Sécuriser les échanges** : Héberger toutes les solutions en interne et chiffrer les communications (HTTPS).
- **Améliorer la collaboration** : Fournir des outils professionnels de partage de fichiers (Nextcloud), de messagerie (Rocket.Chat) et de visioconférence (Jitsi Meet).
- **Automatiser** : Réduire les erreurs et le temps d'administration via des scripts et des outils de workflow (n8n).

---

## 4. Contraintes et Périmètre
- **Budget** : Limité, favorisant l'utilisation de solutions Open Source sans coûts de licence récurrents.
- **Hébergement** : 100% On-Premise pour garantir la souveraineté des données.
- **Délais** : Mise en place d'un POC (Proof of Concept) fonctionnel sous 2 semaines pour validation avant déploiement global.
- **Technique** : Intégration LDAP/AD obligatoire pour tous les services afin de garantir le Single Source of Truth (SSoT).
