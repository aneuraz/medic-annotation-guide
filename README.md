# Guide d'annotation pour l'extraction des informations sur les médicaments v2.0

[Lien vers le guide originale (en anglais)](https://equipe22.github.io/medExtAnnotation/)

Les remarques/corrections sont en <span style="color:red">rouge</span>.

![test-image](examples/image-test.jpeg)
<img src="examples/image-test.jpeg" width="100px">

# Vue d'ensemble :

Ce guide a pour but d'aider et de fournir une méthode standard d'annotation pour l'extraction de médicaments des dossiers médicaux électroniques français. Il est fortement inspiré des directives d'annotation préliminaires du défi i2b2 Medication Extraction Challenge de 2009. Pour chaque rapport de patient fourni, l'objectif est d'extraire des informations sur tous les médicaments qui sont connus pour être pris par le patient ou liés à lui. Certains des médicaments sont fournis sous forme semi-structurée (liste), par exemple, des sections intitulées "médicaments à l'admission" ou "médicaments à la sortie". La sortie finale des médicaments du patient devrait inclure ces derniers ; cependant, le véritable intérêt est d'extraire les médicaments qui sont mentionnés dans les récits des dossiers.


Les données d'entrée pour l'annotation des médicaments seront les résumés de sortie des dossiers médicaux électroniques, pré annotés par un système à base de règles et des modèles de Machine Learning. L'annotation sera faite avec "metanno". Ces données sont en texte libre.

La sortie créée à partir de ces annotations sera une liste de médicaments et leurs informations. Pour chaque médicament listé, les informations suivantes doivent être annotées si elles sont manquantes ou corrigées si elles sont fausses dans la pré annotation :

1. [Médicament ou Classe de médicament](# Médicament ou Classe de Médicaments) (`DRUG`, `CLASS`)
2. Dose : (`DOSE`)
3. Fréquence : (`FREQ`)
4. Duré : (`DURATION`)
5. Voie d'admission : (`ROUTE`)
6. Condition : (`CONDITION`)
7. Événement : (`EVENT`)
8. Prescriptions : (`DRUGBLOB`, `ORDOBLOB`)
9. Attributs


Chaque entité peut être annotée par des marqueurs d'attributs. 

1. Les entités Durée et Évènement seront marquées par un attribut temporel (passé, présent, futur). S'il est absent, "présent" sera considéré par défaut comme attribut temporel.
2. Toutes les entités seront marquées par un attribut de certitude (factuel, suggéré, conditionnel, incertain, nié, contre-indiqué).  S'il est absent, "factuel" sera considéré par défaut comme l'attribut de certitude.
3. Pour tous les médicaments listés, le patient sera considéré par défaut comme expérimentateur. Si ce n'est pas le cas, préciser : famille si cela concerne la famille du patient, "autre" sinon (avis, posologie...).


Les annotations doivent être faites même s'il y a des fautes d'orthographe, sauf si ces fautes d'orthographe peuvent induire une confusion.

Dans ce document, les accents et certains signes de ponctuation ont été supprimés des phrases d'exemple françaises pour être en accord avec notre première étude sur l'extraction d'informations sur les médicaments.

# Médicament ou Classe de Médicaments

Tous les médicaments énumérés dans le résumé de décharge et donnés (présents, passés ou futurs) ou contre-indiqués à un expérimentateur.

## Que faut-il annoter ?

<span style="color:red"> revoir tout cela (class dans medoc)</span>

Nom du médicament, génériques, classe de médicaments ou de substances

Pour annoter des médicaments, le texte doit inclure une déclaration explicite indiquant que le patient a pris ce médicament, qu'il le prend actuellement, qu'il se voit prescrire ce médicament, qu'il lui est suggéré de le prendre, qu'il a eu des effets secondaires en le prenant ou qu'il ne peut pas le prendre en raison d'une contre-indication.

### Médicament (drug)

### Inclut :

- Les substances sur ordonnance :
  - Les médicaments de marque, par exemple, *doliprane*.
  - Génériques, par ex., *paracétamol*.
  - Ingrédients, par exemple, *furosémide*.
  - Nom collectif d'un groupe de médicaments, par exemple *corticoïdes* (il sera annoté comme une classe de médicaments).<span style="color:red"> genre là</span>
- Médicaments en vente libre :
  - Noms de marque, par exemple, *Aspirine*.
  - Ingrédients, par exemple, *vitamine D*.
  - Noms collectifs pour un groupe de médicaments, par exemple *vitamines* (ils seront annotés comme une classe de médicaments).<span style="color:red"> ou là</span>

- Substances biologiques requises ou suggérées par les médecins
  - Ingrédients de la nutrition parentérale totale s'ils sont énumérés individuellement
  - Composants des liquides IV et des solutions salines énumérés (y compris l'"eau minérale" et le "sérum physiologique")
  - Débit glucidique
- Thérapie par substance, par exemple, *Corticothérapie* ou *traitement antirétroviral* (elle sera annotée comme une classe de médicaments). <span style="color:red"> et là</span>


### Exclut :

- Nourriture et eau non utilisées comme traitement
- Régime alimentaire
- Tabac
- Alcool
- Médicaments illicites
- dispositif médical, par exemple, Pompe à Insuline (même si un médicament s'y trouve)
- transfusion

### Classe de médicaments (class)

### Inclut :

- "therapie"
  - oxygénothérapie
  - corticothérapie
  - antibiothérapie
  - antibiotique
- traitement anti-" avec ou sans "traitement" avant
  - traitement antalgique
  - traitement antirétroviral
  - antihypertenseur
  - vaccination antigrippale
  - antifongique
- protocole de chimiothérapie
  - abvd
- autres
  - vaccination contre l'hépatite b/méningocoque...
  - nutrition (si utilisée comme traitement)
    - "oral/parentérale/entérale"
      - inclure le nom du médicament
      - sauf s'il y a une distinction entre les deux "nutrition orale mais pas entérale".
    - régime d'urgence
  - complément nutritionnel
- acronyme
  - np (pour nutrition parentérale)
  - avk (pour anti-vitamine K)
  - o2
  - vit-d

### Exclut :

- "traitement" sans précision
  - traitement prophylactique
  - traitement pour son asthme

- action suivie par "par"
  - sedation
  - (re)hydratation
  - antibioprophylaxie

- classe incluse dans le dispositif médical



## Comment annoter ?

Annotez la phrase nominale complète qui correspond au nom du médicament, par exemple, amoxicilline acide clavulanique. L'annotation doit être faite même s'il y a des fautes d'orthographe. Ne pas inclure des mots tels que "injectable", "crème", "nébuliseur", "solution" comme faisant partie du nom du médicament même s'ils apparaissent immédiatement après le nom du médicament, par exemple, sélénium injectable, xylocaïne nébuliseur. N'incluez pas d'informations numériques dans le nom du médicament, p. ex. renutril 500, à moins qu'il ne s'agisse d'un type de substance, p. ex. iodure 131.

Les pronoms qui font référence à un médicament ne doivent pas être inclus, mais leurs attributs sont liés à l'élément auquel ils font référence.



### Attributs

- Certitude (**certainty**) : Pour les médicaments suggérés ou incertains, un attribut de certitude **"suggested"**/**"uncertain"** doit être ajouté. Pour les médicaments non pris ou non donnés, un attribut de certitude **"negated"** doit être ajouté (la relation sur un médicament négativé peut être annotée, par exemple, la relation entre avk et durée doit être annotée pour "pas d'avk pendant 2 jours"). Pour les médicaments mentionnés comme contre-indication, la certitude **"counterindication"** doit être annotée.

- Expérimentateur (**experiencer**) : Si la médication concerne d'autres personnes, elle doit être annotée avec un attribut d'expérimentateur (**"family"**/**"other"**).



### Examples :

- *amlor : 10 mg le matin*
  - drug : *amlor*
- *ialuset plus creme*
  - drug : *ialuset plus*
  - (creme not included in drug name) <span style="color:red">a traduire</span>.
- *sevrage de l oxygenotherapie en fevrier 2013*
  - class : *oxygenotherapie*
- *grand-mere maternelle : diabete de type 2 a 61 ans, sans surpoids, traitee par insuline*
  - drug : *insuline*
    - experiencer : family

Chaque référence conjointe d'un médicament (nom de classe ou de médicament) ou de ses génériques, y compris avec des fautes d'orthographe, dans la même phrase, doit être annotée.

- *doliprane 1 dose poids\*4/ jour si douleurs (paracetamol 1 boite)*
  - drug : *doliprane*
  - drug : *paracetamol*
- *(matin : 9-12 ui novorapid(1), 20 ui levemir(1), dans les 4 zones.*
*(gouter : 5-7 ui novorapid(2).*
*(soir : 6-8 ui novorapid(3), 15 ui levemir(2), dans les 4 zones.*
  - drug : *novorapid(1)*
  - drug : *levemir(1)*
  - drug : *novorapid(2)*
  - drug : *novorapid(3)*
  - drug : *levemir(2)*

Si un médicament est écrit comme médicament et comme classe dans la même phrase, annoter les deux (comme classe et comme médicament). L'association d'un médicament à une classe dans la même phrase entraîne une annotation "drug" par médicament :

- *relais par avk au cours de l'hospitalisation (coumadine)*
  - drug : *coumadine*
  - class : *avk*
- *un traitement antiretroviral a ete debute (truvada, reyataz et norvir avec une charge virale…)*
  - class : *traitement antiretroviral*
  - drug : *truvada*
  - drug : *reyataz*
  - drug : *norvir*

L'énumération des médicaments partageant un mot doit être annotée ensemble :

- *vitamine C , D , A , and E*
  - drug : *vitamine C , D , A , and E*

Mais :

- *une dose de vitamine C et vitamine D*
  - drug : *vitamine C*
  - drug : *vitamine D*

Annoter le nom des médicaments même si leurs attributs sont niés

- *pas de necessite de doublement des doses d hydrocortisone*
  - drug : *hydrocortisone*

# Dosage (dose)

La quantité d'un seul médicament utilisé dans chaque administration, par exemple *un comprimé, une dose, 30 mg*.

## Que faut-il annoter ?

Les informations numériques et/ou textuelles qui marquent la quantité et l'unité d'administration d'un médicament utilisé dans une seule administration. Annoter la relation avec le médicament concerné par un lien du dosage au nom du médicament.

### Inclut (liste non exhaustive) :

- 1 cp
- un comprimé
- 0.4 mg
- 0.5 m.g.
- 100 mg/kg
- une dose kilo
- 100 mg x 2 comprimés
- 500 mg : 2 gelules
- 4000 ui / 0,4
- 1 sachet
- 2 cuillère-mesure
- deux bouffées
- 3 bolus
- double dose
- [renutril] 500

### Exclut :

- si la dose est niée et que le médicament est donné, par exemple :
  - n'annotez pas "doublement des doses" pour "pas de necessite de doublement des doses d hydrocortisone"

- Dosages cumulés (car trop de variabilité dans la signification) :
  - 3 boites
  - *doliprane 1 dose poids\*4/ jour si douleurs (paracetamol 1 boite)*
    - dose : *1 dose poids*
    - ne pas annoter : *1 boite*

## Commnent annoter ?

Annotez tous les dosages mentionnés de tous les médicaments présents dans le résumé de sortie et leur relation avec celui-ci, même s'il fait partie du nom du médicament.

- *speciafoldine 5mg 10 jours par mois*
  - dose : *5mg*
- *oracilline 500mui x 2 par jour.*
  - dose : *500mui*
- *depakine 500 x 3 par jour*
  - dose : *500*
- *vitabact 0,05 % : x4/jour dans chaque oeil pendant 10 jours*
  - dose: *0,05 %*

Annotez tous les dosages partiels.

- *hydrea 500mg un jour sur 2, 1000mg un jour sur 2*.
  - dose : *500mg*
  - dose : *1000mg*
- *hydrocortisone : 7,5 mg le matin, 5 mg le soir (12,5 mg/m²/jour )*
  - dose : *7,5 mg*
  - dose : *5 mg*
  - dose : *12,5mg/m²*

Annotez les différentes façons de se référer au même dosage dans des entrées séparées :

- *sandostatine : 100µg/8h en sc soit 50µg/kg/j*
  - dose : *100µg*
  - dose : *50µg/kg*

Annoter la partie immédiatement adjacente d'un dosage dans une entrée distincte :

- *seretide 50 deux bouffeesx2/j*
  - dose : *50 deux bouffees*
- *singulair 1 sachet de 4mg/jour,*
  - dose : *1 sachet de 4mg*

Annotez une gamme de dosage comme une seule entrée. Dans cet exemple, il y a plusieurs dosages pour le même médicament mais dans des phrases différentes :

- *matin : 5 a 8 ui novorapid.*
*midi : 5 a 8 ui novorapid.*
*gouter : 3 a 4 1/2 ui novorapid.*
*soir : 3 a 6 ui novorapid.*
  - dose :  *5 a 8 ui*
  - dose :  *5 a 8 ui*
  - dose :  *3 a 4 1/2 ui*
  - dose :  *3 a 6 ui*

Annotez un seul motifs (prescription d'ordonnance) pour tous les médicaments lorsque la posologie concerne les deux :

- *doliprane et ibuprofene, 1 comprime toutes les 6 heures chacun*
  - ordo_blob : *doliprane et ibuprofene, 1 comprime*
    - drug : *doliprane*
    - drug : *ibuprofene*
    - dose : *1 comprime*

# Fréquence (freq)

Termes, phrases ou abréviations qui décrivent la fréquence à laquelle chaque dose du médicament doit être prise.

## Que faut-il annoter ?

Toute expression qui indique la fréquence d'administration d'une dose unique d'un médicament doit être annotée.

### Inclut :

- Les fréquences :
  - par jour
  - toutes les 8 heures
  - toutes les semaine
  - /jour
  - /j
  - /24 heures
  -  par mois
  - tous les soirs
  - x 3 par jour
  - jour (si précéder d'un dosage)
  - 1 - 1 - 1
  - 850 - 1000 - 1000
  - 19/03, 25/03 et 01/04/2016
  - a J3, J5 et J7

- Les phrases temporelles qui précisent quand un médicament doit être pris (il s'agit généralement de phrases prépositionnelles et la préposition doit être incluse dans l'information extraite) :

  - quotidiennement
  - quotidien
  - mensuel
  - après/avant manger
  - a 4 heures
  - avant chaque repas

## Comment annoter ?

Appliquez les mêmes principes de base que pour le balisage de la dose. Annotez chaque fréquence, même si elle est répétée dans la même phrase.

- *doliprane 1 dose poids\*4/ jour si douleurs*
  - freq : *\*4/ jour*
- *sectral : 48 mg matin et soir*
  - freq : *matin et soir*

Annoter la partie immédiatement adjacente d'une fréquence comme une seule entrée :
- *singulair a chaque bolus : 15g a 7h et 16h30*
  - freq : *a 7h et 16h30*

Si la fréquence est segmentée et concerne la même entité, annoter la partie la plus informative :

- *speciafoldine : 1 comprime par jour, 10 jours par mois.*
  - freq : *10 jours par mois*

# Durée (duree)

Une expression de temps écoulé qui indique pendant combien de temps le médicament doit être administré. Ces expressions sont souvent des syntagmes nominaux, des syntagmes prépositionnels ou des clauses.

## Qu'est-ce qui doit être annoté ?

Expressions qui décrivent la durée totale pendant laquelle le médicament doit être pris à une dose donnée. Dans le cas de médicaments qui sont arrêtés, la durée indique pendant combien de temps le médicament a été arrêté.

### Inclut :

- Des expressions de durée :

  - [pendant] 10 jours
  - [pour] un mois
  - [durant] 2 semaines
  - tant que nécessaire
  - [sur] 3h

### Exclut :

- Les expressions temporelles qui indiquent quand chaque dose doit être prise. Incluez-les dans la rubrique fréquence.

  - *a prendre pendant une activité physique*
    - *pendant une activite physique* n'est pas une durée

- Expression temporelle du début ou de l'arrêt d'un médicament :

  - dans 10 jours
  - depuis 10 jours

- Dosages cumulés (car trop de variabilité dans la signification) :

  - 3 boites

## Comment annoter ?

Suivez les mêmes principes de base que pour l'annotation de la fréquence. N'incluez pas les prépositions complètes.

### Attribut

- Temporalité (**temporal_marker**) : Par défaut, **"present"** est l'attribut temporel des durées. Il peut être **"past"**, **"present"** ou **"future"**. Il doit être défini selon que la durée se situe avant, pendant ou après l'hospitalisation en cours.

### Exemples

- *vitabact 0,05 % : x4/jour dans chaque oeil pendant 10 jours*
  - duree : *10 jours*
    - temporal_marker : present (by default)
- *ciflox 500 mg par 24 heures pour une duree totale de 3 semaines*
  - duree : *3 semaines*
    - temporal_marker : present (by default)
-  *a donc beneficie de sa 2ieme perfusion de remicade (200mg) sur 3h*
  - duree : *3h*
    - temporal_marker : present (by default)
- *a ete traite pendant 2 ans par remicade*
  - duree : *2 ans*
    - temporal_marker : past

# Mode d'administration (route)

Décrit la méthode d'administration du médicament.

## Qu'est-ce qui doit être annoté ?

Le texte qui exprime le mode/voie d'administration, même s'il est exprimé dans le cadre du nom du médicament ou de la posologie.

### Inclut :

- per os
- intraveineux ou iv
- topique
- sublingual
- cutanee
- sous cutanée
- intramusculaire
- perfusion
- creme
- solution buvable
- ophtalmique
- Abréviations de ce qui précède


## Comment annoter ?

Suivez les mêmes principes de base que pour l'annotation de la durée. Plusieurs routes peuvent être liées à un seul nom de médicament

- *spray de ventoline : 2 bouffees x4 par jour pendant 4 jours au babyhaler.*
  - route : *spray*
  - route : *babyhaler*
- *hyperhydratation par voie intraveineuse*
  - route : *intraveineuse*
- *necessitant un traitement par kayexalate et aerosol de ventoline*
  - route : *aerosol*

Les changements dans le mode d'administration d'un médicament doivent être inclus dans des entrées séparées.

- *traitement pendant 5 jours par clamoxyl iv puis relais per os*
  - route : *iv*
  - route : *per os*

Les différentes façons de désigner le même mode d'administration doivent être incluses dans des entrées séparées.

- *nebulisation de ventoline toutes les 6 heures puis relais par chambre d inhalation (baby-haler) le 06/02/2012*
  - route : *nebulisation*
  - route : *chambre d inhalation*
  - route : *baby-haler*

Les cas où un mode s'applique à plusieurs médicaments doivent être traités correctement.


- *relais par targocid puis orbenine iv jusqu au 24/03/2013*
  - route : *iv*

# Condition (condition)

Expressions qui indiquent la condition pour laquelle le médicament doit être administré. Ces expressions sont souvent des propositions conditionnelles et commencent par une expression conditionnelle telle que "si", "en cas de", "en fonction de"....


## Qu'est-ce qui doit être annoté ?

Condition pour laquelle le médicament doit être administré.

### Inclut (liste non exhaustive) :

- en cas de fievre
- si besoin
- si veut
- en fonction des ASAT

## Comment annoter ?


Annotez toujours la phrase adjectivale de base la plus informative ou la phrase nominale de base la plus longue comme condition du médicament. N'incluez pas les phrases complexes, n'incluez pas les phrases coordonnées. Au lieu de cela, extrayez de ces phrases la phrase de base, même si cela signifie que vous vous retrouverez avec plusieurs conditions. Une condition peut être liée à un nom de médicament ou à un événement.

Un attribut de certitude "conditionnel" ne doit pas être ajouté sur l'entité concernée par la condition.

<span style="color:red">pas prendre les si/en cas de...</span>

<span style="color:red">ATTRIBUT certainty sur condition</span> 

- *codenfan une dose/poids si besoin maximum 3x par jour*
  - drug : *codenfan*
  - condition : *si besoin*

S'il y a différentes conditions mentionnées pour le même médicament alors inclure une entrée par condition. Ajoutez la relation avec l'entité pour chacune. Dans les cas où plusieurs médicaments sont donnés avec la même condition, indiquez la condition avec tous les médicaments et ajoutez la relation pour chacun.


- *il a ete explique aux parents d utiliser l oxygene en cas d inconfort, de paleur ou de gene respiratoire et non en fonction d un chiffre de saturation*
  - drug : *oxygene*
  - condition : *inconfort*
  - condition : *paleur*
  - condition : *gene respiratoire*
  - condition : *chiffre de saturation*
    - certainty : negated 

Si une condition est composée de plusieurs sous-conditions (séparées par "et"), annotez-les séparément avec plusieurs entrées.

- *melatonine 2mg : 1 gelule au coucher si agitation et probleme d endormissement*
  - condition : *agitation*
  - condition : *probleme d endormissement*

Les différentes façons de désigner la même condition pour les médicaments doivent être traitées comme des conditions distinctes.

- *en cas d'anemie aregenerative (hemolyse non mecanique) augmenter les corticoides*
  - condition : *anemie aregenerative* 
  - condition : *hemolyse non mecanique*
  - increase : *augmenter*

# Évènements

Information sur le fait que le médicament est commencé, arrêté, poursuivi, augmenté ou diminué à un moment défini. Cette information est généralement exprimée par le verbe principal de la phrase ou par une date. Annotez l'événement indiqué par le mot principal lié au médicament, ainsi que la date la plus précise.


## Qu'est-ce qui doit être annoté ?

Le mot principal (verbe, nom...) soulignant l'évènement tel que "mise en route", "début", "poursuite", "relais" ..., ainsi que la date la plus précise. Si le mot principal est séparé de la date dans la phrase, créer deux entités différentes.

## Comment annoter ?

Choisissez parmi les valeurs possibles :

- **"start"** : mot indiquant le début de la prise du médicament ou la date du début.
- **"stop"** : mot indiquant l'arrêt de la prise du médicament ou la date de l'arrêt.
- **"start-stop"** : mot indiquant la prise unique d'un médicament ou la date de la prise unique.
- **"increase"** : mot indiquant l'augmentation de la dose d'un médicament déjà pris, ou la date de l'augmentation.
- **"decrease"** : mot indiquant la diminution de la dose d'un médicament déjà pris, ou la date de la diminution.
- **"continue"** : mot indiquant la poursuite de prise d'un médicament sans changement clair de dosage, ou la date de la poursuite.
- **"switch"** : mot indiquant le changement de médicament/molécule, ou la date du changement.

### Attributs

- Temporalité (**temporal_marker**) : Par défaut, **"present"** est l'attribut temporel des événements. Il peut être **"past"**, **"present"** ou **"future"**. Il doit être défini selon que l'événements se situe avant, pendant ou après la rédaction du document.


S'il y a deux événements sur la même expression (même s'ils sont identiques, par exemple 2 événements de début), vous devez annoter l'expression deux fois avec un événement **"start"**.


## Exemples :

- *Pas de modification de la corticothérapie*
  - continue : *Pas de modification*
- *meningocoque a + c : 11/07.*
  - start-stop : *11/07*
    - temporal_marker : past
- *antibiotherapie debutee lors de la chirurgie, a arrete a j5*
  - start : *debutee lors de la chirurgie*
    - temporal_marker : past
  - stop : *a arrete a j5*
    - temporal_marker : past
- *arret du nubain le 14/12/2010*
  - stop : *arret*
  - stop : *14/12/2010*
    - temporal_marker : past
- *augmentation des doses de morphine*
  - increase : *augmentation*
    - temporal_marker : present (par défault)
- *poursuite de l'hydrea*
  - continue : *poursuite*
- *janvier 2006 : nouveau syndrome thoracique aigu, mise sous hydrea.*
  - start : *janvier 2006*
    - temporal_marker : past
  - start : *mise sous*
- *compte-rendu d hospitalisation de jour du 27/12/2012 pour sa 16ieme perfusion de remicade*
  - start-stop : *27/12/2012*
    - temporal_marker : present

Le passage d'un médicament à un autre comprend deux événements sur la même expression. Un médicament est arrêté et un autre est commencé.

- *relais du traitement avk pour un traitement par heparine en sous cutanee dans la phase aigue*
  - switch : *relais*
    - temporal_marker : present


S'il y a deux événements pour une entité, annotez deux entrées distinctes : elles seront dans le même **"drug_blob"** (voir la partie "Prescription"). Faites en sorte que chaque entrée soit aussi spécifique et complète que possible.

- *debut du traitement par ambisome le 29 mars 2014 a 3 mg/kg jusqu au 2 avril puis 5 mg/kg jusqu au 7 avril, puis 7,5 mg/kg jusqu au 30 avril*

  - start : *debut*
  - start : *29 mars 2014*
  - increase : *jusqu au 2 avril*
  - increase : *jusqu au 7 avril*
  - stop : *jusqu au 30 avril*

<span style="color:red">increase./decrease explicite</span>

<span style="color:red"> mettre des dose/durée sur l'exemple</span>

- *la pancytopenie s est compliquee apres la chimiotherapie d un sepsis a escherichia coli resistant a la tazocilline (tazocilline\* depuis le 6 septembre 2010) traite par fortum a partir du 15 septembre 2010*
  - drugblob : *tazocilline\* depuis le 6 septembre 2010*
    - drug : tazocilline
    - start : *depuis le 6 septembre 2010*
      - temporal_marker : past
  - drugblob : *fortum a partir du 15 septembre 2010*
    - drug : *fortum*
    - switch : *a partir du 15 septembre 2010*
      - temporal_marker : past

S'il existe plusieurs médicaments pour un même événement, annotez le normalement : il sera intégrer dans **ordo_blob** (voir la partie "Prescription")


- *traitement par endoxan avant de debuter un traitement par mabthera fludarabine endoxan etant donne la lymphocytose majeure et la presence d anemie hemolytique*
  - drug : *endoxan*
  - ordoblob : *debuter un traitement par mabthera fludarabine endoxan*
    - start : *debuter*
      - temporal_marker : future
    - drug : *mabthera*
    - drug : *fludarabine*
    - drug : *endoxan*

# Prescription (drug_blob/ordo_blob)


Les prescriptions sont des groupements d'annotations. Il existe deux types de prescription : la presciption de médicaments (**"drug_blob"**) et la prescription d'ordonnance (**"ordo_blob"**).
  - Un **drug_blob** correspond à un et un seul médicaments ainsi que ses attributs. Si le même médicament est cité deux fois, il faut ses deux mentions dans le drugblob.

  - Un **ordo_blob** correspond à un regroupement de **drug_blob** et/ou de médicaments (**"drug"** ou **"class"**) isolés ayant un attribut commun.


##  Qu'est-ce qui doit être annoté ?

Dès qu'un médicament possède des attributs : il est automatique dans un **"drug_blob"** (si au moins un de ses attributs lui est spécifique) ou dans un **"ordo_blob"** (si au moins un de ses attributs est partagé avec d'autres médicaments).


## Comment annoter ?

Du premier mot (médicament, attributs ou événement) jusqu'au dernier. Le type d'événement doit être appliqué sur les modèles de prescription liés. Si un médicament n'a pas d'attributs, ne pas faire de prescription.

Examples :
- *AUGMENTIN 600mg toutes les 8h jusuq'au 2019-10-11 inclus. PARACETAMOL 250mg toutes les 6h de façon systématique pendant 48h puis en cas de douleurs pendant 7 jours*
  - drug_blob : *AUGMENTIN 600mg toutes les 8h jusuq'au 2019-10-11 inclus*
    - drug : *AUGMENTIN*
    - dose : *600mg*
    - frequence : *toutes les 8h*
    - end : *jusuq'au 2019-10-11 inclus*
  - drug_blob : *PARACETAMOL 250mg toutes les 6h de façon systématique pendant 48h puis en cas de douleurs pendant 7 jours*
    - drug : *PARACETAMOL*
    - dose : *250mg*
    - frequence : *toutes les 6h*
    - condition : *de façon systématique*
    - duree : *pendant 48h*
    - condition : *en cas de douleurs*
    - duree : *pendant 7 jours*

- *doliprane 1 dose poids\*4/ jour si douleurs (paracetamol 1 boite)*
  - drugblob : *doliprane 1 dose poids\*4/ jour si douleurs (paracetamol*
    - drug : *doliprane*
    - drug : *paracetamol*
    - dose : *1 dose poids*
    - frequency : *4/jour*
    - condition : *si douleurs*

Si un attribut ou un événement est lié à plusieurs médicaments, ne l'incluez que dans le motif **"ordo_blob"** qui inclura les médicaments.

Ici, "toujours" est lié à l'ordonnance :

- *je ne modifie pas son traitement, soit toujours lasilix 20 mg/j, atacand 8 mg, ezetrol , calciparat 1 g, allopurinol 300 mg et crestor 5.*
  - ordo_blob : *toujours lasilix 20 mg/j, atacand 8 mg, ezetrol , calciparat 1 g, allopurinol 300 mg et crestor 5*
    - continue : *toujours*
    - drug_blob : *lasilix 20 mg/j*
        - drug : *lasilix*
      - dose :  *20 mg*
      - frequency : */j*
    - drug_blob : *latacand 8 mg*
      - drug : *atacand*
      - dose :  *8 mg*
    - drug_blob : *calciparat 1 g*
      - drug : *ezetrol*
      - drug : *calciparat*
      - dose :  *1 g*
    - drug_blob : *allopurinol 300 mg*
      - drug : *allopurinol*
      - dose :  *300 mg*
    - drug_blob: *crestor 5*
      - drug : *crestor*
      - dose : *5*


# Attributs: Rappels

Informations qui indiquent quand les événements et les durées doivent avoir lieu et s'ils sont factuels, suggérés, conditionnels ou incertains.

## Attribut temporel (temporal_marker)

Informations indiquant si le médicament a été administré dans le passé, est administré actuellement ou sera administré dans le futur, dans la mesure où ces informations sont exprimées dans le temps des verbes et des verbes auxiliaires utilisés pour exprimer les événements. Un attribut temporel pour chaque événement.

### Comment marquer ?

Les événements et la durée peuvent être marqués.

Choisissez parmi les valeurs possibles pour chaque événement : passé, présent, futur. L'attribut temporel par défaut est "présent".

- Passé : L'événement s'est produit avant l'hospitalisation actuelle.
- Présent : L'événement s'est produit pendant l'hospitalisation actuelle.
- Futur : L'événement s'est produit après l'hospitalisation actuelle.

Voir Durée et Événements pour des exemples.

## Attributs de certitude

Informations permettant de savoir si l'événement se produit. La certitude peut être exprimée par des mots d'incertitude, par exemple, "suggéré", ou par des modaux, par exemple, "devrait" indique une suggestion.

#### Comment marquer ?

Choisissez parmi les valeurs possibles : conditionnel, suggestion, factuel, incertain ou négation. L'attribut Certainty par défaut est "factuel".

- ~~Conditionnel : L'entité ne se produit que dans certaines conditions mentionnées dans le texte.~~
- Suggestion : L'entité est/était suggérée et ne dépend pas d'une condition.
- Factuel : L'entité n'est pas marquée comme conditionnelle ou suggérée, elle est factuelle. C'est la valeur par défaut de la certitude.
- Incertain : L'entité se produit/ne s'est pas produite de façon certaine.
- Négatif : L'entité se produit/ne se produit pas, par exemple, un médicament n'est pas administré.
- Contre-indiqué : le médicament est mentionné comme une contre-indication.

Voir Medicaments pour des exemples.


[^1]: Pontus Stenetorp, Sampo Pyysalo, Goran Topić, Tomoko Ohta, Sophia Ananiadou and Jun'ichi Tsujii (2012). brat: a Web-based Tool for NLP-Assisted Text Annotation. In _Proceedings of the Demonstrations Session at EACL 2012_.
