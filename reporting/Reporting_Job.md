# 📊 Reporting_Job.pbix – Documentation Complète du Projet BI

 
 **Objectif** : Transformer des données brutes d'offres d'emploi en un dashboard décisionnel interactif  
 **Tables** : `Clean`, `Calendrier`, `Mesure`  
 **Date** : 15 Mai 2026

---

## 📋 Table des matières

1. [Synthèse du projet](#-synthèse-du-projet)
2. [Structure du rapport](#-structure-du-rapport)
3. [Pipeline de données](#-pipeline-de-données)
4. [Mesures DAX développées](#-mesures-dax-développées)
5. [Transformations Power Query](#-transformations-power-query)
6. [Améliorations visuelles](#-améliorations-visuelles)
7. [Configuration technique](#-configuration-technique)
8. [Récapitulatif des compétences](#-récapitulatif-des-compétences)

---

## 🎯 Synthèse du projet

### Contexte et objectifs

Ce projet consiste à analyser un dataset de plus de **10 000 offres d'emploi** dans le domaine de la data. Les données brutes issues d'Excel ont été nettoyées, transformées et modélisées dans Power BI pour créer un tableau de bord complet permettant :

- L'analyse des tendances du marché de l'emploi data
- La comparaison des salaires par rôle, pays et compétence
- L'évaluation de l'attractivité des différents postes
- L'identification des compétences les plus recherchées

### Chiffres clés du projet

```
📊 Offres analysées : 10 000+
🌍 Pays couverts : 15+
🛠️ Compétences distinctes : 150+
📏 Mesures DAX créées : 16
📄 Pages dashboard : 3
🎛️ Slicers interactifs : 5
🏷️ Catégories de skills : 8
```

---

## 🗂️ Structure du rapport

Le dashboard est organisé en **3 pages interconnectées** :

### Page 1 – Vue d'ensemble
| Élément | Contenu |
|---------|---------|
| **KPI Cards** | Total offres, Salaire moyen, Salaire médian, Taux remote, Taux santé, Nb pays distincts |
| **Gauge** | Taux d'assurance santé avec cible dynamique |
| **Bar Chart** | Top 10 pays par nombre d'offres |
| **Line Chart** | Évolution mensuelle des offres + tendance YTD |
| **Slicers** | Période, Pays, Rôle, Compétence, Catégorie skill |

### Page 2 – Analyse Salariale
| Élément | Contenu |
|---------|---------|
| **Bar Chart** | Salaire moyen par rôle avec Score d'Attractivité |
| **Treemap** | Visualisation du Score d'Attractivité |
| **Scatter Chart** | Salaire vs Offres vs Attractivité |
| **Tableau croisé** | Rôle × Salaire moyen, Rang, % Part, Fourchette |
| **Slicers** | Pays, Période |

### Page 3 – Compétences
| Élément | Contenu |
|---------|---------|
| **Bar Chart horizontal** | Top 10 compétences par nombre d'offres |
| **Donut Chart** | Répartition des offres par catégorie de compétence |
| **Table** | Compétence × Salaire moyen × Nb offres |
| **Card** | Compétence la plus demandée (texte dynamique) |
| **Slicers** | Rôle, Catégorie de compétence |

---

## 📂 Pipeline de données

### 1. Excel – Préparation initiale
- Fusion de plusieurs sources de données
- Nettoyage des valeurs nulles et des formats
- Normalisation des colonnes (salaires, dates, pays)
- Structuration des tables pour l'import

### 2. Power Query – Transformation
- Correction des types de données
- Expansion des colonnes listes (skills)
- Création de colonnes calculées
- Nettoyage des espaces et caractères spéciaux

### 3. Modèle DAX – Calculs avancés
- Création de la table Calendrier
- Établissement des relations
- Développement de 16 mesures analytiques

### 4. Visualisation – Dashboard
- Création des 3 pages interactives
- Mise en place des filtres croisés
- Formatage conditionnel et tooltips

---

## 🧮 Mesures DAX développées

### Mesures de base (héritées du fichier initial)

```dax
// Indicateurs fondamentaux
Total offres = COUNTROWS(Clean)

Avg Salary Annual = AVERAGE(Clean[Salaire_annuel_normaliser])

Taux remote = 
DIVIDE(
    CALCULATE([Total offres], Clean[remote] = TRUE()),
    [Total offres]
)

Health Insurance Rate = 
DIVIDE(
    CALCULATE([Total offres], Clean[health_insurance] = TRUE()),
    [Total offres]
)
```

### Mesures salariales avancées

```dax
// Médiane – plus robuste que la moyenne face aux valeurs extrêmes
Salaire Médian = MEDIAN(Clean[Salaire_annuel_normaliser])

// Étendue des salaires
Salaire Max = MAX(Clean[Salaire_annuel_normaliser])
Salaire Min = MIN(Clean[Salaire_annuel_normaliser])

// Amplitude – révèle la dispersion par rôle
Fourchette Salariale = [Salaire Max] - [Salaire Min]
```

### Mesures temporelles (Time Intelligence)

```dax
// Variation Mensuelle (MoM) – dynamique du marché
Variation MoM Offres =
VAR MoisActuel = [Total offres]
VAR MoisPrecedent =
    CALCULATE(
        [Total offres],
        DATEADD(Calendrier[Date], -1, MONTH)
    )
RETURN
    IF(
        MoisPrecedent = 0 || ISBLANK(MoisPrecedent),
        BLANK(),
        DIVIDE(MoisActuel - MoisPrecedent, MoisPrecedent)
    )

// Croissance annuelle cumulée (YTD)
Total Offres YTD =
TOTALYTD(
    [Total offres],
    Calendrier[Date]
)
```

### Mesures analytiques composites

```dax
// Score d'Attractivité – combine salaire, avantages et flexibilité
// Interprétation : 100 = moyenne du marché
Score Attractivité =
DIVIDE(
    [Avg Salary Annual] * [Health Insurance Rate] * (1 + [Taux remote]),
    CALCULATE(
        AVERAGE(Clean[Salaire_annuel_normaliser]),
        ALL(Clean[role])
    )
) * 100

// Classement des rôles – avec ALLSELECTED pour respecter les filtres
Rang Salaire Rôle =
IF(
    HASONEVALUE(Clean[role]),
    RANKX(
        ALLSELECTED(Clean[role]),
        [Avg Salary Annual],
        ,
        DESC,
        Dense
    ),
    BLANK()
)

// Part de marché des rôles
% Part des Offres =
DIVIDE(
    [Total offres],
    CALCULATE([Total offres], ALL(Clean[role]))
)
```

### Mesures géographiques et textuelles

```dax
// Couverture géographique
Nb Pays Distincts = DISTINCTCOUNT(Clean[Pays])

// Pays leader (texte dynamique pour Card)
Nom Top Pays =
CONCATENATEX(
    TOPN(1, VALUES(Clean[Pays]), [Total offres], DESC),
    Clean[Pays],
    ", "
)

// Top compétences
Top 10 Compétences =
TOPN(
    10,
    VALUES(Clean[job_skills]),
    [Total offres],
    DESC
)

// Salaire par compétence
Salaire par Compétence =
CALCULATE(
    [Avg Salary Annual],
    KEEPFILTERS(Clean[job_skills])
)
```

---

## 🔄 Transformations Power Query

### Problème identifié
La colonne `job_skills` contenait des listes au format texte : `"['python','sql','aws']"` – impossible à filtrer ou analyser directement.

### Solution appliquée

```m
let
    // 1. Nettoyage du texte (suppression crochets, guillemets et espaces)
    #"Nettoyage skills" = Table.TransformColumns(#"Colonnes renommées", {{"job_skills", 
        each 
            if _ = null or _ = "" or _ = "[]" then null
            else Text.Split(
                Text.Trim(Text.Replace(Text.Replace(Text.Replace(_, "[", ""), "]", ""), "'", "")),
                ","
            )
        , type list}}),
    
    // 2. Expansion de la liste – une ligne par skill individuel
    #"Skills expandés" = Table.ExpandListColumn(#"Nettoyage skills", "job_skills"),
    
    // 3. Nettoyage final des espaces résiduels
    #"Skills nettoyés" = Table.TransformColumns(#"Skills expandés", {{"job_skills", Text.Trim, type text}}),
    
    // 4. Filtrage des valeurs vides ou nulles
    #"Filtre skills vides" = Table.SelectRows(#"Skills nettoyés", each [job_skills] <> null and [job_skills] <> "")
in
    #"Filtre skills vides"
```

### Résultat de la transformation

| Étape | Exemple |
|-------|---------|
| **Avant** | `"['python','sql','aws']"` (texte, 1 ligne) |
| **Après nettoyage** | `"python,sql,aws"` (texte sans crochets) |
| **Après expansion** | 3 lignes distinctes : python / sql / aws |

### Création de la colonne catégorie_skill

Cette colonne permet de regrouper les 150+ compétences en 8 catégories pour des analyses macro :

```dax
categorie_skill = 
SWITCH(
    TRUE(),
    Clean[job_skills] IN {"python","r","java","scala","javascript","go","c++","c#","rust","ruby","swift","kotlin","php","typescript","bash","shell"}, "Programmation",
    Clean[job_skills] IN {"sql","postgresql","mysql","mongodb","cassandra","redis","elasticsearch","pandas","numpy","spark","hadoop","kafka"}, "Data",
    Clean[job_skills] IN {"aws","azure","gcp","docker","kubernetes","terraform","jenkins","git","github","ansible","linux","unix"}, "Cloud & DevOps",
    Clean[job_skills] IN {"tensorflow","pytorch","scikit-learn","keras","opencv","mlflow","jupyter"}, "Machine Learning",
    Clean[job_skills] IN {"tableau","power bi","looker","qlik","matplotlib","seaborn","plotly"}, "Data Visualisation",
    Clean[job_skills] IN {"react","angular","vue","html","css","node","express","django","flask"}, "Web Development",
    Clean[job_skills] IN {"jira","confluence","trello","asana","notion","slack","excel"}, "Gestion de Projet",
    "Autres"
)
```

### Table Calendrier

```dax
Calendrier = 
CALENDAR(
    MIN(Clean[Date]),
    MAX(Clean[Date])
)

// Colonnes supplémentaires ajoutées
Année = YEAR(Calendrier[Date])
Mois = MONTH(Calendrier[Date])
Nom Mois = FORMAT(Calendrier[Date], "mmmm")
Trimestre = QUARTER(Calendrier[Date])
```

### Relations établies

```
Clean[Date] (Many) ←→ Calendrier[Date] (One)
```

---

## 🎨 Améliorations visuelles

### Vue d'ensemble

| Composant | Configuration |
|-----------|---------------|
| **Cards KPI** | Total offres, Salaire moyen, Salaire médian, Taux remote, Taux santé, Nb pays |
| **Gauge** | Health Insurance Rate avec cible = moyenne secteur |
| **Bar Chart** | Top pays par Total offres (tri décroissant) |
| **Line Chart** | Double axe : Total offres (principal) + Total Offres YTD (secondaire) |
| **Slicers** | Période, Pays, Rôle, Compétence, Catégorie |

**Formatage conditionnel** : Variation MoM → Vert si >0, Rouge si <0

### Analyse Salariale

| Composant | Configuration |
|-----------|---------------|
| **Bar Chart cluster** | Salaire moyen + Score Attractivité par rôle |
| **Treemap** | Taille = Score Attractivité, Couleur = Total offres |
| **Scatter Chart** | X = Salaire moyen, Y = Total offres, Taille = Score Attractivité |
| **Tableau croisé** | Rôle / Salaire moyen / Rang / % Part / Fourchette |

**Formatage conditionnel** : Rang 1 = vert foncé → Rang N = rouge clair

### Compétences

| Composant | Configuration |
|-----------|---------------|
| **Bar Chart horizontal** | Top 10 compétences par Total offres |
| **Donut Chart** | Répartition par categorie_skill |
| **Table** | Compétence / Salaire moyen / Nb offres |
| **Card dynamique** | "Compétence #1 : Python (X offres)" |

---

## 🛠️ Configuration technique

### Création des mesures (pas à pas)

1. Ouvrir Power BI Desktop
2. Accéder à l'onglet **Modélisation**
3. Cliquer sur **Nouvelle mesure**
4. Sélectionner la table `Mesure` comme emplacement
5. Coller le code DAX et valider

### Mise en place du formatage conditionnel

**Pour la Card Variation MoM :**
1. Sélectionner la Card
2. Aller dans **Format** → **Règles de couleur**
3. Configurer : Si valeur > 0 → Vert | Si valeur < 0 → Rouge

**Pour le Rang dans le tableau :**
1. Sélectionner la colonne Rang
2. **Mise en forme conditionnelle** → **Couleur d'arrière-plan**
3. Échelle : Rang 1 (vert) → Rang N (rouge)

### Configuration du Line Chart double axe

1. Cliquer sur le Line Chart
2. Glisser `Total Offres YTD` dans **Valeurs Y secondaire**
3. Activer l'affichage dans les options de formatage

---

## 🏆 Récapitulatif des compétences

### Compétences démontrées par le projet

| Compétence | Mise en œuvre spécifique |
|------------|---------------------------|
| **DAX avancé** | CALCULATE, RANKX, TOTALYTD, ALLSELECTED, SWITCH, DATEADD |
| **Time Intelligence** | MoM, YTD, comparaisons temporelles |
| **Power Query M** | Text.Split, ExpandListColumn, nettoyage données complexes |
| **Modélisation** | Table Calendrier, relations, hiérarchies |
| **Visualisation** | Formatage conditionnel, tooltips, double axe, scatter plot |
| **Data storytelling** | Score composite, KPI, tableau de bord structuré |

### Liste complète des 16 mesures

| # | Mesure | Type |
|---|--------|------|
| 1 | Total offres | Base |
| 2 | Avg Salary Annual | Base |
| 3 | Taux remote | Base |
| 4 | Health Insurance Rate | Base |
| 5 | Salaire Médian | Salariale |
| 6 | Salaire Max | Salariale |
| 7 | Salaire Min | Salariale |
| 8 | Fourchette Salariale | Salariale |
| 9 | Variation MoM Offres | Temporelle |
| 10 | Total Offres YTD | Temporelle |
| 11 | Score Attractivité | Composite |
| 12 | Rang Salaire Rôle | Classement |
| 13 | % Part des Offres | Part |
| 14 | Nb Pays Distincts | Géographique |
| 15 | Nom Top Pays | Texte |
| 16 | Top 10 Compétences | Classement |

---

## 📎 Annexes

### Structure des tables

**Table Clean**
- role (texte) – titre du poste
- Pays (texte) – pays de l'offre
- Salaire_annuel_normaliser (nombre) – salaire en €
- remote (booléen) – télétravail possible
- health_insurance (booléen) – assurance santé fournie
- job_skills (texte) – compétences requises
- categorie_skill (texte) – catégorie de compétence
- Date (date) – date de publication

**Table Calendrier**
- Date (date) – clé primaire
- Année (nombre)
- Mois (nombre)
- Nom Mois (texte)
- Trimestre (nombre)

**Table Mesure**
- Stockage de toutes les mesures DAX

### Fichiers du projet

- `Reporting_Job.pbix` – Dashboard Power BI
- `data_jobs_cleaned.xlsx` – Données sources nettoyées
- `README.md` – Documentation complète

---

*Document généré le 15 mai 2026*  
*Projet : Reporting_Job.pbix – BI Financial & Job Analytics*  
*Auteur : Candidat BIOS Expertise*  
*Version : 1.0 – Documentation complète*
```

Ce fichier README.md unique contient désormais l'intégralité du projet expliquée de manière cohérente et fluide, sans distinction "avant/après", mais comme un tout homogène présentant l'ensemble des réalisations.