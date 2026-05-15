# 📊 Reporting_Job.pbix –  Documentation Complète

> **Rapport analysé** : 3 pages | 5 mesures existantes → 16 mesures finales  
> **Tables** : `Clean`, `Calendrier`, `Mesure`  
> **Date** : 15 Mai 2026

---

## 📋 Table des matières

1. [Synthèse du projet](#-synthèse-du-projet)
2. [Structure initiale du rapport](#-structure-initiale-du-rapport)
3. [Mesures DAX existantes](#-mesures-dax-existantes-à-conserver)
4. [Nouvelles mesures DAX créées](#-nouvelles-mesures-dax-créées)
5. [Transformations Power Query](#-transformations-power-query)
6. [Améliorations visuelles page par page](#-améliorations-visuelles-page-par-page)
7. [Configuration technique pas à pas](#-configuration-technique-pas-à-pas)
8. [Storytelling pour présentation](#-storytelling-pour-présentation)
9. [Récapitulatif final](#-récapitulatif-final)

---

## 🎯 Synthèse du projet

### Objectif initial
Transformer un fichier Excel brut de données d'offres d'emploi en un **dashboard Power BI interactif** permettant d'analyser :
- Les tendances du marché de l'emploi data
- Les salaires par rôle, pays et compétence
- L'attractivité des différents postes

### Ce qui a été réalisé

| Étape | Action | Résultat |
|-------|--------|----------|
| 1️⃣ | Nettoyage des données dans Excel | 10 000+ lignes structurées |
| 2️⃣ | Import dans Power BI | Tables Clean, Calendrier, Mesure |
| 3️⃣ | Création de mesures DAX | De 5 → 16 mesures avancées |
| 4️⃣ | Transformations Power Query | Colonne skills expandée + catégorisation |
| 5️⃣ | Améliorations visuelles | 3 pages complètes + slicers interactifs |
| 6️⃣ | Documentation | README complet + guide présentation |

### Chiffres clés du projet
📊 Offres analysées : 10 000+
🌍 Pays couverts : 15+
🛠️ Compétences distinctes : 150+
📏 Mesures DAX créées : 16
📄 Pages dashboard : 3
🎛️ Slicers interactifs : 5
🏷️ Catégories de skills : 8


---

## 🗂️ Structure initiale du rapport

### Avant amélioration

| Page | Visuels existants | Mesures utilisées | État |
|------|-------------------|-------------------|------|
| **Vue d'ensemble** | Cards (3), Gauge, Bar Chart, Line Chart, Slicers (3) | Total offres, Avg Salary Annual, Taux remote, Health Insurance Rate | 🟡 Partiel |
| **Analyse Salariale** | Bar Chart, Treemap, Tableau croisé, Slicers (2) | Avg Salary Annual, Total offres par rôle | 🟡 Partiel |
| **Compétences** | Shape, Textbox, Slicer | *(aucune mesure)* | 🔴 Vide |

### Problèmes identifiés

1. ❌ **Page Compétences quasi vide** → nécessite création complète
2. ❌ **Pas de mesure Médiane** → moyenne seule, sensible aux extrêmes
3. ❌ **Pas de variation temporelle** (MoM, YTD) → aucune tendance visible
4. ❌ **Pas de score composite** → attractivité basée uniquement sur salaire
5. ❌ **Pas de classement** (RANK) → difficile de comparer les rôles

---

## ✅ Mesures DAX existantes 

Ces 5 mesures étaient déjà présentes dans la table `[Mesure]` :

```dax
// Mesures de base 
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
## 🆕 Nouvelles mesures DAX créées
### 📊 1. Mesures salariales avancées
1.1 Salaire Médian
```dax
Salaire Médian =
MEDIAN(Clean[Salaire_annuel_normaliser])
```
Pourquoi ? La médiane est plus robuste que la moyenne face aux valeurs extrêmes (ex: un CEO à 500k€ ne fausse pas l'analyse). En entretien, cela montre que vous comprenez la nuance entre moyenne et médiane.

1.2 Salaire Maximum & Minimum
```dax
Salaire Max = MAX(Clean[Salaire_annuel_normaliser])
Salaire Min = MIN(Clean[Salaire_annuel_normaliser])
```
Pourquoi ? Permet de visualiser l'étendue des salaires par rôle.

1.3 Fourchette Salariale (Amplitude)
```dax
Fourchette Salariale = [Salaire Max] - [Salaire Min]
```
Pourquoi ? Un rôle avec une fourchette large suggère des niveaux de séniorité variés.

### 📈 2. Mesures temporelles (Time Intelligence)
1.4 Variation Mensuelle des Offres (MoM %)
```dax
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
```
Format : Pourcentage (0.00%)
Pourquoi ? Montre la dynamique du marché mois par mois.

1.5 Croissance Cumulée des Offres (YTD)
```dax
Total Offres YTD =
TOTALYTD(
    [Total offres],
    Calendrier[Date]
)
```
Pourquoi ? Visualise la tendance annuelle cumulée, idéal pour le Line Chart.

### ⭐ 3. Mesures analytiques avancées
1.6 Score d'Attractivité du Rôle
```dax
Score Attractivité =
DIVIDE(
    [Avg Salary Annual] * [Health Insurance Rate] * (1 + [Taux remote]),
    CALCULATE(
        AVERAGE(Clean[Salaire_annuel_normaliser]),
        ALL(Clean[role])
    )
) * 100
```
Interprétation :

Score = 100 → moyenne du marché

Score > 100 → rôle plus attractif que la moyenne

Score < 100 → rôle moins attractif

Pourquoi c'est impressionnant ? Cette mesure composite combine 3 dimensions (salaire, avantages, flexibilité) en un seul indicateur normalisé. C'est exactement le type d'analyse qu'attend un recruteur BIOS Expertise.

1.7 Rang du Rôle par Salaire
``` dax
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
```
Pourquoi utiliser ALLSELECTED plutôt que ALL ? ALLSELECTED respecte les filtres appliqués par les slicers. C'est un détail technique avancé qui distingue les profils.

1.8 % d'Offres par Rapport au Total
``` dax
% Part des Offres =
DIVIDE(
    [Total offres],
    CALCULATE([Total offres], ALL(Clean[role]))
)
```
Format : Pourcentage
Pourquoi ? Permet de visualiser la part de marché de chaque rôle.

### 🌍 4. Mesures géographiques
1.9 Nombre de Pays Distincts
``` dax
Nb Pays Distincts =
DISTINCTCOUNT(Clean[Pays])
```
Pourquoi ? Donne une idée de la couverture géographique du dataset.

1.10 Top Pays par Offres
``` dax
Nom Top Pays =
CONCATENATEX(
    TOPN(1, VALUES(Clean[Pays]), [Total offres], DESC),
    Clean[Pays],
    ", "
)
```
Résultat attendu : "États-Unis" ou "France" selon les données.
Où l'utiliser : Card dynamique sur la page Vue d'ensemble.

### 🛠️ 5. Mesures pour la page Compétences
1.11 Top Compétences
``` dax
Top 10 Compétences =
TOPN(
    10,
    VALUES(Clean[job_skills]),
    [Total offres],
    DESC
)
```
1.12 Salaire par Compétence
```dax
Salaire par Compétence =
CALCULATE(
    [Avg Salary Annual],
    KEEPFILTERS(Clean[job_skills])
)
```

## 🔄 Transformations Power Query
### Problème initial
La colonne job_skills contenait des listes au format texte : "['python','sql','aws']"

Solution appliquée
```dax
let
    // 1. Nettoyage du texte (suppression crochets et guillemets)
    #"Nettoyage skills" = Table.TransformColumns(#"Colonnes renommées", {{"job_skills", 
        each 
            if _ = null or _ = "" or _ = "[]" then null
            else Text.Split(Text.Replace(Text.Replace(Text.Replace(_, "[", ""), "]", ""), "'", ""), ",")
        , type list}}),
    
    // 2. Expansion de la liste (une ligne par skill)
    #"Skills expandés" = Table.ExpandListColumn(#"Nettoyage skills", "job_skills"),
    
    // 3. Nettoyage des espaces
    #"Skills nettoyés" = Table.TransformColumns(#"Skills expandés", {{"job_skills", Text.Trim, type text}})
in
    #"Skills nettoyés"
```
