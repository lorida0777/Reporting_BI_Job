# 📊 BI Financial & Job Analytics – Power BI Project

## 🧾 Contexte du projet

Dans le cadre d'une préparation à un poste de **Stagiaire Business Intelligence** au sein de **BIOS Expertise**, ce projet consiste à transformer et analyser des données issues d'Excel pour produire un **dashboard interactif sous Power BI**.

Les données ont été combinées, nettoyées et structurées dans Excel, puis exploitées pour créer des indicateurs pertinents pour la **prise de décision stratégique**.

---

## 🎯 Objectif professionnel

Ce projet démontre ma capacité à :

| Compétence | Mise en œuvre |
|------------|----------------|
| 📌 Modéliser et structurer les données | Création d'un modèle en étoile avec table calendrier |
| 📊 Créer des tableaux de bord décisionnels | Dashboard interactif avec 3 pages analytiques |
| 🧠 Développer des indicateurs pertinents (KPI) | 8 KPI dynamiques avec mesures DAX |
| 🔄 Transformer des données brutes en insights | Pipeline ETL complet (Excel → Power BI) |
| 📈 Analyser les tendances salariales | MoM, YTD, scores d'attractivité |

### 👉 En lien direct avec les missions du poste BI :
- ✅ Traitement de données
- ✅ Analyse financière
- ✅ Aide à la décision stratégique

---

## 📂 Pipeline de données

### 1. Excel (Préparation des données)
- 🔄 Fusion de plusieurs sources de données
- 🧹 Nettoyage (valeurs nulles, formats)
- 📏 Normalisation des colonnes (salaires, dates, pays)
- 🗂️ Structuration des tables

### 2. Power BI (Modélisation)
- 🔗 Création de relations entre tables
- 📅 Table calendrier (Dates, Mois, Années)
- 📐 Table de mesures (DAX avancé)
- 🏷️ Création de colonnes calculées (catégories skills)

### 3. Visualisation
- 🎛️ Dashboard interactif
- 📊 KPI dynamiques
- 📈 Graphiques analytiques (MoM, Top skills, Matrice salariale)

---

## 🧠 Compétences démontrées

### 📊 Business Intelligence
| Compétence | Réalisation |
|------------|--------------|
| Création de dashboards Power BI | 3 pages interconnectées |
| Data storytelling | KPI cards + tendances |
| Analyse décisionnelle | Score d'attractivité |

### 🧮 Excel avancé
| Compétence | Réalisation |
|------------|--------------|
| Nettoyage et transformation | Power Query (M) |
| Structuration de datasets | Colonnes normalisées |
| Préparation pour BI | Export optimisé |

### 🔢 DAX (Power BI)
| Concept | Mesures créées |
|---------|----------------|
| Mesures avancées | Salaire moyen/médian, Variation MoM, YTD, Score attractivité |
| Fonctions utilisées | CALCULATE, RANKX, TOTALYTD, ALLSELECTED, SWITCH, TOPN |
| Time Intelligence | PREVIOUSMONTH, DATEADD, SAMEPERIODLASTYEAR |

---

## 📊 Indicateurs clés (KPI)

| KPI | Description |
|-----|-------------|
| 📈 Total des offres d'emploi | Volume global d'offres |
| 💰 Salaire moyen et médian | Analyse salariale |
| 🏠 Taux de télétravail | % d'offres avec remote |
| 🏥 Taux d'assurance santé | % d'offres avec couverture |
| 📅 Évolution mensuelle des offres | MoM et YTD |
| ⭐ Score d'attractivité des rôles | Indice composite |
| 🔝 Top compétences | Classement des skills |
| 🌍 Analyse par pays | Répartition géographique |


---

## 📈 Valeur ajoutée du projet

Ce projet ne se limite pas à la visualisation :

✔️ **Combiné plusieurs variables métiers** : salaire, remote, avantages, géographie  
✔️ **Introduit un score analytique avancé** : Score d'Attractivité = f(salaire × santé × remote)  
✔️ **Permet une lecture stratégique rapide** : Top rôles, Top skills, Top pays  
✔️ **Skills catégorisés** : Programmation, Data, Cloud, ML, etc.  

### Exemple : Score d'Attractivité
Score = (Salaire moyen × Taux santé × (1 + Taux remote)) / Salaire de référence × 100

