# 📊 Guide d'Amélioration – Reporting_Job.pbix
> Préparé pour présentation d'entretien d'embauche  
> Rapport analysé : 3 pages | 5 mesures existantes | Tables : `Clean`, `Calendrier`, `Mesure`

---

## 🗂️ Structure actuelle du rapport

| Page | Visuels | Mesures utilisées |
|------|---------|-------------------|
| Vue d'ensemble | Cards (3), Gauge, Bar Chart, Line Chart, Slicers (3) | Total offres, Avg Salary Annual, Taux remote, Health Insurance Rate |
| Analyse Salariale | Bar Chart, Treemap, Tableau croisé, Slicers (2) | Avg Salary Annual, Total offres par rôle |
| Compétences | Shape, Textbox, Slicer | *(page à enrichir)* |

---

## ✅ Mesures DAX existantes (à conserver)

```dax
-- Déjà dans la table [Mesure]
Total offres         -- COUNT des offres d'emploi
Avg Salary Annual    -- AVERAGE du salaire annuel
Taux remote          -- % d'offres en télétravail
Health Insurance Rate -- % offres avec assurance santé
Valeur Cible         -- Constante utilisée comme max du gauge
```

---

## 🆕 ÉTAPE 1 — Nouvelles mesures DAX à créer

Ouvrir **Power BI Desktop** → onglet **Modélisation** → **Nouvelle mesure** → sélectionner la table `Mesure`.

---

### 1.1 — Salaire Médian

```dax
Salaire Médian =
MEDIAN( Clean[salary_year_avg] )
```

**Pourquoi ?** La médiane est plus robuste que la moyenne face aux valeurs extrêmes. Elle montre que vous maîtrisez la différence entre les deux indicateurs — très apprécié en entretien.

**Où l'ajouter :** Nouvelle Card sur la page *Vue d'ensemble*, à côté de `Avg Salary Annual`.

---

### 1.2 — Salaire Maximum & Salaire Minimum

```dax
Salaire Max =
MAX( Clean[salary_year_avg] )

Salaire Min =
MIN( Clean[salary_year_avg] )
```

**Où les ajouter :** Cards additionnelles sur *Vue d'ensemble*, ou colonnes dans le tableau croisé de *Analyse Salariale*.

---

### 1.3 — Fourchette Salariale (Amplitude)

```dax
Fourchette Salariale =
[Salaire Max] - [Salaire Min]
```

**Pourquoi ?** Permet de visualiser la dispersion des salaires par rôle ou par pays.

**Où l'ajouter :** Colonne supplémentaire dans le tableau croisé de *Analyse Salariale*.

---

### 1.4 — Variation Mensuelle des Offres (MoM %)

```dax
Variation MoM Offres =
VAR MoisActuel = [Total offres]
VAR MoisPrecedent =
    CALCULATE(
        [Total offres],
        DATEADD( Calendrier[Date], -1, MONTH )
    )
RETURN
    IF(
        MoisPrecedent = 0 || ISBLANK(MoisPrecedent),
        BLANK(),
        DIVIDE( MoisActuel - MoisPrecedent, MoisPrecedent )
    )
```

**Format à appliquer :** Pourcentage avec 1 décimale.

**Où l'ajouter :** Card avec indicateur flèche sur *Vue d'ensemble*, ou colonne dans le Line Chart.

---

### 1.5 — Croissance Cumulée des Offres (YTD)

```dax
Total Offres YTD =
TOTALYTD(
    [Total offres],
    Calendrier[Date]
)
```

**Pourquoi ?** Montre la tendance annuelle, utile pour le Line Chart de la page *Vue d'ensemble*.

**Où l'ajouter :** Ajouter comme deuxième ligne (Secondary Y-Axis) dans le Line Chart existant.

---

### 1.6 — Score de Attractivité du Rôle

```dax
Score Attractivité =
DIVIDE(
    [Avg Salary Annual] * [Health Insurance Rate] * (1 + [Taux remote]),
    CALCULATE(
        AVERAGE( Clean[salary_year_avg] ),
        ALL( Clean[role] )
    )
) * 100
```

**Pourquoi ?** Combine salaire, avantages sociaux et flexibilité en un seul score normalisé. **Très impressionnant en présentation** — montre une vision analytique globale.

**Où l'ajouter :** Nouvelle colonne dans le Treemap ou Bar Chart de *Analyse Salariale*.

---

### 1.7 — Rang du Rôle par Salaire

