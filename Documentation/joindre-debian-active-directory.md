# Intégrer une machine Debian à un domaine Active Directory

## Prérequis

- Une machine Debian avec une **IP fixe** configurée
- Le **DNS** pointant vers le contrôleur de domaine (ex: `192.168.192.10`)
- Le **nom de la machine** correctement défini
- Les identifiants **Administrateur** du domaine

---

## Étape 1 — Configurer l'IP fixe et le DNS

Éditez le fichier `/etc/network/interfaces` :

```
auto lo
iface lo inet loopback

auto ens33
iface ens33 inet static
    address 192.168.192.40
    netmask 255.255.255.0
    gateway 192.168.192.2
    dns-nameservers 192.168.192.10
```

Appliquez la configuration :

```bash
systemctl restart networking
```

---

## Étape 2 — Définir le nom de la machine

```bash
hostnamectl set-hostname nom-de-la-machine
```

---

## Étape 3 — Installer les paquets nécessaires

```bash
apt install -y realmd sssd sssd-tools adcli krb5-user packagekit samba-common samba-common-bin
```

Lors de l'installation, renseigner le royaume Kerberos en majuscules :

```
TECHNOVA.LOCAL
```

---

## Étape 4 — Vérifier que le domaine est détecté

```bash
realm discover technova.local
```

Le résultat doit afficher `server-software: active-directory` et `client-software: sssd`.

---

## Étape 5 — Joindre le domaine

```bash
realm join --user=Administrateur technova.local
```

Saisir le mot de passe administrateur du domaine. L'absence de message d'erreur indique que l'opération a réussi.

---

## Étape 6 — Vérifier l'intégration

```bash
realm list
```

Le champ `configured` doit afficher `kerberos-member`.

Vérifier également côté Windows que la machine apparaît dans **Utilisateurs et ordinateurs Active Directory > Computers**.
