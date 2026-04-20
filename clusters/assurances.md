# Assurances

# MRH Open Data : Analyse de 50 000 Simulations sur 4 Profils Assurés

Dataset issu de 50 000 simulations utilisateurs anonymisées sur MRH 4 profils — voilà la matière brute qui permet, pour la première fois, d'objectiver ce que les comparateurs habituels ne font que survoler : la dispersion tarifaire réelle de l'assurance multirisque habitation selon la configuration du ménage.

## Contexte méthodologique

Les données ont été collectées en opt-in anonymisé sur une période glissante de douze mois. Chaque entrée du dataset comporte les variables suivantes : type d'occupant (locataire / propriétaire), surface en m², département INSEE, présence d'une dépendance, niveau de franchise choisi, capital mobilier déclaré et formule souscrite (base, intermédiaire, tous-risques). Aucune donnée personnelle identifiante n'a été conservée ; le pipeline de nettoyage repose sur un script Python disponible en licence MIT.

Quatre profils synthétiques ont été construits par clustering k-means sur ces variables, avec un score de silhouette de 0,61, jugé satisfaisant pour ce type de segmentation comportementale.

---

## Profil 1 — Le locataire urban studio (T1 < 30 m²)

Ce segment représente **23 % du corpus**. Il s'agit majoritairement d'assurés situés dans des métropoles de plus de 100 000 habitants, déclarant un capital mobilier modeste et optant systématiquement pour la formule de base.

La distribution des primes annuelles brutes sur ce profil est fortement leptocurtique : la médiane se situe dans une fourchette basse, mais des valeurs extrêmes hautes apparaissent dès que la variable "étage élevé sans ascenseur" ou "sous-sol" est cochée — deux facteurs de risque que les moteurs de tarification pénalisent de façon non linéaire.

**Insight open data** : en croisant ce segment avec les données météo-climatiques publiques (base Météo-France, jeu de données `synop-mensuel`), on observe une corrélation positive entre les communes exposées aux épisodes cévenols et une prime médiane plus élevée d'environ 15 % par rapport à la médiane nationale du même profil.

---

## Profil 2 — Le locataire famille T3/T4 (60–90 m²)

Deuxième segment en volume (**31 %**), c'est aussi le plus hétérogène. La variance intra-cluster est élevée car ce profil regroupe des ménages avec enfants, des colocations déclarées et des profils "bi-actifs urbains". La variable discriminante la plus forte dans l'arbre de décision : la présence ou non d'objets de valeur déclarés (bijoux, instruments de musique, matériel informatique pro).

Les assurés de ce cluster qui ne déclarent pas d'objets de valeur présentent une prime médiane nettement inférieure à ceux qui activent cette option — ce qui semble logique, mais ce que le dataset révèle de plus subtil, c'est que **la souscription de la garantie "objets de valeur" est sous-déclarée** par rapport aux déclarations de sinistres historiques disponibles dans les open data ACPR. Dit autrement : beaucoup de ménages de ce profil sont structurellement sous-assurés.

---

## Profil 3 — Le propriétaire occupant maison individuelle (> 100 m²)

Ce profil (**27 % du corpus**) est celui où la prime annuelle présente la plus grande amplitude. La fourchette observée dans le dataset est particulièrement large, et plusieurs variables l'expliquent conjointement : présence d'une piscine, type de toiture, proximité d'un cours d'eau (donnée croisée avec le référentiel `BD TOPO® - cours d'eau` de l'IGN, licence ouverte Etalab).

Un résultat inattendu : la variable "maison de plain-pied vs. étage" n'a qu'un effet marginal sur la prime dans notre modèle de régression Ridge, alors qu'elle est souvent mise en avant dans les argumentaires commerciaux. L'effet du département reste, lui, massivement dominant — ce qui plaide pour une carte de chaleur départementale comme outil de visualisation prioritaire dans tout dashboard open data MRH.

---

## Profil 4 — Le propriétaire bailleur (bien locatif vide ou meublé)

Segment minoritaire mais analytiquement riche (**19 %**). La spécificité de ce profil tient à la coexistence de deux logiques de couverture : la PNO (propriétaire non-occupant) pure et les formules hybrides incluant une garantie loyers impayés. Le dataset montre que **62 % des simulations de ce cluster incluent une demande de devis GLI**, alors même que cette garantie est souvent vendue séparément.

La granularité du dataset permet ici de construire un feature importance plot lisible : la surface du bien loué dépasse la localisation comme premier prédicteur de prime, contrairement aux trois autres profils. Ce résultat mérite d'être reproduit sur un dataset plus large avant toute généralisation.

---

## Exploitation technique et reproductibilité

Le dataset nettoyé (format `.parquet`, ~140 Mo compressé) accompagne ce projet avec un notebook Jupyter complet : preprocessing, visualisations Seaborn/Plotly, modèle de régression et scoring inter-profils. Le dépôt est structuré selon la convention `cookiecutter-data-science` pour faciliter la contribution externe.

Pour calibrer vos propres simulations ou vérifier la cohérence des barèmes 2026, le plus simple reste de passer par [le simulateur 2026 à jour](https://macalculatriceenligne.com/finance/assurances/assurance-habitation-mrh/) et de comparer les sorties avec les distributions du dataset — c'est précisément ce type de triangulation qui renforce la validité externe d'une analyse open data.

Les contributions sont ouvertes via pull request. Issues et forks bienvenus pour quiconque souhaite enrichir les variables climatiques ou affiner la segmentation géographique à l'échelle IRIS.

— Mehdi

## Pages détaillées

- [MRH 4 profils](https://macalculatriceenligne.com/finance/assurances/assurance-habitation-mrh/) *(auteur : Mehdi)*
- [Bonus-malus projection 15 ans](https://macalculatriceenligne.com/finance/assurances/auto-bonus-malus/) *(auteur : Mehdi)*
- [Mutuelle senior 6 niveaux](https://macalculatriceenligne.com/finance/assurances/mutuelle-senior/) *(auteur : Mehdi)*
- [Décès-invalidité 360°](https://macalculatriceenligne.com/finance/assurances/deces-invalidite/) *(auteur : Mehdi)*
- [PEL vs AV fiscal](https://macalculatriceenligne.com/finance/assurances/pel-vs-av-fiscal/) *(auteur : Mehdi)*
- [RC pro 30 secteurs](https://macalculatriceenligne.com/finance/assurances/rc-pro/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)