```dax
Rang Salaire Rôle =
IF(
    HASONEVALUE( Clean[role] ),
    RANKX(
        ALLSELECTED( Clean[role] ),
        [Avg Salary Annual],
        ,
        DESC,
        Dense
    ),
    BLANK()
)
```

**Pourquoi ?** Permet d'afficher "#1 Data Engineer", "#2 ML Engineer" etc. Très visuel.

**Où l'ajouter :** Colonne dans le tableau croisé, ou tooltip du Bar Chart de *Analyse Salariale*.

---

### 1.8 — % d'Offres par Rapport au Total (Part de marché)

```dax
% Part des Offres =
DIVIDE(
    [Total offres],
    CALCULATE( [Total offres], ALL( Clean[role] ) )
)
```

**Format :** Pourcentage avec 1 décimale.

**Où l'ajouter :** Label du Treemap, ou colonne dans le tableau croisé.

---

### 1.9 — Nombre de Pays Distincts

```dax
Nb Pays Distincts =
DISTINCTCOUNT( Clean[Pays_corrigé] )
```

**Où l'ajouter :** Card supplémentaire sur *Vue d'ensemble*.

---

### 1.10 — Top Pays par Offres (Texte dynamique)

```dax
Top Pays =
TOPN(
    1,
    VALUES( Clean[Pays_corrigé] ),
    [Total offres],
    DESC
)
```

> ⚠️ Cette mesure retourne une table, à utiliser dans un visuel de type **Table** ou avec `CONCATENATEX` :

```dax
Nom Top Pays =
CONCATENATEX(
    TOPN( 1, VALUES( Clean[Pays_corrigé] ), [Total offres], DESC ),
    Clean[Pays_corrigé],
    ", "
)
```

**Où l'ajouter :** Card dynamique "Pays #1" sur *Vue d'ensemble*.

---

## 🎨 ÉTAPE 2 — Améliorations visuelles page par page

### Page 1 — Vue d'ensemble

| Action | Détail |
|--------|--------|
| ➕ Ajouter 2 Cards | `Salaire Médian` et `Nb Pays Distincts` |
| ➕ Ajouter 1 Card | `Variation MoM Offres` avec icône conditionnel (▲▼) |
| 🔧 Line Chart | Ajouter `Total Offres YTD` comme deuxième série |
| 🎨 Formatage conditionnel | Sur les cards : rouge si variation < 0, vert si > 0 |
| 🔧 Gauge | Ajouter une cible dynamique au lieu de `Valeur Cible` fixe |

**Disposition recommandée :**
```
[Total Offres]  [Avg Salary]  [Salaire Médian]  [Taux Remote]  [Nb Pays]
[          Gauge Health Insurance          ]  [  Variation MoM  ]
[     Bar Chart Pays       ]   [     Line Chart Tendance     ]
```

---

### Page 2 — Analyse Salariale

| Action | Détail |
|--------|--------|
| ➕ Colonne Tableau | Ajouter `Rang Salaire Rôle`, `% Part des Offres`, `Fourchette Salariale` |
| 🔧 Bar Chart | Ajouter `Score Attractivité` comme deuxième barre (clustered) |
| 🔧 Treemap | Utiliser `Score Attractivité` à la place de `Avg Salary Annual` pour la taille |
| ➕ Nouveau visuel | Scatter Chart : axe X = `Avg Salary Annual`, Y = `Total offres`, taille = `Score Attractivité` |
| 🎨 Formatage conditionnel | Colorer le tableau selon le `Rang Salaire Rôle` |

---

### Page 3 — Compétences *(page actuellement quasi-vide)*

Cette page ne contient qu'un slicer. Voici comment la compléter :

| Action | Détail |
|--------|--------|
| ➕ Bar Chart horizontal | Top compétences par `Total offres` (si colonne `skill` dans `Clean`) |
| ➕ Word Cloud (custom visual) | Fréquence des compétences mentionnées |
| ➕ Table | Compétences × Salaire moyen |
| ➕ Card | "Compétence la plus demandée" avec mesure texte dynamique |
| ➕ Slicer Role | Filtrer les compétences par rôle |

> 💡 Si la table `Clean` ne contient pas de colonne `skill`, créer une table distincte `Compétences` via **Power Query** en décomposant une colonne texte avec `Text.Split`.

---

## 🛠️ ÉTAPE 3 — Mise en place technique (pas à pas)

### 3.1 — Créer toutes les mesures DAX

1. Ouvrir **Power BI Desktop**
2. Dans le volet **Données** (droite), cliquer sur la table `Mesure`
3. Onglet **Modélisation** → **Nouvelle mesure**
4. Coller le code DAX → **Entrée**
5. Répéter pour chaque mesure (§1.1 à §1.10)

