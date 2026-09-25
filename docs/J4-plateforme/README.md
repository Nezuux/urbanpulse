# J4 — Plateforme

## 1. Objectif du jalon

Le jalon J4 a pour objectif de définir l’architecture technique de la plateforme UrbanPulse. Il décrit les principaux composants, les flux de données, les responsabilités de chaque couche et les choix retenus pour construire un MVP évolutif.

L’architecture doit rester suffisamment simple pour être mise en œuvre par l’équipe, tout en permettant d’ajouter de nouvelles sources, de nouveaux indicateurs et de nouvelles visualisations.

## 2. Principes d’architecture

La plateforme repose sur les principes suivants :

- séparation entre collecte, transformation, stockage, analyse et restitution
- reproductibilité des traitements et conservation des données sources
- utilisation prioritaire de sources ouvertes et documentées
- développement progressif à partir d’un périmètre MVP
- traçabilité des transformations et des indicateurs produits
- possibilité de faire évoluer les composants sans réécrire l’ensemble du projet

## 3. Architecture fonctionnelle

Le système est organisé en cinq couches :

1. **Sources** : trafic, transport public TBM, qualité de l’air et données réglementaires.
2. **Collecte** : récupération manuelle ou automatisée des fichiers, flux et API disponibles.
3. **Préparation** : contrôle, nettoyage, normalisation et enrichissement des données.
4. **Analyse** : calcul des KPI, analyses temporelles et croisements entre sources.
5. **Restitution** : tableaux de bord, graphiques, synthèses et éléments de preuve.

```mermaid
flowchart LR
	A[Sources publiques] --> B[Collecte]
	B --> C[Données brutes]
	C --> D[Nettoyage et normalisation]
	D --> E[Données préparées]
	E --> F[Indicateurs et analyses]
	F --> G[Tableaux de bord]
	F --> H[Rapport et preuves]
```

## 4. Composants techniques envisagés

### 4.1. Collecte

La collecte doit permettre d’intégrer des fichiers CSV, des fichiers JSON, des flux GTFS et, lorsque cela est possible, des API publiques. Chaque collecte doit conserver :

- la source d’origine
- la date de récupération
- le format initial
- la période couverte
- les éventuels paramètres de requête

### 4.2. Traitement

Les traitements seront réalisés principalement avec Python et des bibliothèques adaptées à la manipulation de données tabulaires. Ils devront couvrir :

- la conversion des formats
- la gestion des valeurs manquantes
- l’harmonisation des dates et heures
- la standardisation des identifiants géographiques et des axes
- la détection des doublons et des valeurs anormales

Les scripts de traitement seront stockés dans [src](../../src) et organisés par source ou par étape de traitement.

### 4.3. Stockage

Le stockage est séparé en trois niveaux logiques :

- **raw** : données conservées dans leur format initial
- **processed** : données nettoyées et harmonisées
- **analytics** : tables ou fichiers destinés aux indicateurs et aux visualisations

Pour le MVP, un stockage local structuré peut être utilisé afin de limiter la complexité de déploiement. Une base de données relationnelle pourra être ajoutée lorsque le volume, la fréquence de mise à jour ou les besoins de requêtage le justifieront.

Les données volumineuses ou sensibles ne doivent pas être versionnées directement dans Git. Le dépôt conserve les scripts, les métadonnées, les échantillons légers et les preuves nécessaires à la reproductibilité.

### 4.4. Analyse

La couche d’analyse produit les indicateurs définis lors du cadrage :

- trafic par axe et par période
- niveau de congestion ou vitesse moyenne lorsque la donnée est disponible
- fréquence et régularité des transports publics
- indicateurs de qualité de l’air
- évolutions temporelles et variations territoriales
- rapprochements entre mobilité et environnement

Chaque indicateur devra préciser sa définition, son unité, sa période, sa source et ses limites d’interprétation.

### 4.5. Restitution

Les livrables de restitution seront placés dans [dashboards](../../dashboards). Ils devront permettre :

- une lecture rapide des KPI principaux
- un filtrage par période et territoire lorsque les données le permettent
- une comparaison entre mobilité et environnement
- l’identification des zones ou périodes prioritaires
- une interprétation compréhensible pour un public non technique

## 5. Organisation des flux de données

Le flux nominal est le suivant :

1. identifier et documenter la source dans le registre
2. récupérer un échantillon ou une première extraction
3. déposer la donnée brute dans l’espace prévu
4. contrôler le schéma, les types et la qualité
5. appliquer les transformations documentées
6. produire les tables ou fichiers d’analyse
7. calculer les indicateurs
8. vérifier les résultats avant restitution

Une erreur de collecte ou de qualité doit être signalée avant la production des KPI afin d’éviter de présenter une analyse fondée sur des données incomplètes.

## 6. Structure technique cible

La structure du dépôt doit évoluer progressivement vers une organisation de ce type :

```text
urbanpulse/
├── data/
│   ├── raw/
│   ├── processed/
│   └── analytics/
├── src/
│   ├── ingestion/
│   ├── transformation/
│   ├── analysis/
│   └── quality/
├── dashboards/
├── preuves/
└── docs/
```

Cette structure est une cible d’organisation. Elle sera créée au fur et à mesure des besoins réels du MVP, afin d’éviter d’ajouter des composants sans usage démontré.

## 7. Qualité, sécurité et exploitation

Les contrôles minimums à prévoir sont :

- présence des colonnes attendues
- contrôle des types et des formats de date
- détection des valeurs nulles et des doublons
- vérification des bornes plausibles
- comparaison des volumes avant et après transformation
- conservation d’un échantillon ou d’une preuve de traitement

Le projet ne doit pas stocker de données personnelles non nécessaires à l’analyse. Les accès aux éventuelles API ou services externes doivent rester hors du dépôt Git et être configurés localement.

## 8. Choix du périmètre MVP

Le premier MVP doit privilégier un parcours complet et vérifiable sur un nombre limité de sources :

1. une source de trafic
2. une source de transport public
3. une source de qualité de l’air
4. un jeu d’indicateurs communs
5. une restitution synthétique

L’intégration de toutes les sources ou l’automatisation complète ne constitue pas un prérequis pour valider l’architecture. La priorité est de démontrer la chaîne complète, de la collecte à la décision.

## 9. Livrables attendus pour J4

Le jalon J4 doit produire :

- une architecture fonctionnelle et technique documentée
- un schéma des flux de données
- une organisation cible du dépôt
- les principes de stockage et de traitement
- la définition du périmètre MVP
- les règles minimales de qualité et de sécurité

## 10. Points à valider avant la mise en œuvre

Les prochaines validations devront porter sur :

- l’accès effectif aux sources retenues
- les formats et volumes réellement disponibles
- la fréquence de mise à jour des données
- les besoins de stockage du MVP
- l’outil de visualisation retenu
- la capacité à reproduire les principaux indicateurs

## 11. Conclusion

Le jalon J4 définit une architecture progressive pour UrbanPulse. La séparation des couches permet de rendre les traitements lisibles, de contrôler la qualité des données et de faire évoluer la plateforme sans perdre la traçabilité du projet.

Cette architecture servira de base à la réalisation du MVP, puis à la préparation du jalon J5 consacré aux analyses avancées, à l’IA et à la restitution finale.
