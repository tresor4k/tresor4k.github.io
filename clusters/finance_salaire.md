# Finance / Salaire

# Calcul de salaire : 4 modes implémentés en JavaScript pur — approche open data

Code ouvert, méthode transparente : voici ce que signifie concrètement implémenter le calcul de salaire selon quatre modes distincts en JavaScript natif, sans dépendance externe, avec des données publiquement accessibles et vérifiables.

## Pourquoi parler de "4 modes" dans un contexte de calcul de salaire ?

Le terme recouvre quatre approches computationnelles distinctes pour calculer une rémunération nette à partir d'un brut déclaré, ou inversement. Ces modes ne sont pas arbitraires : ils correspondent à des cas d'usage réels rencontrés par les développeurs qui construisent des outils RH, des simulateurs fiscaux ou des dashboards de paie.

- **Mode brut → net** : le plus fréquent. On part d'un salaire brut et on applique les taux de cotisations salariales pour obtenir le net imposable, puis le net à payer.
- **Mode net → brut** : utile pour les négociations salariales. Le candidat annonce un net souhaité ; l'employeur veut connaître le coût brut correspondant. C'est une inversion de fonction, souvent résolue par itération ou par un ratio approximatif.
- **Mode coût employeur → net** : vision globale du recrutement. Le budget total intègre les cotisations patronales. On part du haut de la pyramide pour descendre jusqu'au virement bancaire.
- **Mode annuel → mensuel (et inversement)** : normalisation temporelle, indispensable dès qu'on compare des contrats à durée variable, du temps partiel, ou des primes exceptionnelles.

## Architecture du dataset et du code source

Dans une approche open data rigoureuse, chaque paramètre du calcul doit être externalisé dans un fichier de configuration versionné — typiquement un `rates.json` ou `baremes.js` — commité dans le dépôt et mis à jour à chaque évolution réglementaire. Cela permet à n'importe quel contributeur de vérifier, comparer ou forker les valeurs sans toucher à la logique métier.

Un exemple de structure JSON pour stocker les taux de cotisations salariales :

```json
{
  "cotisations_salariales": {
    "assurance_maladie": 0.0000,
    "retraite_base_tranche1": 0.0690,
    "retraite_complementaire": 0.0315,
    "chomage": 0.0000,
    "csg_deductible": 0.0680,
    "csg_non_deductible": 0.0240,
    "crds": 0.0050
  }
}
```

Ces valeurs sont issues de sources publiques officielles. Le code qui les consomme reste agnostique : on passe le dataset en paramètre, et la fonction de calcul s'adapte sans modification. C'est le principe de l'inversion de dépendance appliqué à la fiscalité.

## Implémentation fonctionnelle en JavaScript pur

Voici la logique centrale du mode brut → net, écrite de façon délibérément lisible et testable unitairement :

```javascript
function calculateNetFromGross(grossSalary, rates) {
  const assiette = grossSalary * 0.9825; // abattement forfaitaire CSG/CRDS
  const cotisations = Object.values(rates.cotisations_salariales)
    .reduce((acc, rate) => acc + (grossSalary * rate), 0);
  const netImposable = grossSalary - cotisations;
  return {
    gross: grossSalary,
    cotisations: parseFloat(cotisations.toFixed(2)),
    netImposable: parseFloat(netImposable.toFixed(2)),
    assietteCsgCrds: parseFloat(assiette.toFixed(2))
  };
}
```

Cette approche fonctionnelle pure — pas d'état global, pas d'effet de bord — facilite le test automatisé. On peut brancher Jest ou Vitest dessus en deux lignes et vérifier la cohérence des sorties pour cent scénarios différents.

Pour le mode inverse (net → brut), une méthode itérative converge rapidement :

```javascript
function calculateGrossFromNet(targetNet, rates, tolerance = 0.01) {
  let gross = targetNet / 0.78; // approximation initiale
  for (let i = 0; i < 100; i++) {
    const result = calculateNetFromGross(gross, rates);
    const diff = result.netImposable - targetNet;
    if (Math.abs(diff) < tolerance) break;
    gross -= diff * 0.5; // descente de gradient simplifiée
  }
  return parseFloat(gross.toFixed(2));
}
```

## Versioning des barèmes : la vraie valeur ajoutée open source

Ce qui distingue un projet open data sérieux d'un simple script isolé, c'est la traçabilité des changements réglementaires. Chaque mise à jour du barème mérite un commit atomique avec un message explicite du type `feat(rates): update retraite complementaire Q1 2025`. Les utilisateurs du package peuvent alors pinpointer exactement quelle version du barème correspond à quelle période de paie — ce qui est décisif pour les recalculs rétroactifs ou les audits sociaux.

Combiner ce dépôt avec une interface de démonstration interactive — même minimaliste, en HTML/CSS/JS vanilla hébergée sur GitHub Pages — transforme un outil technique en ressource pédagogique accessible. Pour valider vos propres implémentations ou tester manuellement un scénario précis avant de l'automatiser, une [simulation pas à pas](https://macalculatriceenligne.com/finance/salaire/calcul-salaire/) permet de confronter les sorties de votre code aux résultats attendus.

## Ce que cette architecture permet

Un développeur qui clone ce type de dépôt dispose immédiatement d'une base de calcul auditable, modifiable et extensible. Il peut ajouter un cinquième mode — calcul du SMIC horaire vers mensuel, gestion du temps partiel, simulation de prime — sans réécrire la logique existante. Il peut aussi contribuer en amont : corriger un taux, documenter une edge case, proposer un test manquant.

C'est précisément cet esprit — rendre le calcul de paie lisible, collaboratif et indépendant de toute boîte noire propriétaire — qui justifie de publier ce type d'implémentation sous licence MIT sur une plateforme publique.

— Mehdi

## Pages détaillées

- [Salaire maître 4 modes](https://macalculatriceenligne.com/finance/salaire/calcul-salaire/) *(auteur : Mehdi)*
- [Convertisseur brut/net](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-brut-net/) *(auteur : Mehdi)*
- [Cadre avec APEC](https://macalculatriceenligne.com/finance/salaire/calcul-brut-net-cadre/) *(auteur : Mehdi)*
- [Enseignant grille indiciaire](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-enseignant/) *(auteur : Claire)*
- [Apprenti décret 2025-207](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-apprenti/) *(auteur : Mehdi)*
- [Cadre PMSS 4 005 €](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-cadre/) *(auteur : Mehdi)*
- [Apprentissage + contrat pro](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-alternance/) *(auteur : Mehdi)*
- [Salaire réel IPC base 2025](https://macalculatriceenligne.com/finance/salaire/calcul-salaire-reel/) *(auteur : Mehdi)*
- [Cotisations sociales 2026](https://macalculatriceenligne.com/finance/salaire/calculer-cotisations-sociales/) *(auteur : Mehdi)*
- [IJ Sécu 1,4× SMIC](https://macalculatriceenligne.com/finance/calcul-salaire-arret-maladie/) *(auteur : Mehdi)*

[← Retour à l'index](../index.md)