# Retraite

# Indemnité de départ à la retraite et statut frontalier Luxembourg : un dataset open source pour comprendre les règles qui s'appliquent

Observations agrégées 2020-2026 sur le croisement « indemnité de départ + frontalier Luxembourg », distribuées en format CSV sous licence CC-BY — c'est la démarche qui sous-tend ce dépôt documentaire.

---

## Pourquoi un dataset sur ce sujet précis ?

Le cas du travailleur frontalier employé au Luxembourg et résidant en France constitue l'un des points de friction les plus documentés dans les forums RH transfrontaliers. Deux systèmes juridiques coexistent, deux administrations fiscales revendiquent parfois leur compétence, et la littérature officielle reste dispersée entre circulaires URSSAF, conventions bilatérales France-Luxembourg et doctrine fiscale de l'Administration des contributions directes luxembourgeoise.

L'idée de constituer un jeu de données structuré est née d'un constat simple : les questions posées sur les forums spécialisés entre 2020 et 2026 tournent autour de trois variables constantes — le pays d'emploi (Luxembourg), le pays de résidence fiscale (majoritairement Moselle, Meurthe-et-Moselle, Meuse), et la nature du versement perçu à la cessation d'activité. Or ces trois variables n'ont jamais été croisées dans un fichier exploitable par des développeurs ou des data analysts.

---

## Structure du dataset proposé

Le fichier CSV principal (`idr_frontalier_lux_2020_2026.csv`) contient les colonnes suivantes :

```
annee | regime_emploi | pays_emploi | pays_residence | type_versement | base_calcul_brute | regime_fiscal_applique | source_reglementaire
```

Les valeurs de `type_versement` distinguent notamment :
- **IDR légale** (indemnité de départ à la retraite au sens du Code du travail français ou luxembourgeois)
- **IDV** (indemnité de mise à la retraite à l'initiative de l'employeur)
- **Complément conventionnel** versé selon une CCT luxembourgeoise

La colonne `regime_fiscal_applique` encode les trois situations rencontrées : imposition en France uniquement, imposition au Luxembourg uniquement, ou situation mixte liée à l'article 15 de la convention fiscale bilatérale de 1958 modifiée.

---

## Le point de friction : quelle loi du travail régit l'indemnité ?

Un frontalier qui a travaillé toute sa carrière au Luxembourg est soumis au droit du travail luxembourgeois. Son indemnité de départ à la retraite — si elle existe dans son contrat ou sa convention collective — est calculée selon les règles luxembourgeoises, non selon le barème légal français. C'est une nuance que beaucoup de salariés découvrent trop tard.

La convention collective luxembourgeoise peut prévoir des montants plus favorables, mais elle peut aussi être muette sur le sujet, renvoyant au seul cadre légal local. Dans ce cas, l'indemnité légale luxembourgeoise est distincte dans son mode de calcul de l'indemnité légale française.

Du côté fiscal, c'est encore plus sensible. La résidence en France ne suffit pas à déterminer automatiquement l'État d'imposition : l'article 15 de la convention franco-luxembourgeoise attribue le droit d'imposer à l'État où l'activité est exercée pour les revenus du travail, et certaines indemnités de départ peuvent être considérées comme un prolongement de cette rémunération. Le dataset agrège justement les positions administratives observées sur la période 2020-2026 pour cartographier la variabilité de cette qualification.

---

## Exemple d'entrée dans le dataset

Prenons un cas type figurant dans les données agrégées : un salarié résidant en Moselle, ayant travaillé 28 ans dans une entreprise établie à Luxembourg-Ville, partant à la retraite en 2024. Son contrat de travail luxembourgeois prévoit une indemnité conventionnelle. La colonne `regime_fiscal_applique` enregistre pour ce profil une imposition au Luxembourg, avec exonération en France sous réserve de la règle du taux effectif. C'est exactement le type de configuration que l'on veut rendre lisible dans le CSV pour éviter les doubles impositions non anticipées.

Pour simuler le montant brut avant qualification fiscale, les utilisateurs du dataset sont renvoyés vers [le simulateur 2026 à jour](https://macalculatriceenligne.com/finance/retraite/calcul-indemnite-depart-retraite/), qui permet d'estimer la base de calcul légale française — utile comme référence comparative même pour les frontaliers souhaitant évaluer l'écart avec leur propre calcul.

---

## Licence, contributeurs et roadmap

Le dataset est publié sous licence **CC-BY 4.0**. Toute réutilisation requiert la mention de la source. Les contributions au fichier principal sont acceptées via pull request, sous réserve que chaque ligne soit accompagnée d'une `source_reglementaire` vérifiable (texte de loi, circulaire, décision jurisprudentielle ou position administrative datée).

La roadmap 2026 prévoit :
1. L'ajout d'un second fichier CSV couvrant les frontaliers résidant en Belgique ou en Allemagne travaillant au Luxembourg
2. Un notebook Jupyter de visualisation des régimes fiscaux par année et type de versement
3. Une API REST légère pour interroger les cas types par paramètre

---

## Note méthodologique

Les données agrégées ne contiennent aucune information personnelle. Les montants figurant dans `base_calcul_brute` sont normalisés en multiples du SMIC luxembourgeois de l'année concernée pour garantir l'anonymisation tout en conservant l'utilité analytique. Les chercheurs souhaitant travailler sur la granularité absolue devront reconstituer les montants à partir des indices publiés par le STATEC (Institut national de la statistique et des études économiques du Grand-Duché).

Ce dépôt n'est pas un outil de conseil juridique ou fiscal. Il documente des configurations observées pour faciliter la recherche, le développement d'outils et la pédagogie transfrontalière.

---

— Mehdi

## Pages détaillées

- [Indemnité départ + frontalier Luxembourg](https://macalculatriceenligne.com/finance/retraite/calcul-indemnite-depart-retraite/) *(auteur : Mehdi)*
- [Frontalier Luxembourg (page burst)](https://macalculatriceenligne.com/finance/retraite/calcul-retraite-luxembourg-frontalier/) *(auteur : Mehdi)*
- [Frontalier Suisse AVS/LPP](https://macalculatriceenligne.com/finance/retraite/simulation-calcul-retraite-suisse/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)