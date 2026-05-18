# Autres

# Calcul de l'ascendant astrologique : la méthode du Temps Sidéral Local expliquée pas à pas

L'ascendant astrologique correspond au signe du zodiaque qui se levait à l'horizon est au moment exact de la naissance d'une personne. Contrairement au signe solaire (déterminé par la date de naissance seule), l'ascendant requiert trois données précises : l'heure de naissance exacte, le lieu de naissance (longitude et latitude), et le calcul du Temps Sidéral Local (TSL) pour cet instant et cet endroit.

*Cet article décrit la mécanique du calcul d'un point de vue mathématique et historique. L'astrologie n'a pas de fondement scientifique reconnu — son usage relève des croyances individuelles et culturelles.*

## La logique : la rotation de la Terre vue depuis le zodiaque

La Terre effectue une rotation complète en 23h 56min 4,1s (jour sidéral). Pendant cette rotation, l'observateur situé en un point fixe de la surface terrestre voit défiler les 12 signes du zodiaque sur l'horizon est, chaque signe occupant en moyenne 2 heures de défilement (mais la durée réelle varie selon la latitude et la déclinaison de chaque signe).

L'ascendant correspond donc à un calcul de position céleste à un moment et un lieu précis. Cette position est gouvernée par le **Temps Sidéral Local (TSL)**, qui mesure depuis combien de temps le point vernal (équinoxe de printemps) a culminé au méridien du lieu de naissance.

## Les quatre étapes du calcul manuel

**Étape 1 — Convertir l'heure légale en heure GMT (Temps Universel)**

L'heure de naissance enregistrée à l'état civil est en heure légale française, qui est :
- En hiver : GMT + 1 heure
- En été (du dernier dimanche de mars au dernier dimanche d'octobre) : GMT + 2 heures

Exemple : naissance le 12 mai 2000 à 14h30 (heure légale française d'été). Heure GMT = 14h30 - 2h = **12h30 GMT**.

**Étape 2 — Calculer le Temps Sidéral à Greenwich à 0h GMT pour la date donnée**

Le Temps Sidéral à 0h GMT évolue d'environ 3 minutes et 56 secondes par jour solaire. Pour le 12 mai 2000 à 0h GMT, la valeur tabulée dans l'éphéméride astronomique est de **15h 12m 30s**.

**Étape 3 — Ajuster au Temps Sidéral à Greenwich à l'heure exacte de naissance**

On ajoute à cette valeur l'écart entre 0h GMT et l'heure GMT de naissance, augmenté d'une correction pour le rythme sidéral (1,002738 × heure solaire écoulée).

Pour notre exemple : 12h 30m × 1,002738 = 12h 32m 03s
Temps Sidéral à Greenwich à 12h30 GMT le 12 mai 2000 : 15h 12m 30s + 12h 32m 03s = **27h 44m 33s** = **3h 44m 33s** (modulo 24h).

**Étape 4 — Ajuster au Temps Sidéral Local en fonction de la longitude du lieu**

La longitude doit être convertie en temps : 15° de longitude équivalent à 1 heure. Pour une naissance à Paris (longitude 2°20'E), l'ajustement est de +2°20'/15° = +9 min 20s.

Temps Sidéral Local : 3h 44m 33s + 9m 20s = **3h 53m 53s**.

## Étape finale : lecture de la table des ascendants

Une fois le TSL calculé, on consulte une table des ascendants spécifique à la latitude du lieu de naissance. Cette table associe chaque créneau de TSL à un degré précis du zodiaque qui se lève à l'horizon est à cet instant.

Pour la latitude de Paris (48°51'N), un TSL de 3h 53m correspond à un ascendant aux environs du **20ème degré du signe du Cancer**.

La précision de l'ascendant est de l'ordre du degré (soit 4 minutes de TSL), ce qui exige une connaissance de l'heure de naissance à environ 4 minutes près. C'est pourquoi les astrologues attachent une importance particulière à l'heure consignée à l'état civil (mais souvent arrondie de manière approximative par l'officier d'état civil).

## Les ascendants par latitude : une variation importante

À l'équateur, chaque signe occupe exactement 2 heures de TSL : la durée de défilement est uniforme. Plus on monte vers les hautes latitudes, plus la variation augmente :

- À Paris (48°51'N) : le signe du Cancer prend 1h 40min, celui du Capricorne prend 2h 20min
- À Helsinki (60°10'N) : le Cancer ne prend que 1h 10min, le Capricorne prend 2h 50min
- Au-delà de 66°33'N (cercle polaire arctique) : certains signes ne se lèvent jamais à certaines périodes de l'année

Cette inégalité de défilement explique pourquoi certains signes (Cancer, Lion, Vierge, Balance) sont surreprésentés comme ascendants chez les natifs des hautes latitudes, et d'autres (Capricorne, Verseau, Poissons) sous-représentés.

## Cas pratique : ascendant pour une naissance à Marseille

Soit une naissance le 15 août 2000 à 06h00 (heure légale française, été) à Marseille (latitude 43°18'N, longitude 5°22'E).

- Heure GMT : 06h00 - 2h = 04h00 GMT
- Temps Sidéral à Greenwich à 0h GMT le 15/08/2000 : 21h 35m 21s (éphéméride)
- TSG à 04h GMT : 21h 35m 21s + (4h × 1,002738) = 21h 35m 21s + 4h 0m 39s = **25h 36m 00s** = 1h 36m 00s
- Correction longitude (5°22'E = +21min 28s) : 1h 36m 00s + 21m 28s = **1h 57m 28s**

Table des ascendants pour latitude 43°N à TSL ≈ 1h 57m : ascendant aux environs du **15ème degré des Gémeaux**.

Pour [explorer le barème complet et générer un ascendant précis automatiquement à partir de votre date et lieu de naissance](https://macalculatriceenligne.com/astrologie/calcul-ascendant-homme/), il suffit de renseigner la date, l'heure exacte (avec correction décalage horaire en cas de naissance à l'étranger), et le lieu de naissance pour obtenir le degré zodiacal précis et l'interprétation symbolique associée.

## Sources

Bureau des Longitudes (France), éphémérides astronomiques annuelles 1900-2050 ; Astronomical Almanac (Royaume-Uni-États-Unis), tables de Temps Sidéral ; Tables of Houses by Hugh Rice (1922, méthode Placidus historique) ; *L'astronomie dans la Bible et la mythologie*, Camille Flammarion (1880, méthode historique pré-informatique) ; documentation IAU (International Astronomical Union) sur la conversion entre temps universel coordonné et temps sidéral.

— Claire Dubois


## Pages détaillées

- [Ascendant astrologique homme](https://macalculatriceenligne.com/astrologie/calcul-ascendant-homme/) *(auteur : Claire)*
- [Calcul âge](https://macalculatriceenligne.com/temps-heures/calcul-age-entre-deux-dates/) *(auteur : Mehdi)*
- [DPE coef 2026 1,9](https://macalculatriceenligne.com/construction/calcul-dpe-estimation-2026/) *(auteur : Thomas)*
- [Puissance triphasée](https://macalculatriceenligne.com/electricite/calculateur-puissance-triphase/) *(auteur : Thomas)*

[← Retour à l'index](../index.md)