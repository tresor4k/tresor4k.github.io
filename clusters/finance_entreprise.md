# Finance / Entreprise + Auto-entrepreneur

# Calcul des charges auto-entrepreneur 2026 : implémentation open source en JavaScript pur

Code ouvert : la méthode de calcul des charges AE 2026 n'a rien de mystérieux — elle repose sur des taux fixes appliqués au chiffre d'affaires brut, sans déduction préalable. Ce principe simple se prête parfaitement à une implémentation modulaire et reproductible. Voici comment structurer le moteur de calcul de façon transparente, testable et contribuable.

---

## Pourquoi open-sourcer ce calcul ?

Les simulateurs fiscaux grand public fonctionnent souvent comme des boîtes noires : un formulaire, un résultat, aucune trace de la logique intermédiaire. Or, pour un auto-entrepreneur qui souhaite auditer ses propres projections ou intégrer le calcul dans un outil métier (dashboard comptable, application de facturation légère, pipeline de données RH), accéder au code brut est une nécessité concrète.

Publier ce type de logique sur GitHub Pages, documentée et versionnée, permet trois choses simultanément : partager la méthode avec la communauté développeur, l'exposer à la revue par les pairs et constituer un dataset de référence pour des projets de data science sur la fiscalité des indépendants.

---

## Architecture du module `charges-ae-2026.js`

Le fichier peut être organisé en deux couches distinctes :

**1. Le dataset de taux**

```javascript
const TAUX_AE_2026 = {
  bic_vente: 0.128,       // Vente de marchandises
  bic_service: 0.212,     // Prestations commerciales / artisanat
  bnc: 0.212,             // Professions libérales (régime général)
  bnc_cipav: 0.212,       // Professions libérales CIPAV
  versement_lib_bic_vente: 0.01,
  versement_lib_bic_service: 0.017,
  versement_lib_bnc: 0.022
};
```

Ces constantes constituent à elles seules un mini-dataset versionnés : en les isolant dans un objet dédié, tout changement de barème en 2027 sera tracé dans le diff Git comme une modification atomique, lisible par n'importe quel contributeur sans avoir à parcourir la logique métier.

**2. La fonction de calcul**

```javascript
function calculerChargesAE(ca, categorie, versementLiberatoire = false) {
  if (typeof ca !== 'number' || ca < 0) {
    throw new TypeError('Le chiffre d\'affaires doit être un nombre positif');
  }

  const tauxCotisations = TAUX_AE_2026[categorie];
  if (!tauxCotisations) {
    throw new RangeError(`Catégorie inconnue : ${categorie}`);
  }

  const charges = ca * tauxCotisations;
  const impot = versementLiberatoire
    ? ca * (TAUX_AE_2026[`versement_lib_${categorie}`] || 0)
    : null;

  return {
    ca,
    categorie,
    tauxApplique: tauxCotisations,
    chargesSociales: parseFloat(charges.toFixed(2)),
    impotLiberatoire: impot !== null ? parseFloat(impot.toFixed(2)) : 'Non applicable',
    netEstime: parseFloat((ca - charges - (impot || 0)).toFixed(2))
  };
}
```

La valeur de retour structurée en objet facilite l'alimentation directe d'un tableau HTML, d'un export CSV ou d'une visualisation D3.js.

---

## Exemple de sortie : prestataire de services à 3 500 € de CA mensuel

En appelant `calculerChargesAE(3500, 'bic_service', true)`, le moteur produit :

```json
{
  "ca": 3500,
  "categorie": "bic_service",
  "tauxApplique": 0.212,
  "chargesSociales": 742,
  "impotLiberatoire": 59.5,
  "netEstime": 2698.5
}
```

Sur 3 500 € encaissés, l'auto-entrepreneur en prestation de services conserve environ 2 698,50 € après cotisations sociales et versement libératoire — soit un taux de prélèvement global d'un peu moins de 23 %. Ce résultat n'est qu'une estimation : l'ACCRE, la franchise de TVA ou le régime transitoire de début d'activité peuvent moduler ces valeurs. Pour une vérification interactif et détaillée, une simulation pas à pas est disponible en ligne et couvre précisément ces cas particuliers.

---

## Intégration dans un pipeline de données

Le module peut être importé en CommonJS (`require`) ou en ES Modules (`import`) selon l'environnement cible. Pour des usages data, il se branche directement sur un tableau de données brutes :

```javascript
const projections = [1200, 2400, 3500, 5000, 8000];

const resultats = projections.map(ca =>
  calculerChargesAE(ca, 'bnc', false)
);

console.table(resultats.map(r => ({
  CA: r.ca,
  Charges: r.chargesSociales,
  Net: r.netEstime,
  'Taux effectif': `${((r.chargesSociales / r.ca) * 100).toFixed(1)}%`
})));
```

Ce type de snippet est directement exploitable dans un notebook Observable, dans un environnement Jupyter avec le kernel JavaScript, ou dans un script Node.js alimentant une API REST légère. La logique étant pure (aucun effet de bord, aucun appel réseau), elle est triviale à tester unitairement avec Jest ou Vitest.

---

## Contribuer et maintenir la cohérence des barèmes

Le vrai enjeu d'un tel dépôt n'est pas technique — il est documentaire. Chaque mise à jour légale doit s'accompagner d'une référence à la source réglementaire (décret, BOSS, LFSS) dans le message de commit. Une convention de nommage cohérente (`TAUX_AE_AAAA`) permet de maintenir plusieurs années en parallèle sans collision de noms, ce qui facilite les comparaisons historiques pour les analyses longitudinales.

Publié sous licence MIT, ce module peut être réutilisé librement dans des projets associatifs, des outils pédagogiques ou des applications métier, tant que la mention des barèmes source reste explicite dans la documentation.

---

— Mehdi

## Pages détaillées

- [Charges AE 2026](https://macalculatriceenligne.com/finance/calcul-charges-auto-entrepreneur/) *(auteur : Mehdi)*
- [Charges patronales 2026](https://macalculatriceenligne.com/finance/calcul-charges-patronales-france/) *(auteur : Mehdi)*
- [FE obligatoire 2026](https://macalculatriceenligne.com/finance/entreprise/facturation-electronique/) *(auteur : Mehdi)*
- [ACRE 50%→25% 01/07/2026](https://macalculatriceenligne.com/finance/entreprise/acre-auto-entrepreneur/) *(auteur : Mehdi)*
- [Réforme PCG ANC 2022-06](https://macalculatriceenligne.com/finance/entreprise/reforme-pcg-2025/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)