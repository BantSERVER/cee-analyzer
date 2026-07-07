# 🔍 CEE Analyzer — Assistant IA d'analyse de documents CEE

> **Outil de détection et valorisation des Certificats d'Économies d'Énergie (CEE)**
> Développé par **Jessy KOUOLA** — Chargé de Financement CEE
> Déployé en production chez un bailleur social francilien · 2025

---

## 📋 Présentation

**CEE Analyzer** est une application web standalone permettant d'analyser automatiquement des documents techniques (DPGF, devis, fiches marchés — jusqu'à 100+ pages) pour détecter les fiches CEE valorisables, puis pré-remplir les lignes de saisie d'un tableau de suivi de portefeuille.

L'outil s'appuie sur l'IA (Mistral AI) pour lire les documents et les confronter au référentiel officiel des 116 fiches standardisées BAR/BAT/RES publiées sur [ecologie.gouv.fr](https://www.ecologie.gouv.fr).

**Impact mesuré : réduction du temps de traitement de plusieurs heures à quelques secondes par document.**

---

## ✨ Fonctionnalités

### 🔍 Analyse intelligente
- Détection automatique des fiches CEE applicables depuis les documents (PDF, Excel, Word)
- Distinction **Confirmée** / **À vérifier** avec preuve textuelle exacte et point de blocage précis
- Extraction des données clés : identifiants opération, entreprise, certification RGE, COFRAC, cumac, dates

### 📋 Saisie guidée & Export
- Tableau de saisie éditable avec les **36 colonnes** du tableau de suivi de portefeuille
- Règles métier automatiques : détermination de la **période CEE (P5/P6)** et attribution de l'obligé selon des règles configurables
- Calculatrice cumac intégrée : Classique + Précarité = Total → Montant prévisionnel
- Export Excel au format exact du tableau de suivi

### 📁 Historique & Sessions
- Sauvegarde automatique des analyses en localStorage (30 sessions)
- Moteur de recherche sur les analyses passées (identifiant, commune, fiche, entreprise)
- Restauration complète d'une session en un clic

### 💬 Assistant CEE
- Chat contextuel basé sur les documents analysés
- Réponses directes et structurées sur fiches, conditions d'éligibilité, calculs cumac
- Base de connaissance réglementaire construite par scraping des sources officielles
- Possibilité de joindre des documents supplémentaires au chat

### 📄 Référentiel officiel
- **116 fiches officielles** BAR + BAT + RES (source : ecologie.gouv.fr)
- Liens directs vers les PDFs officiels par fiche
- Badges **P6 ✓** et numéro de version par fiche
- Mentions légales obligatoires par fiche (conditions de dossier recevable)

---

## 🏗️ Architecture technique

```
CEE_Analyzer_v8.html            ← Version principale (nécessite accès CDN)
CEE_Analyzer_v8_standalone.html ← Version autonome (0 dépendance externe)
```

### Stack technique
| Composant | Technologie |
|-----------|------------|
| Framework UI | React 18 (JSX) |
| Rendu | Babel Standalone (v8) / JSX pré-compilé (standalone) |
| Export Excel | SheetJS (xlsx) |
| Lecture PDF | PDF.js 3.11.174 |
| IA / Analyse | Mistral AI API (`mistral-large-latest`) |
| Stockage | localStorage (clé API, historique sessions) |
| Déploiement | Fichier HTML statique — aucun serveur requis |

### Règles métier automatiques

```
Période CEE :
  Date début < 01/01/2026  →  P5
  Date début ≥ 01/01/2026  →  P6

Obligé :
  Attribution automatique selon période et structure
  (règles configurables selon l'organisation)

Date limite dépôt CEE :
  Date fin de travaux + 364 jours
```

---

## 🚀 Installation & Utilisation

