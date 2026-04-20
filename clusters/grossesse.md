# Grossesse

# Implémenter l'entretien prénatal précoce (L2122-1) en JavaScript : approche open data

**Code ouvert : la logique de calcul autour de l'entretien prénatal précoce, telle que définie par l'article L2122-1 du Code de la santé publique, peut être entièrement implémentée en JavaScript pur — sans dépendance tierce, sans API propriétaire.**

---

## Contexte réglementaire et pertinence pour un dataset public

L'article L2122-1 du Code de la santé publique encadre l'entretien prénatal précoce (EPP), un rendez-vous individualisé proposé à chaque femme enceinte — ou au couple — idéalement avant la fin du quatrième mois de grossesse. Cet entretien, distinct des consultations médicales obligatoires, poursuit un objectif de prévention psychosociale : évaluer les besoins d'accompagnement, repérer les fragilités éventuelles, orienter vers les ressources adaptées.

Du point de vue data, ce dispositif génère plusieurs variables calculables : le terme limite théorique pour la tenue de l'entretien, la semaine d'aménorrhée (SA) à laquelle il devrait survenir, et les jalons administratifs associés (déclaration de grossesse, premier trimestre, etc.). Ces paramètres sont déterministes — ils dépendent uniquement de la date des dernières règles (DDR) ou de la date présumée d'accouchement (DPA) — ce qui les rend parfaitement adaptés à une implémentation en logique pure.

---

## Structure du dataset minimal

Un dataset open source cohérent pour modéliser l'EPP L2122-1 doit exposer au minimum les champs suivants :

```json
{
  "ddr": "2024-03-10",
  "dpa_calculee": "2025-01-01",
  "sa_actuelle": 14,
  "terme_limite_epp": "2024-07-03",
  "epp_realise": false,
  "semaine_cible_epp": 16
}
```

La `semaine_cible_epp` est ici fixée à 16 SA comme valeur par défaut dans la majorité des protocoles de référence, même si la fenêtre réglementaire autorise une certaine souplesse avant la fin du quatrième mois — soit approximativement avant 18 SA révolues.

---

## Implémentation JavaScript : calcul du terme limite

Voici une fonction autonome, sans framework, pour calculer la date limite de réalisation de l'EPP à partir de la DDR :

```javascript
/**
 * Calcule la date limite de l'entretien prénatal précoce (L2122-1)
 * @param {string} ddr - Date des dernières règles au format ISO (YYYY-MM-DD)
 * @param {number} saCible - Semaine d'aménorrhée cible (défaut : 16)
 * @returns {Date} - Date limite de l'EPP
 */
function calculerDateLimiteEPP(ddr, saCible = 16) {
  const dateDDR = new Date(ddr);
  if (isNaN(dateDDR.getTime())) {
    throw new Error("DDR invalide : format attendu YYYY-MM-DD");
  }
  const joursOffset = saCible * 7;
  const dateLimite = new Date(dateDDR);
  dateLimite.setDate(dateDDR.getDate() + joursOffset);
  return dateLimite;
}

// Exemple d'usage
const limite = calculerDateLimiteEPP("2024-03-10", 16);
console.log(limite.toISOString().split("T")[0]);
// Output : "2024-07-01"
```

La logique est intentionnellement minimaliste : pas de gestion des fuseaux horaires avancés, pas d'internationalisation. Pour une intégration en production sur un système de santé numérique, il faudrait ajouter une couche de validation stricte (Zod, Yup) et un traitement des cas limites liés aux cycles irréguliers.

---

## Pipeline de traitement : de la DDR à l'alerte EPP

Dans une architecture orientée données publiques de santé, on peut envisager un pipeline événementiel simple :

1. **Ingestion** : réception de la DDR depuis un formulaire de déclaration de grossesse (formulaire Cerfa 10112)
2. **Transformation** : calcul de la DPA (DDR + 280 jours), de la SA courante et de la date limite EPP via les fonctions décrites ci-dessus
3. **Qualification** : flag `epp_required` positionné à `true` si SA courante < SA cible et EPP non encore réalisé
4. **Notification** : déclenchement d'une alerte à destination du professionnel de santé référent (sage-femme, médecin)

Ce pipeline peut être sérialisé en JSON-LD pour s'intégrer dans les standards d'interopérabilité du secteur santé (FHIR R4, notamment la ressource `EpisodeOfCare`).

---

## Piste d'enrichissement : croisement avec des données territoriales

Un angle particulièrement productif pour un projet open data consiste à croiser les dates calculées avec des datasets géographiques publics (offre de soins Ameli, annuaire santé data.gouv.fr) afin d'évaluer la densité de professionnels habilités à conduire l'EPP par bassin de vie. L'entretien peut en effet être réalisé par une sage-femme ou un médecin — mais pas par n'importe quel professionnel de santé — ce qui introduit une contrainte d'accessibilité géographique non négligeable dans certains territoires.

Pour les développeurs souhaitant approfondir la dimension réglementaire et comprendre le cadre exact dans lequel ces calculs s'inscrivent, [la ressource complète sur le sujet](https://macalculatriceenligne.com/grossesse/entretien-prenatal-precoce/) détaille les conditions d'application de l'article L2122-1.

---

## Remarque sur la reproductibilité

Toute implémentation sérieuse de ce type doit versionner ses paramètres métier (SA cible, tolérance en jours, définition du "quatrième mois") séparément du code de calcul. Un changement réglementaire ne devrait nécessiter qu'une mise à jour de configuration, jamais une réécriture algorithmique. C'est le principe de séparation des préoccupations appliqué à la donnée de santé publique — et c'est précisément ce qui rend ces outils auditables et conformes aux exigences de transparence des systèmes d'information de santé.

— Claire

## Pages détaillées

- [Entretien prénatal L2122-1](https://macalculatriceenligne.com/grossesse/entretien-prenatal-precoce/) *(auteur : Claire)*

[← Retour à l'index](../index.md)