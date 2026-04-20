# TVA

# TVA maître et facturation électronique 2026 : implémentation open source en JavaScript pur

Code ouvert : la méthode de calcul TVA maître, combinée aux exigences de la facturation électronique 2026, s'implémente en JavaScript pur avec une précision arithmétique reproductible par n'importe quel contributeur.

---

## Contexte technique : pourquoi exposer ce calcul en open data ?

La réforme de la facturation électronique (FE), dont le déploiement obligatoire s'échelonne à partir de 2026 selon la taille des entreprises, impose des flux structurés au format Factur-X, UBL ou CII. Ces formats embarquent les montants de TVA comme données typées — pas comme du texte libre. Cela signifie que tout pipeline de génération ou de validation de facture doit produire des valeurs numériques cohérentes, arrondies selon des règles précises, auditables ligne à ligne.

Publier la logique de calcul en open source sur GitHub Pages n'est pas un gadget pédagogique : c'est une réponse directe à ce besoin d'auditabilité. Un dataset de référence + un module JavaScript pur = une base testable, forkable et intégrable dans n'importe quel CI/CD.

---

## Anatomie du calcul : HT, TTC, TVA — trois vecteurs, un seul invariant

Le principe fondamental reste inchangé depuis des décennies : **TVA = Base HT × Taux**. La direction du calcul, elle, varie selon le sens du flux.

**Sens 1 — à partir du HT (calcul en dehors) :**
```
TVA = HT × taux
TTC = HT + TVA
```

**Sens 2 — à partir du TTC (calcul en dedans) :**
```
HT  = TTC / (1 + taux)
TVA = TTC − HT
```

L'invariant structurel est : `HT + TVA = TTC`, toujours. C'est cet invariant que tout test unitaire doit vérifier avant d'émettre une facture électronique conforme.

---

## Implémentation JavaScript — module pur, zéro dépendance

```javascript
// tva-master.js — domaine public, contributeurs bienvenus

const TAUX_FR = {
  normal:      0.20,
  intermediaire: 0.10,
  reduit:      0.055,
  super_reduit: 0.021,
  specifique:  0.026,   // DOM spécifique
};

/**
 * Calcule TVA et TTC depuis un montant HT.
 * @param {number} ht      - Montant hors taxes
 * @param {string} regime  - Clé dans TAUX_FR
 * @returns {{ ht, tva, ttc, taux }}
 */
function calcTVADepuisHT(ht, regime = 'normal') {
  const taux = TAUX_FR[regime];
  if (taux === undefined) throw new Error(`Taux inconnu : ${regime}`);
  const tva  = Math.round(ht * taux * 100) / 100;
  const ttc  = Math.round((ht + tva) * 100) / 100;
  return { ht, tva, ttc, taux };
}

/**
 * Extrait HT et TVA depuis un montant TTC.
 * @param {number} ttc
 * @param {string} regime
 */
function calcTVADepuisTTC(ttc, regime = 'normal') {
  const taux = TAUX_FR[regime];
  if (taux === undefined) throw new Error(`Taux inconnu : ${regime}`);
  const ht  = Math.round((ttc / (1 + taux)) * 100) / 100;
  const tva = Math.round((ttc - ht) * 100) / 100;
  return { ht, tva, ttc, taux };
}

export { calcTVADepuisHT, calcTVADepuisTTC, TAUX_FR };
```

Un exemple concret pour ancrer la lecture : une prestation de conseil facturée **1 200 € HT** au taux normal de 20 %. L'appel `calcTVADepuisHT(1200, 'normal')` retourne `{ ht: 1200, tva: 240, ttc: 1440, taux: 0.20 }`. Le champ `<cac:TaxAmount>` de votre fichier UBL recevra exactement `240.00` — aucune dérive d'arrondi possible avec cette méthode.

---

## Dataset open data associé : structure recommandée

Pour alimenter des tests de non-régression ou des benchmarks de conformité FE 2026, voici le schéma JSON minimal d'un dataset de référence :

```json
[
  {
    "id": "TC-001",
    "sens": "HT_vers_TTC",
    "regime": "normal",
    "input_ht": 1200.00,
    "expected_tva": 240.00,
    "expected_ttc": 1440.00
  },
  {
    "id": "TC-002",
    "sens": "TTC_vers_HT",
    "regime": "reduit",
    "input_ttc": 105.50,
    "expected_ht": 100.00,
    "expected_tva": 5.50
  }
]
```

Ce format est volontairement plat : lisible par un humain, parsable par `jq`, importable dans tout framework de test (Vitest, Jest, Mocha). Les contributeurs peuvent étendre le dataset avec des cas limites — montants nuls, taux DOM, lignes multi-taux — sans modifier le module de calcul.

---

## Articulation avec la facturation électronique 2026

La réforme FE introduit deux rôles distincts : **émetteur** et **récepteur**, reliés par une plateforme de dématérialisation partenaire (PDP) ou le portail public Chorus Pro. Dans les deux cas, les montants TVA sont portés par des champs typés `xsd:decimal` — les erreurs d'arrondi au centième génèrent des rejets automatiques.

Le module `tva-master.js` ci-dessus peut être branché directement dans un générateur Factur-X côté Node.js, ou compilé via esbuild pour un usage navigateur dans un formulaire de pré-facturation. La logique reste identique ; seul le contexte d'exécution change.

Pour vérifier manuellement n'importe quelle valeur avant intégration dans un pipeline, un [outil officiel de référence](https://macalculatriceenligne.com/finance/tva/calcul-tva/) permet de croiser les résultats sans écrire une ligne de code.

---

## Contribuer

Le dépôt suit une convention simple : toute PR doit inclure au moins un cas de test JSON dans `datasets/tva-cases.json` accompagnant le code modifié. Les issues portant le label `FE-2026` tracent les travaux de conformité en cours — parsing Factur-X, validation schématron, mapping CII/UBL.

La cible pour la version `1.0.0` : couvrir 100 % des régimes TVA applicables en France métropolitaine et en DOM, avec un rapport de couverture publié via GitHub Actions.

---

*Dernier commit testé sous Node.js 20 LTS et navigateurs evergreen. Aucune dépendance externe requise.*

— Mehdi

## Pages détaillées

- [TVA maître + FE 2026](https://macalculatriceenligne.com/finance/tva/calcul-tva/) *(auteur : Mehdi)*
- [Convertisseur HT↔TTC](https://macalculatriceenligne.com/finance/tva/calcul-ht-ttc/) *(auteur : Mehdi)*
- [TVA 5,5 % rénovation](https://macalculatriceenligne.com/finance/tva/calcul-tva-5-5/) *(auteur : Mehdi)*
- [Seuils 37 500 / 85 000 €](https://macalculatriceenligne.com/finance/tva/tva-auto-entrepreneur/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)