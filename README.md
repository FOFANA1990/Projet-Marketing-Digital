# 📊 Étude de Cas — Analyse Marketing Digital SmartMarket

**Auteur :** Fofana Boubacar  
**Formation :** Mastère 2 Data & IA  
**Contexte :** Analyse complète des performances de campagnes marketing digitales pour l'entreprise fictive SmartMarket.

---

## 📋 Description du projet

Ce projet constitue une étude de cas appliquée en marketing digital. Il couvre l'ensemble du pipeline analytique, depuis l'ingestion et le nettoyage des données jusqu'à la production d'un tableau de bord interactif destiné aux décideurs métier.

L'objectif est de répondre à une problématique business : **quels canaux, devices et segments génèrent les leads les plus qualifiés et les plus rentables pour SmartMarket ?**

---

## 🗂️ Sources de données

Le projet exploite trois fichiers de données complémentaires :

| Fichier | Format | Contenu |
|---|---|---|
| `leads_smartmarket2.csv` | CSV | Données de leads (canal, device, secteur, région, statut CRM…) |
| `crm_smartmarket.xlsx` | Excel | Informations CRM sur les leads (taille d'entreprise, statut qualifié…) |
| `campaign_smartmarket.json` | JSON | Données de campagnes publicitaires (coût, impressions, clics, conversions) |

Ces trois sources sont fusionnées (merge) sur les clés `lead_id` et `channel` pour constituer un dataset unifié : `donnees_nettoyes.csv`.

---

## 🏗️ Structure du notebook

### 1. Prétraitement et Nettoyage des données
- Chargement des trois sources (CSV, Excel, JSON)
- Inspection des données : `.info()`, `.describe()`, détection des types
- Conversion de la colonne `date` en type `datetime`
- Détection et suppression des doublons dans chaque source
- Vérification des valeurs manquantes colonne par colonne
- Fusion (merge) progressif des DataFrames :
  - `leads` + `crm` → `leads_crm`
  - `leads_crm` + `campaign` → `merger_leads_crm_campaign`
- Export du dataset nettoyé : `donnees_nettoyes.csv`

### 2. Analyses Univariées et Bivariées
- **Univariées** : statistiques descriptives (moyenne, médiane, écart-type, quartiles), fréquences et proportions pour les variables qualitatives (`channel`, `device`, `company_size`, `sector`, `region`, `status`)
- **Bivariées** : tableaux croisés (pivot tables) et nuages de points sur des croisements métier clés :
  - Canal × Device
  - Canal × Statut CRM
  - Coût × Conversions
  - Impressions × Clics
  - Taille d'entreprise × Statut CRM
- **Matrice de corrélation** : analyse des corrélations entre variables numériques (via Seaborn heatmap)

### 3. Visualisations pertinentes (3–6 graphiques)
Chaque graphique répond à une question métier explicite :
- **Bubble chart** — Coût vs Conversions (taille = impressions, couleur = statut, forme = canal) → *Quelle campagne maximise les conversions pour le budget investi ?*
- **Heatmap** — Clients par taille d'entreprise × canal → *Quels segments convertissent le mieux ?*
- **Scatter plot** — Impressions × Clics colorés par statut et canal → *Comment la visibilité influence-t-elle l'engagement ?*
- **Bar chart** — Clients par canal et device → *Quels canaux et devices génèrent les leads les plus qualifiés ?*

### 4. Tableau de bord interactif (Streamlit)
- Génération de 200 lignes synthétiques aléatoires pour enrichir le dataset (`df_extended.csv`)
- Calcul des **4 KPI clés** :
  - **CTR** (Click-Through Rate) = clics / impressions
  - **CVR** (Conversion Rate) = conversions / clics
  - **CPC** (Coût Par Clic) = coût / clics
  - **CPA** (Coût Par Acquisition) = coût / conversions
- Dashboard Streamlit (`app.py`) avec :
  - Filtres dynamiques sidebar (Canal, Device, Statut)
  - 4 cards de KPI moyens
  - Graphiques Plotly : CTR/CVR par canal, CPC/CPA par canal, entonnoir marketing, KPI par device, heatmap canal × device
- Exposition via **ngrok** pour accès externe depuis Google Colab

---

## 🔑 Principaux enseignements métier

- **Instagram Ads** affiche le meilleur ratio conversions/coût (210 conversions pour 1 800 €)
- **Le mobile** est le device dominant sur l'ensemble des canaux — stratégie mobile-first recommandée
- **Emailing** génère le plus grand volume de leads, mais principalement des MQL nécessitant un nurturing
- **LinkedIn** présente un coût élevé (2 500 €) pour un faible rendement en conversions
- La corrélation clics–conversions est quasi parfaite (0,999), confirmant que l'engagement est le meilleur prédicteur de conversion
- Augmenter le budget accroît surtout la portée, pas directement les conversions → optimiser le ciblage plutôt que les dépenses brutes

---

## 🚀 Lancement du Dashboard

Le tableau de bord Streamlit est conçu pour tourner dans **Google Colab** avec ngrok :

```bash
# 1. Installer les dépendances
pip install streamlit pyngrok plotly

# 2. Configurer ngrok avec votre token
from pyngrok import ngrok
ngrok.set_auth_token("VOTRE_TOKEN_NGROK")

# 3. Lancer l'application
!streamlit run app.py &>/dev/null&
public_url = ngrok.connect(8501)
print(public_url)
```

Pour une exécution locale (hors Colab) :
```bash
streamlit run app.py
# Ouvrir http://localhost:8501 dans le navigateur
```

---

## 📁 Fichiers du projet

```
├── Etude_de_cas_marketing_digital.ipynb   # Notebook principal
├── app.py                                  # Application Streamlit (dashboard)
├── leads_smartmarket2.csv                  # Données leads (source 1)
├── crm_smartmarket.xlsx                    # Données CRM (source 2)
├── campaign_smartmarket.json               # Données campagnes (source 3)
├── donnees_nettoyes.csv                    # Dataset fusionné et nettoyé
├── df_extended.csv                         # Dataset enrichi (+200 lignes synthétiques)
├── README.md                               # Ce fichier
└── requirements.txt                        # Dépendances Python
```

---

## ⚠️ Notes importantes

- Le notebook est optimisé pour **Google Colab** — les chemins de fichiers commencent par `/content/`
- Pour une exécution locale, adapter les chemins d'accès aux fichiers CSV/Excel/JSON
- Le token ngrok présent dans le notebook est personnel ; le remplacer par votre propre token avant usage
- Les 200 lignes générées aléatoirement en section 4 sont synthétiques et servent uniquement à enrichir le dashboard démonstratif
