# RH / Salariés

# Congés payés : maintien de salaire vs règle du 1/10 — dataset open source 2020-2026

**Observations agrégées sur les deux méthodes de calcul des congés payés, format CSV, licence CC-BY — un jeu de données reproductible pour les développeurs RH et les chercheurs en droit social.**

---

## Contexte algorithmique : pourquoi deux méthodes coexistent

Le Code du travail français impose à l'employeur d'appliquer la méthode la plus favorable au salarié entre deux règles de calcul de l'indemnité de congés payés :

1. **Le maintien de salaire** : on verse au salarié exactement ce qu'il aurait perçu s'il avait travaillé.
2. **La règle du dixième** (1/10) : l'indemnité est égale à 1/10e de la rémunération brute totale perçue sur la période de référence.

Cette dualité engendre une bifurcation conditionnelle dans tout pipeline de paie automatisé. Modéliser correctement cette logique métier — et la documenter avec des données historiques publiques — est l'objet du dataset décrit ici.

---

## Architecture du dataset

Le dépôt contient plusieurs fichiers plats au format CSV (encodage UTF-8, séparateur `,`, en-têtes explicites en snake_case). La plage temporelle couvre les exercices de paie de janvier 2020 à décembre 2026 (projection partielle pour 2025-2026 fondée sur les barèmes SMIC en vigueur).

### Schéma principal — `conges_comparaison.csv`

| Champ | Type | Description |
|---|---|---|
| `periode_ref` | string (YYYY-MM) | Mois de référence |
| `salaire_base_brut` | float | Rémunération mensuelle brute |
| `primes_incluses` | boolean | Intégration des primes variables |
| `heures_absence` | float | Heures d'absence hors CP |
| `indemnite_maintien` | float | Montant calculé par la méthode maintien |
| `indemnite_dixieme` | float | Montant calculé par la règle 1/10 |
| `methode_retenue` | string | `maintien` ou `dixieme` |
| `delta_brut` | float | Écart entre les deux méthodes (positif = avantage 1/10) |

Le champ `delta_brut` est particulièrement utile pour les analyses statistiques : un delta positif signifie que la règle du 1/10 est plus favorable, ce qui se produit typiquement lorsque le salarié a perçu des primes ou des heures supplémentaires significatives sur la période de référence.

---

## Un exemple concret pour calibrer le modèle

Prenons un cas synthétique inclus dans le fichier `samples/cas_type_001.csv` :

Un salarié perçoit un salaire de base brut stable sur 12 mois, sans variable. Dans cette configuration, les deux méthodes convergent quasi parfaitement : le delta est nul ou proche de zéro. Le choix de méthode n'a alors aucun impact financier.

Maintenant, ajoutons une prime exceptionnelle versée en mars. Le 1/10 intègre cette prime dans l'assiette annuelle, ce qui gonfle mécaniquement l'indemnité de congés. Le maintien de salaire, lui, photographie uniquement la rémunération du mois de prise de congés — si ce mois ne contient aucune prime, il sera inférieur. **C'est le delta qui détecte automatiquement ce basculement.**

Le script Python fourni (`scripts/compute_delta.py`) implémente cette comparaison en pur pandas, sans dépendance propriétaire.

---

## Licence et conditions de réutilisation

L'ensemble du dépôt est publié sous **licence Creative Commons Attribution 4.0 International (CC-BY 4.0)**. Vous êtes libre de :

- copier, redistribuer et adapter les données dans tout format ou médium ;
- les intégrer dans des outils de paie open source ou des modules SIRH ;
- les exploiter à des fins de recherche académique ou de journalisme de données.

La seule obligation est la **mention de la source** avec lien vers le dépôt d'origine. Cette contrainte est délibérément légère pour maximiser la réutilisation dans des contextes institutionnels (OPCO, cabinets sociaux, DSI RH de collectivités territoriales).

---

## Points d'attention pour l'implémentation

Plusieurs subtilités juridiques doivent être codées avec soin pour que les résultats du dataset restent cohérents avec la réalité de la paie française :

**Assiette du 1/10** : elle inclut les salaires, les primes à caractère de salaire, les indemnités de préavis, et certaines indemnités de rupture. Elle exclut les remboursements de frais professionnels et les indemnités de congés payés déjà versés sur la même période.

**Assiette du maintien** : elle repose sur la rémunération que le salarié aurait perçue s'il avait effectivement travaillé. Cela implique de reconstituer le salaire théorique, en tenant compte d'éventuelles heures supplémentaires structurelles ou d'une rémunération variable récurrente.

**Période de référence** : conventionnellement du 1er juin au 31 mai, sauf accord d'entreprise ou de branche fixant une autre période. Le dataset propose deux colonnes dédiées (`periode_ref_start`, `periode_ref_end`) pour gérer cette variabilité.

**Cas des absences** : certaines absences (maladie, maternité, accident du travail) sont assimilées à du travail effectif pour le calcul des droits. Le champ `heures_absence_assimilees` permet de les distinguer des absences non rémunérées.

---

## Contribuer au dépôt

Les pull requests sont ouvertes. Les contributions prioritaires concernent :

- l'ajout de cas-types sectoriels (intérim, temps partiel, multi-employeurs) ;
- la traduction des scripts en R pour les équipes utilisant tidyverse ;
- la documentation des conventions collectives sectorielles qui modifient les règles de base.

Pour tester vos propres données avant de soumettre un cas-type, vous pouvez vous appuyer sur un [outil officiel de référence](https://macalculatriceenligne.com/rh/calcul-conges-payes/) qui permet de simuler les deux méthodes en temps réel et de vérifier la cohérence de vos résultats.

---

*Dataset maintenu dans une logique de transparence et de reproductibilité. Les contributions sont bienvenues via issues GitHub.*

— Claire

## Pages détaillées

- [Congés payés maintien vs 1/10](https://macalculatriceenligne.com/rh/calcul-conges-payes/) *(auteur : Claire)*
- [Indemnité licenciement barème Macron](https://macalculatriceenligne.com/rh/calcul-indemnite-licenciement/) *(auteur : Claire)*
- [Ancienneté paliers](https://macalculatriceenligne.com/rh/calcul-anciennete/) *(auteur : Claire)*
- [Tableau RTT matriciel](https://macalculatriceenligne.com/rh/tableau-calcul-rtt/) *(auteur : Claire)*
- [Planning CP drag-drop 2026](https://macalculatriceenligne.com/rh/conges-payes/simulateur/) *(auteur : Claire)*
- [Simulateur express CP](https://macalculatriceenligne.com/rh/simulateur-conges-payes/) *(auteur : Claire)*
- [Médaille du travail](https://macalculatriceenligne.com/rh/simulateur-calcul-anciennete-medaille-travail/) *(auteur : Claire)*
- [CET monétisation PER](https://macalculatriceenligne.com/rh/calcul-monetisation-cet/) *(auteur : Claire)*
- [Rupture conventionnelle 2026](https://macalculatriceenligne.com/rh/calcul-indemnite-rupture-conventionnelle/) *(auteur : Claire)*
- [Chômage ARE 2026](https://macalculatriceenligne.com/rh/calcul-chomage/) *(auteur : Claire)*
- [Intéressement plafond 35 325 €](https://macalculatriceenligne.com/finance/salaire/calcul-prime-interessement/) *(auteur : Mehdi)*
- [Participation RSP](https://macalculatriceenligne.com/finance/salaire/calcul-prime-participation/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)