- Base 100 = moyenne du marché
- Permet de comparer objectivement les rôles

---

## 🧩 Alignement avec le poste (BIOS Expertise)

| Exigences du poste | Réponse du projet | Niveau |
|--------------------|-------------------|--------|
| Traitement de données | Nettoyage + transformation Excel + Power Query | ✅ Avancé |
| Business Intelligence | Dashboard Power BI complet | ✅ Expert |
| Analyse financière | KPI salariaux + scores | ✅ Avancé |
| Outils d'analyse | DAX + visualisations interactives | ✅ Avancé |
| Excel avancé | Préparation complète + Power Query | ✅ Avancé |
| Esprit analytique | Score personnalisé + ranking + MoM | ✅ Expert |

---

## 🗂️ Structure du Dashboard

### Page 1 : Vue d'ensemble
- KPI Cards (Total offres, Salaire moyen, Remote, Santé)
- Évolution MoM (graphique linéaire)
- Top 10 rôles (bar chart)
- Top 10 pays (carte + bar chart)

### Page 2 : Analyse salariale
- Salaire par rôle (matrice)
- Salaire par pays (bar chart)
- Salaire par compétence (matrice)
- Score d'attractivité (treemap)

### Page 3 : Compétences
- Top 10 compétences (bar chart)
- Répartition par catégorie (donut)
- Matrice Compétences × Rôle
- Analyse temporelle des compétences

---

## 📊 Filtres disponibles

- 📅 **Période** : Mois / Trimestre / Année
- 🌍 **Pays** : Sélection multiple
- 💼 **Rôle** : Data Eng, ML Eng, Data Scientist, Data Analyst
- 🏷️ **Compétence** : Par skill ou catégorie
- 🏠 **Remote** : Oui/Non

---

## ▶️ Utilisation

### Prérequis
- Power BI Desktop (version gratuite)
- Windows 10/11

### Étapes
1. **Ouvrir** le fichier `BI_Job_Analytics.pbix` avec Power BI
2. **Explorer** les 3 pages :
   - 📊 Vue d'ensemble
   - 💰 Analyse salariale
   - 🛠️ Compétences
3. **Utiliser les slicers** pour filtrer par période, pays, rôle
4. **Interagir** avec les graphiques (cross-filtering)

---

## 🚀 Améliorations futures

| Priorité | Amélioration | Impact |
|----------|--------------|--------|
| Haute | Intégration de données financières réelles | ROI précis |
| Haute | Connexion à une base de données SQL | Automatisation |
| Moyenne | Ajout de prévisions (ML) | Tendances futures |
| Moyenne | Automatisation du pipeline (ETL) | Mise à jour auto |
| Basse | Export PDF automatisé | Reporting |

---

## 🎤 Pitch rapide (à dire en entretien)

> *"J’ai d’abord combiné et nettoyé les données sous Excel, puis j’ai construit un modèle Power BI avec des KPI avancés comme la variation MoM et le score d’attractivité. J’ai aussi catégorisé plus de 150 compétences techniques pour analyser les tendances du marché. Ce projet montre ma capacité à transformer des données brutes en un outil d’aide à la décision stratégique, avec des visuels clairs et interactifs."*

---

## 📈 Chiffres clés du projet

| Métrique | Valeur |
|----------|--------|
| Lignes traitées | +10 000 |
| Colonnes transformées | 18 |
| Mesures DAX créées | 25+ |
| Skills distincts | 150+ |
| Catégories de skills | 8 |
| Pages dashboard | 3 |
| KPI | 8 |

---

## 👤 Auteur

**Nom** : ANDRIATSIFERANA No Kanto Lorida  
**Objectif** : Stage Business Intelligence – BIOS Expertise  
**Contact** : kantonotsiferana@gmail.com  
 

---

## 📎 Annexes

### Fichiers inclus
- `BI_Job_Analytics.pbix` - Dashboard Power BI
- `data_jobs_cleaned.xlsx` - Données nettoyées
- `README.md` - Documentation

### Outils utilisés
- Power BI Desktop
- Microsoft Excel
- DAX (Data Analysis Expressions)
- Power Query (M Language)

---

*Projet réalisé dans le cadre d'une candidature pour un stage Business Intelligence chez BIOS Expertise.*