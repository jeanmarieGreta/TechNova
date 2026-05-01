# Procédure d'installation automatique de Rocket.Chat sur Debian 13

## 1. Objectif

Cette procédure permet d'automatiser l'installation de Rocket.Chat sur une machine Debian 13 propre.

Le principe est simple :

- le fichier `.env` contient les paramètres ;
- le script `install_rocketchat.sh` lit le fichier `.env` ;
- le script installe les dépendances nécessaires ;
- le script installe Docker et Docker Compose si besoin ;
- le script crée automatiquement le fichier `compose.yml` ;
- le script lance MongoDB et Rocket.Chat.

---

## 2. Fichiers nécessaires

Les deux fichiers doivent être dans le même dossier :

```text
install_rocketchat.sh
.env
```

Exemple :

```text
installation-rocketchat/
├── install_rocketchat.sh
└── .env
```

Le fichier `.env` doit être dans le même dossier que le script, car le script va le lire automatiquement.

---

## 3. Créer le dossier de travail

Sur ta Debian 13 :

```bash
mkdir -p ~/installation-rocketchat
cd ~/installation-rocketchat
```

Place ensuite les deux fichiers dans ce dossier.

Vérifie :

```bash
ls -la
```

Tu dois voir :

```text
.env
install_rocketchat.sh
```

---

## 4. Modifier le fichier `.env`

Ouvre le fichier `.env` :

```bash
nano .env
```

La ligne importante est :

```env
ROOT_URL="http://ADRESSE_IP_DU_SERVEUR:3000"
```

Tu dois remplacer `ADRESSE_IP_DU_SERVEUR` par l'adresse IP réelle de ta Debian.

Pour connaître l'adresse IP de ta machine Debian vierge :

```bash
hostname -I
```

Sur une machine propre, tu verras normalement une seule adresse IP.

Exemple :

```text
192.168.42.139
```

Dans ce cas, tu modifies la ligne comme ceci :

```env
ROOT_URL="http://192.168.42.139:3000"
```

Enregistre ensuite le fichier :

```text
Ctrl + O
Entrée
Ctrl + X
```

---

## 5. Modifier le compte administrateur si besoin

Toujours dans le fichier `.env`, tu peux modifier ces lignes :

```env
ADMIN_USERNAME="admin"
ADMIN_NAME="Administrateur Rocket.Chat"
ADMIN_EMAIL="admin@example.local"
ADMIN_PASS="Admin123456789!"
```

Important : les valeurs avec espaces doivent rester entre guillemets.

Correct :

```env
ADMIN_NAME="Administrateur Rocket.Chat"
```

Incorrect :

```env
ADMIN_NAME=Administrateur Rocket.Chat
```

---

## 6. Rendre le script exécutable

Depuis le dossier `~/installation-rocketchat` :

```bash
chmod +x install_rocketchat.sh
```

---

## 7. Lancer l'installation automatique

Toujours depuis le même dossier :

```bash
./install_rocketchat.sh
```

Le script va automatiquement :

1. lire le fichier `.env` ;
2. vérifier le système ;
3. installer les dépendances manquantes comme `curl`, `gnupg`, `ca-certificates` ;
4. installer Docker si nécessaire ;
5. installer Docker Compose si nécessaire ;
6. ajouter l'utilisateur courant au groupe `docker` ;
7. créer le dossier `/opt/rocketchat` ;
8. copier le `.env` dans `/opt/rocketchat` ;
9. créer le fichier `/opt/rocketchat/compose.yml` ;
10. télécharger les images Docker ;
11. démarrer MongoDB et Rocket.Chat ;
12. afficher l'adresse d'accès.

---

## 8. Redémarrer après installation

À la fin de l'installation, le script recommande de redémarrer :

```bash
sudo reboot
```

Ce redémarrage est important pour appliquer correctement les droits Docker de ton utilisateur.

---

## 9. Vérifier après redémarrage

Après redémarrage, reconnecte-toi à Debian puis vérifie Docker :

```bash
docker ps
```

Ensuite :

```bash
cd /opt/rocketchat
docker compose ps
```

Tu dois voir au minimum :

```text
rocketchat
rocketchat-mongodb
```

---

## 10. Voir les logs Rocket.Chat

```bash
cd /opt/rocketchat
docker compose logs -f rocketchat
```

Pour quitter les logs :

```text
Ctrl + C
```

Cela ne coupe pas Rocket.Chat. Cela quitte seulement l'affichage des logs.

---

## 11. Accéder à Rocket.Chat

Depuis ton navigateur :

```text
http://IP_DE_TA_DEBIAN:3000
```

Exemple :

```text
http://192.168.42.139:3000
```

---

## 12. Identifiants par défaut

Si tu as gardé le `.env` fourni :

```text
Utilisateur : admin
E-mail      : admin@example.local
Mot de passe: Admin123456789!
```

Tu peux tester avec :

```text
admin
```

ou :

```text
admin@example.local
```

Attention : ce compte est créé surtout lors du premier démarrage avec une base MongoDB neuve.

---

## 13. Commandes utiles

Voir les conteneurs :

```bash
cd /opt/rocketchat
docker compose ps
```

Voir les logs :

```bash
cd /opt/rocketchat
docker compose logs -f
```

Redémarrer Rocket.Chat :

```bash
cd /opt/rocketchat
docker compose restart
```

Arrêter Rocket.Chat :

```bash
cd /opt/rocketchat
docker compose down
```

Relancer Rocket.Chat :

```bash
cd /opt/rocketchat
docker compose up -d
```

Mettre à jour les images :

```bash
cd /opt/rocketchat
docker compose pull
docker compose up -d
```

---

## 14. Repartir de zéro

Attention : cette commande supprime les données Rocket.Chat, les comptes, les messages et les fichiers.

```bash
cd /opt/rocketchat
docker compose down -v
docker compose up -d
```

---

## 15. Dépannage rapide

### Erreur Docker permission denied

Si tu vois :

```text
permission denied while trying to connect to the docker API
```

Redémarre :

```bash
sudo reboot
```

Si le problème continue :

```bash
sudo usermod -aG docker $USER
sudo reboot
```

---

### Rocket.Chat ne répond pas

Vérifie :

```bash
cd /opt/rocketchat
docker compose ps
docker compose logs -f rocketchat
```

Teste localement :

```bash
curl -I http://localhost:3000
```

---

### Rocket.Chat demande encore l'enregistrement Cloud

Le script essaye de passer l'assistant en mode terminé.

Si besoin :

```bash
cd /opt/rocketchat
docker exec -it rocketchat-mongodb mongosh
```

Puis :

```javascript
use rocketchat

db.rocketchat_settings.updateOne(
  { _id: "Show_Setup_Wizard" },
  { $set: { value: "completed" } },
  { upsert: true }
)

db.rocketchat_settings.updateOne(
  { _id: "Setup_Wizard" },
  { $set: { value: "completed" } },
  { upsert: true }
)
```

Quitter :

```text
.exit
```

Puis :

```bash
cd /opt/rocketchat
docker compose restart rocketchat
```

---

## 16. Conclusion

La procédure normale est :

```bash
mkdir -p ~/installation-rocketchat
cd ~/installation-rocketchat
nano .env
chmod +x install_rocketchat.sh
./install_rocketchat.sh
sudo reboot
```

Après redémarrage :

```bash
cd /opt/rocketchat
docker compose ps
```

Puis accès navigateur :

```text
http://IP_DE_TA_DEBIAN:3000
```
