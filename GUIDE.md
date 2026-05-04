# Guide — Builder l'APK VidQuest avec GitHub Actions

> ✅ Aucun Android Studio requis · Build dans le cloud · Gratuit

---

## Ce que tu vas obtenir

Un fichier **VidQuest.apk** téléchargeable directement depuis GitHub, que tu peux distribuer sur ton site web. L'app est une WebView qui charge ton site `thegreatestvalt-prog.github.io/vidquest`.

---

## Étape 1 — Créer un nouveau dépôt GitHub

1. Va sur **github.com** → connecte-toi avec le compte `thegreatestvalt-prog`
2. Clique **`+`** → **New repository**
3. Nom : **`vidquest-apk`**
4. Sélectionne **Public**
5. **Ne pas** cocher "Add README"
6. Clique **Create repository**

---

## Étape 2 — Uploader tous les fichiers de ce dossier

### Option A — Via l'interface GitHub (plus simple)

1. Dans le dépôt vide, clique **Add file → Upload files**
2. **Glisse tout le contenu de ce dossier** `vidquest-apk/` dans la zone
3. ⚠️ Vérifie que la structure est respectée (les sous-dossiers doivent être présents)
4. Message de commit : `init: Capacitor Android project`
5. Clique **Commit changes**

### Option B — Via Git en ligne de commande

```bash
cd vidquest-apk
git init
git add .
git commit -m "init: Capacitor Android project"
git remote add origin https://github.com/thegreatestvalt-prog/vidquest-apk.git
git push -u origin main
```

---

## Étape 3 — Activer GitHub Actions

1. Dans le dépôt `vidquest-apk`, clique l'onglet **Actions**
2. Si GitHub demande d'activer les Actions → clique **I understand my workflows, go ahead and enable them**
3. Dans le menu gauche, tu veras **Build VidQuest APK**
4. Clique dessus → **Run workflow** → **Run workflow** (bouton vert)

Le build prend **5 à 10 minutes**.

---

## Étape 4 — Télécharger l'APK

Une fois le build terminé (✅ vert) :

**Option A — Via Releases (recommandé)**
1. Va dans l'onglet **Releases** (côté droit de la page principale du dépôt)
2. Tu verras `VidQuest v1.0.X`
3. Clique sur **VidQuest-v1.0.X.apk** pour télécharger

**Option B — Via les Artifacts**
1. Clique sur le build terminé dans **Actions**
2. En bas de la page → **Artifacts** → clique **VidQuest-APK**

---

## Étape 5 — Ajouter le lien de téléchargement sur ton site

Dans ton `index.html` VidQuest, ajoute ce lien :

```
https://github.com/thegreatestvalt-prog/vidquest-apk/releases/latest/download/VidQuest-v1.0.0.apk
```

Ce lien pointe **toujours vers la dernière version**.

---

## Étape 6 — Mettre à jour l'APK

Chaque fois que tu pousses du code sur la branche `main` du dépôt `vidquest-apk`, le build se relance automatiquement et une nouvelle Release est créée.

Si tu veux juste forcer un rebuild sans modifier le code :
1. **Actions → Build VidQuest APK → Run workflow**

---

## Permettre l'installation sur Android (instructions pour tes utilisateurs)

Tes utilisateurs devront activer les "Sources inconnues" :

- **Android 8+** : Paramètres → Apps → Paramètres spéciaux → Installer des apps inconnues → Autoriser pour le navigateur
- **Android 7 et moins** : Paramètres → Sécurité → Sources inconnues → Activer

---

## Structure du projet

```
vidquest-apk/
├── .github/
│   └── workflows/
│       └── build-apk.yml          ← Le workflow de build (GitHub Actions)
├── android/
│   ├── app/
│   │   ├── build.gradle           ← Config build Android
│   │   └── src/main/
│   │       ├── AndroidManifest.xml
│   │       ├── java/com/vidquest/app/
│   │       │   └── MainActivity.java
│   │       └── res/
│   │           ├── values/
│   │           │   ├── strings.xml
│   │           │   ├── styles.xml
│   │           │   └── colors.xml
│   │           └── xml/
│   │               ├── network_security_config.xml
│   │               └── file_paths.xml
│   ├── build.gradle               ← Config Gradle racine
│   ├── settings.gradle
│   ├── variables.gradle
│   └── gradle/wrapper/
│       └── gradle-wrapper.properties
├── www/
│   └── index.html                 ← Page de chargement (remplacée par le site live)
├── capacitor.config.json          ← Config Capacitor (URL de ton site)
└── package.json                   ← Dépendances npm
```

---

## Problèmes courants

**Build échoue avec "gradlew not found"**
→ Vérifie que le fichier `android/gradle/wrapper/gradle-wrapper.properties` est bien uploadé

**L'app s'ouvre mais page blanche**
→ Vérifie que l'URL dans `capacitor.config.json` est correcte :
`"url": "https://thegreatestvalt-prog.github.io/vidquest"`

**Erreur "minSdkVersion too low"**
→ L'app nécessite Android 6.0 minimum (API 23) — les appareils plus anciens ne sont pas supportés

---

## Après la première version

Pour une vraie signature APK (obligatoire pour Google Play) tu auras besoin de :
1. Générer un keystore : `keytool -genkey -v -keystore vidquest.keystore -alias vidquest -keyalg RSA -keysize 2048 -validity 10000`
2. Ajouter les secrets dans GitHub : `KEYSTORE_FILE`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`

Pour l'instant, l'APK debug est parfait pour la distribution directe.
