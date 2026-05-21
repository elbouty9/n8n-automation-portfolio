# 🤖 Agent IA Recherche Emploi — Documentation

![n8n](https://img.shields.io/badge/n8n-self--hosted-6c63ff?style=flat-square&logo=n8n)
![GPT](https://img.shields.io/badge/GPT-5.4-00e5a0?style=flat-square&logo=openai)
![License](https://img.shields.io/badge/license-MIT-ff6b35?style=flat-square)
![Status](https://img.shields.io/badge/status-production-success?style=flat-square)

> **Chaque matin à 9h, cet agent scrape 100+ offres, les score avec GPT selon ton profil, génère une lettre de motivation et t'envoie seulement les meilleures par email. Zéro effort manuel.**

---

## ✨ Ce que ça fait

```
⏰ 9h00        → Cron Trigger se déclenche
🔍 9h00-9h01   → Scraping 100+ offres sur 5 recherches simultanées
🧹 9h01        → Dédoublonnage par ID unique
📊 9h01-9h02   → Scoring GPT 0-100 selon ton profil
✉️ 9h02        → Génération lettre de motivation personnalisée
📬 9h02        → Email avec les meilleures offres (score ≥ 75)
💾 9h02        → Sauvegarde dans Google Sheets (anti-doublon)
```

---

## 🏗️ Architecture

```
Schedule Trigger (9h)
│
├── HTTP Request (n8n)
├── HTTP Request (automatisation ia)
├── HTTP Request (agent IA développeur)          5 recherches
├── HTTP Request (chef de projet automatisation) simultanées
└── HTTP Request (intégrateur no-code)
         │
         ▼
      Merge (5 inputs)
         │
         ▼
   Code — Extraction & normalisation des offres
         │
         ▼
   Remove Duplicates (par ID Adzuna)
         │
         ▼
   Google Sheets — Check doublons historique
         │
         ▼
   Code — Filtre nouvelles offres uniquement
         │
         ▼
   GPT — Scoring 0-100 selon profil candidat
         │
         ▼
   Code — Parsing JSON GPT
         │
         ▼
   IF score ≥ 75
   │
   ├── ✅ TRUE  → Gmail (email + lettre de motivation)
   │              → Google Sheets (sauvegarde)
   │
   └── ❌ FALSE → Ignoré
```

---

## 🛠️ Stack technique

| Outil | Usage |
|-------|-------|
| **n8n** | Orchestration du workflow |
| **Adzuna API** | Source des offres d'emploi |
| **OpenAI GPT** | Scoring et génération lettre de motivation |
| **Gmail** | Envoi des alertes email |
| **Google Sheets** | Stockage anti-doublon |
| **JavaScript** | Parsing et filtrage des données |

---

## ⚡ Prérequis

- n8n self-hosted (VPS) ou n8n Cloud
- Compte [Adzuna Developer](https://developer.adzuna.com) (gratuit)
- Compte OpenAI avec clé API
- Gmail configuré en OAuth2 dans n8n
- Google Sheets configuré en OAuth2 dans n8n

---

## 🚀 Installation

### 1. Importer le workflow dans n8n
1. Ouvre ton instance n8n
2. Clique sur **"Import from file"**
3. Sélectionne le fichier `Veille emploi automatique.json` reçu en DM

### 2. Configurer les credentials

| Variable | Où la trouver |
|----------|---------------|
| `ADZUNA_APP_ID` | [developer.adzuna.com](https://developer.adzuna.com) → Your Apps |
| `ADZUNA_API_KEY` | [developer.adzuna.com](https://developer.adzuna.com) → Application Keys |
| `OPENAI_API_KEY` | [platform.openai.com](https://platform.openai.com) → API Keys |
| Gmail OAuth2 | n8n → Credentials → Gmail OAuth2 |
| Google Sheets OAuth2 | n8n → Credentials → Google Sheets OAuth2 |

### 3. Configurer Google Sheets
Crée une feuille avec ces deux colonnes :
```
| lien | date |
```

### 4. Personnaliser ton profil dans GPT
Dans le node **"Scorer l'offre d'emploi"**, modifie la section profil :
```
- Compétences : [tes compétences]
- Formation : [ta formation]
- Expérience : [ton expérience]
```

### 5. Personnaliser les mots-clés de recherche
Dans chaque node HTTP Request, modifie le paramètre `what` selon ton domaine :
```
n8n → automatisation workflow → agent IA → ...
```

### 6. Activer le workflow
Clique sur le toggle **"Active"** — le workflow tournera automatiquement chaque matin à 9h !

---

## 🎯 Personnalisation

### Changer le seuil de score
Dans le node **"Si score ≥ 75"**, modifie la valeur :
```
75 → 80 (plus sélectif)
75 → 60 (plus permissif)
```

### Changer l'heure de déclenchement
Dans le **Schedule Trigger**, modifie `triggerAtHour` :
```json
"triggerAtHour": 9
```

---

## 📬 Exemple d'email reçu

```
🎯 88/100 — AI & Ops Automation chez Pennylane

Entreprise : Pennylane SAS
Score : 88/100

Pourquoi postuler :
Très bon match entre le profil et le poste AI & Ops Automation.
Les compétences sont directement alignées avec l'automatisation
des opérations et l'IA appliquée...

👉 Voir l'offre
```

---

## 🤝 Questions ?

Tu as des questions sur l'installation ou la configuration ?

Contacte-moi directement sur LinkedIn 👇

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Jérémy_Taverne-0077B5?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/jérémy-taverne-2679b4129/)

---

*Fait avec ❤️ et n8n · Si ce workflow t'a aidé, partage-le autour de toi !*
