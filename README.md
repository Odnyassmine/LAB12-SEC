# Contournement de la Détection Root avec Medusa et Frida

## 📌 Présentation

Ce laboratoire présente une approche défensive de contournement de la détection root sur Android à l’aide de **Frida** et **Medusa**.

L’objectif est de comprendre comment les applications Android détectent un appareil rooté et comment les hooks dynamiques permettent de neutraliser ces vérifications dans un cadre pédagogique et autorisé.

Le laboratoire utilise :

- Frida pour l’instrumentation dynamique
- Frida-Server sur Android
- Medusa pour automatiser les hooks de bypass root
- Une application vulnérable : **Uncrackable Level 3**

---

# 🎯 Objectifs du Lab

À la fin de ce laboratoire, l’apprenant sera capable de :

- Comprendre les mécanismes de détection root Android
- Identifier les contrôles Java et natifs
- Utiliser Frida pour injecter des hooks dynamiques
- Utiliser Medusa pour automatiser un bypass root
- Vérifier le fonctionnement des hooks
- Diagnostiquer les échecs de bypass

---

# ⚖️ Cadre légal et éthique

Ce laboratoire est strictement pédagogique et défensif.

## Autorisé uniquement sur :

- Applications de test
- APK pédagogiques
- Environnements explicitement autorisés
- Émulateurs Android personnels

## Interdit :

- Analyse non autorisée
- Bypass sur applications tierces réelles
- Contournement malveillant
- Exploitation offensive

---

# 🧰 Prérequis

- Frida installé côté PC
- frida-server fonctionnel côté Android
- Android Platform Tools (ADB)
- Émulateur Android ou appareil physique
- Medusa installé
- Application de test vulnérable

---

# 🔎 Vérifications initiales

## Vérifier Frida

```bash
frida --version
```

## Vérifier Python Frida

```bash
python -c "import frida; print(frida.__version__)"
```

## Vérifier ADB

```bash
adb devices
```

---

# 🚀 Démarrage de Frida-Server

## Identifier l’architecture CPU

```bash
adb shell getprop ro.product.cpu.abi
```

## Pousser Frida-Server

```bash
adb push frida-server /data/local/tmp/
```

## Donner les permissions

```bash
adb shell chmod 755 /data/local/tmp/frida-server
```

## Lancer Frida-Server

```bash
adb shell "/data/local/tmp/frida-server -l 0.0.0.0"
```

## Forward des ports

```bash
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
```

## Vérifier la connexion

```bash
frida-ps -Uai
```

---

# 🛡️ Détection Root Android

Les applications Android utilisent plusieurs techniques pour détecter un environnement rooté.

## Vérifications Java

- Build.TAGS
- Recherche du binaire `su`
- Vérification BusyBox
- Runtime.exec()
- Librairies RootBeer

## Vérifications natives

- open()
- access()
- stat()
- fopen()
- Lecture de `/proc/mounts`

## Anti-Frida

- Détection des ports Frida
- Recherche de chaînes “frida”
- Détection de debugging

---

# 🧪 Étape 1 — État initial : Root détecté

L’application Uncrackable Level 3 détecte le root dès son lancement.

Message affiché :

> “Rooting or tampering detected. This is unacceptable. The app is now going to exit.”

L’application ferme automatiquement son exécution.

---

# ⚙️ Étape 2 — Préparation de l’environnement

## Vérifier l’appareil

```bash
adb devices
```

## Lancer Frida-Server

```bash
adb shell "/data/local/tmp/frida-server -l 0.0.0.0"
```

---

# 🧬 Étape 3 — Lancement de Medusa

Medusa permet d’utiliser rapidement des modules Frida prêts à l’emploi.

Informations récupérées automatiquement :

- Android 11
- API 30
- Émulateur Android
- Propriétés système

---

# 🔓 Étape 4 — Exécution du bypass root

## Commande utilisée

```bash
python medusa.py --usb --spawn com.example.uncrackable3 --module root-bypass
```

---

# 🪝 Hooks utilisés

## Hook Build.TAGS

Retour forcé :

```text
release-keys
```

---

## Hook File.exists()

Blocage des chemins sensibles :

```text
/system/xbin/su
/system/bin/su
```

Retour :

```text
false
```

---

## Hook Runtime.exec()

Blocage des commandes :

```text
su
busybox
which su
```

---

# ✅ Résultat

Après injection des hooks :

- la détection root disparaît,
- l’application continue son exécution,
- les protections sont neutralisées.

---

# 🔍 Vérification des hooks

Les logs Frida permettent de vérifier :

- les appels interceptés,
- les vérifications root bloquées,
- les hooks chargés correctement.

---

# 🛠️ Dépannage Rapide

## Root toujours détecté ?

Certaines applications utilisent du code natif C/C++.

Dans ce cas :

- activer les Native Hooks Medusa,
- intercepter `fopen()`,
- intercepter `access()`,
- vérifier les contrôles JNI.

---

## Vérifier les versions Frida

Le client Frida et frida-server doivent avoir exactement la même version.

```bash
frida --version
```

---

# 📚 Références Sécurité

Ce laboratoire s’appuie sur :

- OWASP
- OWASP MASVS
- OWASP MASTG

---

# 📷 Screenshots

## 1. Détection root initiale

![Root Detection](2.png)

---

## 2. Console Frida / Medusa

![Frida Console](3.png)

---

## 3. Bypass confirmé

![Bypass Confirmed](4.png)

---

## 4. Application sur émulateur

![Emulator](1.png)
