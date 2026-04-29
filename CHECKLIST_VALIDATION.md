# Checklist de validation

## But

Cette checklist permet de valider que le projet est :

- **installé**
- **fonctionnel**
- **sécurisé**
- **documenté**

Elle peut être utilisée :

- pendant les tests
- avant une démonstration
- avant un rendu

---

## 1. Infrastructure

- [ ] Les serveurs sont créés et démarrent correctement
- [ ] Les adresses IP sont fixes pour les serveurs
- [ ] Les noms de machines sont définis
- [ ] Le réseau entre les machines fonctionne
- [ ] Les mises à jour système sont faites

---

## 2. Active Directory / DNS / DHCP

- [ ] Le domaine `technova.local` existe
- [ ] Le contrôleur de domaine répond correctement
- [ ] Le DNS résout les noms internes
- [ ] Le DHCP distribue des adresses valides
- [ ] Les OU sont présentes
- [ ] Les groupes sont présents
- [ ] Les utilisateurs sont présents
- [ ] Les adresses mail AD sont renseignées

---

## 3. Certificats et sécurité

- [ ] Les certificats ont été générés
- [ ] Les services web sont accessibles en HTTPS
- [ ] Les navigateurs ne remontent pas d'erreur de certificat
- [ ] Le certificat est bien déployé sur les postes du domaine si nécessaire
- [ ] Les accès admin sont séparés des comptes utilisateurs

---

## 4. Nextcloud

- [ ] Le site Nextcloud est accessible
- [ ] Le compte administrateur fonctionne
- [ ] La base de données est connectée
- [ ] Un utilisateur peut se connecter
- [ ] Un fichier peut être déposé
- [ ] Un fichier peut être partagé
- [ ] Les tâches cron sont configurées
- [ ] L'administration ne remonte pas d'alerte majeure


---

## 5. Rocket.Chat

- [ ] Rocket.Chat est accessible
- [ ] Le compte administrateur fonctionne
- [ ] Le reverse proxy fonctionne
- [ ] L'accès HTTPS fonctionne
- [ ] L'API admin est utilisable
- [ ] Les utilisateurs synchronisés apparaissent bien
- [ ] Les canaux sont créés correctement
- [ ] Un message peut être envoyé dans un canal

---

## 6. Jitsi Meet

- [ ] Jitsi Meet est accessible
- [ ] Une salle peut être créée
- [ ] Le son fonctionne
- [ ] La vidéo fonctionne
- [ ] Les ports nécessaires sont ouverts
- [ ] L'authentification fonctionne si elle a été activée

---

## 7. n8n *(si utilisé)*

- [ ] n8n est accessible
- [ ] Le service démarre automatiquement
- [ ] L'authentification est activée
- [ ] Un workflow simple fonctionne

---

## 8. Intégrations

- [ ] Les comptes AD sont exploitables dans les services visés
- [ ] La synchronisation Rocket.Chat fonctionne
- [ ] Les groupes AD sont bien traduits en canaux Rocket.Chat
- [ ] Les certificats internes sont reconnus côté Linux si nécessaire
- [ ] Les intégrations choisies sont testées

---

## 9. Scripts

- [ ] Le script AD s'exécute correctement
- [ ] Le script d'ajout des mails fonctionne
- [ ] Le script d'import de certificats Linux fonctionne si utilisé

Référence : *SCRIPTS.md*

---

## 10. Documentation et rendu

- [ ] Le README est à jour
- [ ] L'architecture est documentée
- [ ] La procédure d'installation est documentée
- [ ] Les tutoriels annexes sont présents
- [ ] La démonstration peut être faite dans un ordre clair

---

## Validation finale

- [ ] Le projet est prêt pour une démonstration
- [ ] Le projet est prêt pour une correction
- [ ] Le projet est suffisamment documenté pour être repris par un autre étudiant