### Prérequis
- Navigateur web moderne (Chrome, Edge, Firefox)
- Clé API Mistral AI (gratuite sur [console.mistral.ai](https://console.mistral.ai))

### Démarrage rapide

```bash
# 1. Cloner le repository
git clone https://github.com/BANTServer/cee-analyzer.git

# 2. Ouvrir le fichier HTML dans votre navigateur
# Version avec CDN (accès internet) :
open CEE_Analyzer_v8.html

# Version standalone (réseau d'entreprise / intranet) :
open CEE_Analyzer_v8_standalone.html
```

> ⚠️ Si le réseau d'entreprise bloque les CDNs externes (jsdelivr.net, cdnjs), utiliser la version **standalone** — elle embarque toutes les dépendances.

### Configuration
À la première ouverture, renseigner :
- **Clé API Mistral** (stockée localement dans le navigateur)
- **Modèle** : `mistral-large-latest` (recommandé) ou `pixtral-large-latest` (PDFs scannés)

---

## 📊 Workflow type

```
1. Onglet Analyse
   └── Glisser-déposer DPGF / Devis / Fiche marché
   └── Cliquer "Analyser"
   └── Visualiser les fiches détectées avec preuves

2. Onglet Saisie & Export
   └── Compléter / corriger les données extraites
   └── Calculer le cumac et le montant prévisionnel
   └── Consulter les mentions légales par fiche
   └── Exporter en Excel (36 colonnes)

3. Onglet Historique
   └── Retrouver une analyse passée par identifiant / fiche / commune
   └── Restaurer et reprendre une session
```

---

## 📁 Structure du projet

```
cee-analyzer/
├── CEE_Analyzer_v8.html            # Application principale
├── CEE_Analyzer_v8_standalone.html # Version autonome (intranet)
├── fiches_CEE_officiel.xlsx        # Base de données 116 fiches source
├── README.md                       # Ce fichier
└── docs/
    └── screenshots/                # Captures d'écran
```

---

## 🗂️ Référentiel fiches

| Secteur | Nb fiches | Exemples |
|---------|-----------|----------|
| **BAR-EN** | 10 | Isolation combles, murs, fenêtres |
| **BAR-TH** | 30+ | PAC, VMC, chaudière biomasse, robinets thermostatiques |
| **BAR-EQ** | 5+ | LED parties communes, affichage consommations |
| **BAR-SE** | 6 | Équilibrage, désembouage, CPE |
| **BAT-** | 54 | Équivalents tertiaire |
| **RES-CH** | 6 | Réseaux de chaleur |

---

## ⚙️ Variables d'environnement

L'outil ne nécessite aucun fichier `.env`. La clé API est saisie dans l'interface et stockée dans le `localStorage` du navigateur.

```
localStorage:
  cee_mk    → Clé API Mistral
  cee_mm    → Modèle sélectionné
  cee_hist  → Historique des sessions (JSON, max 30 entrées)
```

---

## 🔒 Sécurité & Confidentialité

- **Aucune donnée stockée côté serveur** — tout tourne dans le navigateur
- La clé API est stockée localement, jamais transmise sauf vers `api.mistral.ai`
- Les documents analysés transitent vers les serveurs Mistral AI pour traitement
- **Recommandation** : ne pas analyser de documents contenant des données personnelles sensibles sans validation DPO
- Les données métier et références internes de l'organisation d'origine ont été retirées de cette version publique

---

## 🛣️ Roadmap

- [ ] Intégration Claude API (Anthropic) en option pour plus de précision
- [ ] Calcul automatique du cumac basé sur les formules PNCEE
- [ ] Connexion au tableau de suivi via Google Sheets API
- [ ] Intégration des nouvelles fiches P6 (BAT-TH-162/163, IND-UT-137)
- [ ] Mise à jour automatique du référentiel depuis ecologie.gouv.fr

---

## 👤 Auteur

**Jessy KOUOLA**
Chargé de Financement CEE · Alternant MBA Finance & Data Performance (ESLSCA)

📩 Jessybants@gmail.com
🔗 [linkedin.com/in/jessykouola](https://www.linkedin.com/in/jessykouola)

---

## 📜 Licence

Code démonstratif publié à des fins de portfolio.
Les données métier, règles contractuelles et références internes de l'organisation d'origine ont été anonymisées.

---

## 🙏 Remerciements

- [PNCEE / Ministère de la Transition Écologique](https://www.ecologie.gouv.fr) — référentiel officiel des fiches CEE
- [Mistral AI](https://mistral.ai) — moteur d'analyse IA

---

*CEE Analyzer v8.0 — Construit avec ❤️ et beaucoup de fiches BAR*