### 3.2 — Ajouter les Cards (Vue d'ensemble)

1. Page *Vue d'ensemble* → cliquer dans une zone vide
2. Volet **Visualisations** → icône **Nouvelle carte** (Card)
3. Glisser la mesure depuis le volet **Données** → champ **Valeur**
4. Ajuster la taille et position pour aligner avec les cards existantes

### 3.3 — Modifier le Line Chart

1. Cliquer sur le Line Chart existant
2. Dans le volet **Visualisations** → champ **Valeurs Y secondaire**
3. Glisser `Total Offres YTD` dans ce champ
4. Activer l'axe Y secondaire dans les options de formatage

### 3.4 — Ajouter le Scatter Chart (Analyse Salariale)

1. Volet **Visualisations** → **Nuage de points** (Scatter Chart)
2. Configurer :
   - **Axe X** : `Avg Salary Annual`
   - **Axe Y** : `Total offres`
   - **Taille** : `Score Attractivité`
   - **Légende** : `Clean[role]`
3. Activer les **info-bulles** avec `Rang Salaire Rôle`

### 3.5 — Formatage conditionnel sur le tableau

1. Cliquer sur le Tableau croisé → **Formater**
2. Sur la colonne `Rang Salaire Rôle` → **Mise en forme conditionnelle** → **Couleur d'arrière-plan**
3. Règle : valeur 1 = vert foncé, valeur N = rouge clair

### 3.6 — Thème visuel cohérent

1. Onglet **Affichage** → **Thèmes** → choisir **Divergent** *(déjà inclus dans le fichier)*
2. Couleurs suggérées pour la présentation :
   - Primaire : `#1A3C5E` (bleu marine professionnel)
   - Accent : `#F4A300` (orange dynamique)
   - Positif : `#2ECC71` | Négatif : `#E74C3C`

---

## 📝 ÉTAPE 4 — Storytelling pour la présentation

### Narrative recommandée (3 minutes)

**Slide 1 – Vue d'ensemble :**
> "Ce tableau de bord analyse **[N] offres d'emploi** collectées sur [période]. Le salaire moyen est de **[X]$**, avec une médiane de **[Y]$** — l'écart révèle des postes très bien rémunérés qui tirent la moyenne vers le haut."

**Slide 2 – Analyse Salariale :**
> "En croisant salaire, avantages et télétravail, j'ai créé un **Score d'Attractivité** qui classe les rôles au-delà du seul critère salarial. Data Engineer et ML Engineer arrivent en tête."

**Slide 3 – Compétences :**
> "Les compétences les plus demandées sont [X, Y, Z]. Ce filtre par rôle permet à un candidat d'identifier exactement ce qu'il doit développer pour maximiser ses chances."

---

## 🏆 Récapitulatif des mesures à créer

| # | Mesure | Formule clé | Page cible |
|---|--------|-------------|------------|
| 1 | Salaire Médian | `MEDIAN(Clean[salary_year_avg])` | Vue d'ensemble |
| 2 | Salaire Max | `MAX(Clean[salary_year_avg])` | Analyse Salariale |
| 3 | Salaire Min | `MIN(Clean[salary_year_avg])` | Analyse Salariale |
| 4 | Fourchette Salariale | `[Salaire Max] - [Salaire Min]` | Analyse Salariale |
| 5 | Variation MoM Offres | `DATEADD(..., -1, MONTH)` | Vue d'ensemble |
| 6 | Total Offres YTD | `TOTALYTD(...)` | Vue d'ensemble |
| 7 | Score Attractivité | Salaire × Assurance × Remote | Analyse Salariale |
| 8 | Rang Salaire Rôle | `RANKX(ALLSELECTED(...))` | Analyse Salariale |
| 9 | % Part des Offres | `DIVIDE([Total offres], ALL)` | Analyse Salariale |
| 10 | Nb Pays Distincts | `DISTINCTCOUNT(Clean[Pays_corrigé])` | Vue d'ensemble |
| 11 | Nom Top Pays | `CONCATENATEX(TOPN(...))` | Vue d'ensemble |

---

> 💼 **Conseil entretien** : Mentionnez que vous avez utilisé `ALLSELECTED` (vs `ALL`) pour respecter les filtres des slicers — c'est un détail technique qui distingue les profils avancés.

---

*Généré le 13 mai 2026 | Source : Reporting_Job.pbix | Tables : Clean, Calendrier, Mesure*