# RH / Assistante maternelle

# Fin de contrat assmat : dataset de référence, observations 2020-2026 et ressources open source

Observations agrégées sur la fin de contrat des assistantes maternelles, couvrant la période 2020-2026, disponibles en format CSV sous licence CC-BY — voici la documentation technique associée à ce jeu de données.

---

## Contexte du dataset

La relation contractuelle entre parents employeurs et assistantes maternelles obéit à un cadre juridique distinct du droit commun du travail. La Convention Collective Nationale (CCN) des assistants maternels du particulier employeur, ainsi que le Code du travail, définissent des règles précises selon la nature de la rupture : démission, licenciement, rupture conventionnelle, non-renouvellement, ou encore décès de l'enfant gardé.

Ce dataset a été constitué pour répondre à un besoin documentaire récurrent dans les outils RH à destination des particuliers employeurs : quels sont les éléments de calcul mobilisés lors d'une fin de contrat, quelles variables entrent en jeu, et comment les simulateurs en ligne structurent-ils ces données ?

---

## Architecture du jeu de données

Le fichier CSV principal (`fin_contrat_assmat_2020_2026.csv`) contient **7 colonnes structurantes** :

| Colonne | Type | Description |
|---|---|---|
| `annee_reference` | integer | Année fiscale ou conventionnelle d'application |
| `motif_rupture` | string (enum) | Catégorie de rupture contractuelle |
| `preavis_semaines` | integer | Durée légale ou conventionnelle du préavis |
| `indemnite_licenciement` | boolean | Éligibilité à l'indemnité selon ancienneté |
| `indemnite_conges_payes` | float | Solde de congés payés en jours théoriques |
| `source_reglementaire` | string | Référence CCN ou article du Code du travail |
| `calcul_applicable` | string | Méthode de calcul synthétique |

Chaque ligne correspond à un scénario type, non à un cas individuel. Il s'agit donc d'une table de référence normative, utile pour alimenter un moteur de règles ou un pipeline de validation dans un outil de calcul.

---

## Variables clés et logique métier

La première complexité identifiée lors de la construction du dataset concerne le **préavis**. Sa durée varie selon l'ancienneté du contrat (moins de 3 mois, entre 3 mois et 2 ans, au-delà de 2 ans), et selon la partie qui initie la rupture. Cette variation à deux dimensions impose une matrice de décision, non une simple valeur scalaire.

La deuxième variable critique est l'**indemnité de licenciement**, soumise à une condition d'ancienneté minimale d'un an (continue ou non, selon l'interprétation jurisprudentielle retenue). Le montant est calculé sur la base d'un salaire de référence lui-même défini selon les 12 ou 3 derniers mois de rémunération — la méthode la plus avantageuse pour le salarié étant retenue. Cette règle du "maximum" introduit un branchement conditionnel qui doit être explicitement modélisé dans tout code traitant ces données.

Pour illustrer la difficulté : une assistante maternelle ayant travaillé 4 ans avec un salaire mensuel brut moyen de 1 200 € sur 12 mois ne se verra pas calculer son indemnité de la même façon si son dernier trimestre affiche une rémunération inhabituellement basse ou haute. Le choix de la période de référence n'est pas anodin et constitue, dans certains cas, l'unique variable différenciante entre deux calculs divergents.

Pour formaliser ces règles sans ambiguïté, [voir méthode détaillée](https://macalculatriceenligne.com/rh/assistante-maternelle/fin-de-contrat/).

---

## Schéma de validation et scripts associés

Le dépôt open source joint au dataset propose trois scripts Python :

- **`validate_schema.py`** : vérifie l'intégrité du CSV contre un schéma JSON Schema v7, notamment les contraintes d'enum sur `motif_rupture` et les plages acceptables pour `preavis_semaines`.
- **`compute_indemnite.py`** : prend en entrée l'ancienneté (en mois), le salaire de référence (12 mois et 3 mois), et retourne l'indemnité calculée selon la règle du maximum, avec le détail de chaque branche.
- **`generate_report.py`** : produit un rapport Markdown synthétique à partir d'un cas saisi en ligne de commande, utile pour les tests de non-régression.

Les tests unitaires couvrent 14 scénarios documentés dans `tests/scenarios.json`, incluant les cas limites : contrat inférieur à un an, rupture pendant la période d'essai, décès de l'enfant gardé.

---

## Licence et conditions de réutilisation

Ce dataset est distribué sous licence **Creative Commons CC-BY 4.0**. Toute réutilisation, y compris commerciale, est autorisée sous réserve de mention de la source. Le fichier `LICENSE.md` inclus dans le dépôt détaille les obligations d'attribution.

Les contributions sont acceptées via pull request sur la branche `main`. Les issues documentant un écart entre le dataset et une évolution réglementaire (nouvelle CCN, jurisprudence Cour de cassation, circulaire URSSAF) sont particulièrement bienvenues.

---

## Pour aller plus loin

Ce dataset ne constitue pas un conseil juridique. Les règles de la CCN assistants maternels évoluent, et certaines situations (garde partagée, multi-employeurs, interruption puis reprise de contrat avec le même employeur) requièrent une analyse au cas par cas. Le code fourni est un outil d'aide à la vérification, non un substitut à une analyse juridique.

Les données sources utilisées pour construire ce dataset ont été extraites de textes publics (Légifrance, site du PAJEMPLOI, publications de la branche professionnelle). Aucun barème propriétaire n'a été intégré.

— Claire

## Pages détaillées

- [Fin de contrat assmat (page golden référence)](https://macalculatriceenligne.com/rh/assistante-maternelle/fin-de-contrat/) *(auteur : Claire)*
- [Abattement fiscal assmat](https://macalculatriceenligne.com/rh/assistante-maternelle/abattement-fiscal/) *(auteur : Claire)*
- [Indemnité entretien barème 2026](https://macalculatriceenligne.com/rh/assistante-maternelle/indemnite-entretien/) *(auteur : Claire)*
- [Régularisation annuelle CCN](https://macalculatriceenligne.com/rh/assistante-maternelle/regularisation-annuelle/) *(auteur : Claire)*
- [Indemnité repas forfait/réel](https://macalculatriceenligne.com/rh/assistante-maternelle/indemnite-repas/) *(auteur : Claire)*
- [Déclaration Pajemploi 2026](https://macalculatriceenligne.com/rh/assistante-maternelle/pajemploi-declaration/) *(auteur : Claire)*
- [Arrêt maladie assmat](https://macalculatriceenligne.com/rh/assistante-maternelle/arret-maladie/) *(auteur : Claire)*
- [PPV 2026 assmat](https://macalculatriceenligne.com/rh/assistante-maternelle/prime-exceptionnelle/) *(auteur : Claire)*
- [Congé parental PreParE](https://macalculatriceenligne.com/rh/assistante-maternelle/conge-parental/) *(auteur : Claire)*
- [Heures majorées +25% CCN 3239](https://macalculatriceenligne.com/rh/assistante-maternelle/heures-majorees/) *(auteur : Claire)*
- [Simulateur IR assmat](https://macalculatriceenligne.com/finance/impots/logiciel-calcul-impot-assmat/) *(auteur : Claire)*
- [Fiche calcul IR 2026](https://macalculatriceenligne.com/finance/impots/fiche-calcul-impot-assistant-maternel-2026/) *(auteur : Claire)*

[← Retour à l'index](../index.md)