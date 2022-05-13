# Remarques Guide d'annotation Pymedext

[Lien](https://equipe22.github.io/medExtAnnotation/)

Les remarques sont en <span style="color:red">rouge</span>.

# Overview :

This Guideline is done to help and give standard method of annotation for medication extraction from french electronic health records. It's strongly inspired from Preliminary Annotation Guidelines of the i2b2 Medication Extraction Challenge of 2009. For each patient report provided, the goal is to extract information about all of the medications that are known to be taken by the patient or related with him. Some of the medications are provided in semi-structured (list) form, e.g., sections labeled as "medications on admission" or "medications at discharge". The final output of medications of the patient should include these; however, the real interest is in extracting medications that are mentioned in the narratives of records.

The input of the medication annotation will be discharges summaries of Electronics Health records,  pre annotated by a rule based system <span style="color:red">and machine learning models</span>. The annotation will be done with "brat"[^1] <span style="color:red">or metanno ?</span>. Those discharges are free text.

The output created from these annotations will be a list of medications and their informations. For each listed medication, the following information needs to be annotated if missing or corrected if false in the pre annotation :


1. medication name (or drug class)
2. dosage
3. frequency
4. duration
5. mode/route of administration
6. condition
7. event
8. attributes
9. prescription patterns

<span style="color:red">

- `entity_tag` : `BIO` format (Begin, In, Out) with 7 classes :
    - CLASS
    - CONDITION
    - DOSE
    - DRUG
    - DURATION
    - FREQ
    - ROUTE
- `event_tag` :
    - CONTINUE
    - DECREASE
    - INCREASE
    - START
    - START_STOP
    - STOP
    - SWITCH
- `ordoblob_tag`
- `drugblob_tag`

</span>

Each entity can be annotate by attribute markers <span style="color:red"> We really do it  ?</span> :

1. Duration and Event entity will be marked by temporal attribute(past, present, futur). If it's missing, "present" will be considered by default as temporal attribute.
2. All of the entity will be marked by certainty attribute (factual, suggested, conditional, uncertain, negated, contraindicated).  If it's missing, "factual" will be considered by default as certainty attribute.
3. For all of the listed medications, the patient will be considered as experiencer by default. If it's not the case, precise it : family if it concerns patients family, "other" otherwise (notice, dosage…)

Annotations must be done even if there are spelling mistakes, unless those spelling mistakes could induce confusion. <span style="color:red">vague ?</span>

In this document, accents and some punctuation signs have been removed from french example sentences to agree with our first study on extraction of medication informations.

# Medication:

All Medications listed in discharge summary and given (present, past or future) or contraindicated to an experiencer.

## What should be annotated?

Drug name, generics, class of medication or substance

Medications include:

- Prescription substances:
  - Brand name medications, e.g., *doliprane*
  - Generics , e.g.,* paracetamol*
  - Ingredients, e.g., *furosemide*
  - Collective name for a group of medications, e.g., *corticoïdes* (it will be annotated as a drug class)
- Over the counter medications:
  - Brand names, e.g., *Aspirine*
  - Ingredients, e.g., *vitamine D*
  - Collective names for a group of medications, e.g, *vitamines* (it will be annotated as a drug class)
- Biological substances required or suggested by doctors
  - Ingredients in total parenteral nutrition if listed individually
  - Components of IV fluid and saline listed (including "eau minerale" and "serum physiologique")
  - Debit glucidique
- Substance therapy , e.g., *Corticothérapie* or *traitement antiretroviral* (it will be annotated as a drug class)

<span style="color:red"> revoir la liste ci-dessous ?</span>

Medications exclude:

- Food and Water not used as treatment
- Diet
- Tobacco
- Alcohol
- Illicit drugs
- medical device , e.g., Pompe à Insuline (even if a drug is in)
- transfusion

Class include :

- "therapie"
  - oxygenotherapie
  - corticotherapie
  - antibiotherapie
  - antibiotique
- "traitement anti-" with or without "traitement" before
  - traitement antalgique
  - traitement antiretroviral
  - antihypertenseur
  - vaccination antigrippal
  - antifongique
- chemotherapy protocol
  - abvd
- others
  - vaccination contre l'hepatite b/meningocoque...
  - nutrition (if used as treatment)
    - "orale/parentérale/entérale"
      - include medication name
      - unless there is a distinction between them "nutrition orale mais pas enterale"
    - regime d'urgence
  - complement nutritionnel
- acronyme
  - np (for nutrition parenterale)
  - avk (for anti-vitamine K)
  - o2
  - vit-d

Class exclude:

"traitement" without precision

- traitement prophylactique
- traitement pour son asthme

action followed by "par"

- sedation
- (re)hydratation
- antibioprophylaxie

<span style="color:red">?</span> class included in medical device


To annotate medications, the text has to include an explicit statement indicating that the patient either took this medication, is taking the medication, is prescribed the medication, is suggested to take the medication, had side effect taking the medication or can't take it because of contraindication.

<span style="color:red">idem que markers au précédemment cité :</span>

For medication suggested or uncertain, a certainty attribute"suggested"/"uncertain" must be add. For medication not taken or not given, a certainty attribute "negated" must be add (relation on a negated drug can be annotate, e.g., relation between avk and duration must be annotate for "pas d'avk pendant 2 jours"). For medication mentioned as a contraindication, a certainty "contraindicated" must be annotate. If medications concerned other people, it must be annotated with an experiencer attribute ("family"/"other").

## How to annotate ?

Annotate the complete noun phrase that correspond to the name of the medication, e.g., amoxicilline acide clavulanique. Annotation must be done even if there are spelling mistakes. Don't Include words such as "injectable", "creme", "nebuliseur", "solution" as part of the medication name even when they appear immediately after the medication name, e.g., selenium injectable, xylocaïne nebuliseur. Don't include numeric informations as part of the medication, e.g., renutril 500 unless it concerns a kind of the substance, e.g., iodure 131

Pronouns that refer to a drug, mustn't be included, but its attributes are related with the element referred.

### Examples :

- *amlor : 10 mg le matin*
  - drug : *amlor*
- *ialuset plus creme*
  - drug : *ialuset plus*
  - (creme not included in drug name)
- *sevrage de l oxygenotherapie en fevrier 2013*
  - class : *oxygenotherapie*
- *grand-mere maternelle : diabete de type 2 a 61 ans, sans surpoids, traitee par insuline*
  - drug : *insuline*
    - experiencer : family

Each co reference of a medication (class or drug name) or its generics, including with spelling <span style="color:red">m</span>istakes, in the same sentence, must be annotated.

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

If a drug is written as medication and a class in the same sentence, annotate both (as a class and as a drug). Medication association with class in the same sentence results of one annotation "drug" by medication :

- *relais par avk au cours de l'hospitalisation (coumadine)*
  - drug : *coumadine*
    - is\_part\_of : *avk*
  - class : *avk*
- *un traitement antiretroviral a ete debute (truvada, reyataz et norvir avec une charge virale…)*
  - class : *traitement antiretroviral*
  - drug : *truvada*
  - drug : *reyataz*
  - drug : *norvir*

Medication enumeration sharing a word must be annotated together :

- *vitamine C , D , A , and E*
  - drug : *vitamine C , D , A , and E*

But :

- *une dose de vitamine C et vitamine D*
  - drug : *vitamine C*
  - drug : *vitamine D*

Annotate drugs name even if their attributes are negated

- *pas de necessite de doublement des doses d hydrocortisone*
  - drug : *hydrocortisone*
  - don't annotate "doublement des doses"


# Dosage :

The amount of a single medication used in each administration, e.g., *un comprimé, une dose, 30 mg*.

## What should be annotated?

The numeric and/or the textual information that mark the amount and the unit of administration of a medication used in a single administration. Annotate relation with the drug concerned by a link from dosage to drug name

Includes (not exhaustive):

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

Exclude:

if dose is negated and drug is given, e.g.

- don't annotate "doublement des doses" for "pas de necessite de doublement des doses d hydrocortisone"

Cumulative dosages (because too much variability in the meaning):

- 3 boites

## How to annotate?

Annotate all mentioned dosages of all medications present in the discharge summary and their relation with it even if it is part of the medication name.

- *speciafoldine 5mg 10 jours par mois*
  - Dosage : *5mg*
- *oracilline 500mui x 2 par jour.*
  - Dosage : *500mui*
- *depakine 500 x 3 par jour*
  - Dosage : *500*
- *vitabact 0,05 % : x4/jour dans chaque oeil pendant 10 jours*
  - Dosage: *0,05 %*

Annotate all the partial dosage as "Dosage"

- *hydrea 500mg un jour sur 2, 1000mg un jour sur 2*.
  - Dosage : *500mg*
  - Dosage : *1000mg*
- *hydrocortisone : 7,5 mg le matin, 5 mg le soir (12,5 mg/m²/jour )*
  - Dosage : *7,5 mg*
  - Dosage : *5 mg*
  - Dosage : *12,5mg/m²*

Annotate different ways of referring to the same dosage in separate entries:

- *sandostatine : 100µg/8h en sc soit 50µg/kg/j*
  - Dosage : *100µg*
  - Dosage : *50µg/kg*

Annotate immediately adjacent part of a dosage in seperate entry :

- *seretide 50 deux bouffeesx2/j*
  - Dosage : *50 deux bouffees*
- *singulair 1 sachet de 4mg/jour,*
  - Dosage : *1 sachet de 4mg*

Annotate a range of dosage as one entry. In this example, there is multiple dosage for the same drug but in different sentence:

- *matin : 5 a 8 ui novorapid.*
*midi : 5 a 8 ui novorapid.*
*gouter : 3 a 4 1/2 ui novorapid.*
*soir : 3 a 6 ui novorapid.*
  - Dosage :  *5 a 8 ui*
  - Dosage :  *5 a 8 ui*
  - Dosage :  *3 a 4 1/2 ui*
  - Dosage :  *3 a 6 ui*

Annotate only one pattern (ordonnance prescription) for all drug when dosage concerned both :

- *doliprane et ibuprofene, 1 comprime toutes les 6 heures chacun*
  - Dosage : *1 comprime*
  - Ordonnance\_prescription\_start : *doliprane et ibuprofene, 1 comprime*

# Frequency

Terms, phrases, or abbreviations that describe how often each dose of the medication should be taken.

## What should be annotated?

Any expression that indicates the frequency of administering a single dose of a medication should be annotated.

Includes :

Frequency :

- par jour
- toutes les 8 heures
- toutes les semaine
- /jour
- /j
- /24 heures
-  par mois
- tous les soirs
- x 3 par jour
- jour (if preceded by dosage)
- 1 - 1 - 1
- 850 - 1000 - 1000
- 19/03, 25/03 et 01/04/2016
- a J3, J5 et J7

Temporal phrases which specify when a medication should be taken (These tend to be prepositional phrases. Preposition should be included in the extracted information):

- quotidiennement
- quotidien
- mensuel
- après/avant manger
- a 4 heures
- avant chaque repas

## How to annotate?

Apply the same basic principles that you use for tagging dose. Annotate each frequency even if repeated in the same sentence

- doliprane 1 dose poids\*4/ jour si douleurs
  - Frequency : \*4/ jour
- sectral : 48 mg matin et soir
  - Frequency : matin et soir

Annotate immediately adjacent part of a frequency as one entry :

- singulair a chaque bolus : 15g a 7h et 16h30
  - Frequency : a 7h et 16h30

If frequency is segmented and concerns same entity, annotate the most informative part :

- speciafoldine : 1 comprime par jour, 10 jours par mois.
  - Frequency : 10 jours par mois

# Duration

A elapsed time expression that indicate for how long the medication is to be administered. Such expressions are often noun phrases, prepositional phrases, or clauses.

## What should be annotated?

Expressions that describe the total time period for which the medication should be taken at a given dose. In case of medications that are stopped, the duration indicates for how long the medication has been stopped.

Includes:

Time expressions:

- [pendant] 10 jours
- [pour] un mois
- [durant] 2 semaines
- tant que nécessaire
- [sur] 3h

Excludes:

Time expressions that indicate when each dose should be taken. Include these under frequency.

- a prendre pendant une activité physique
  - "pendant une activite physique" is not a duration

Time expression of starting or stopping a medication :

- dans 10 jours
- depuis 10 jours

Cumulative dosages (because too much variability in the meaning):

- 3 boites

## How to annotate?

Follow the same basic principles as for annotating frequency. Don't include complete prepositional. Duration must be annotated with temporal attribute. if missing, "present" will be considered by default as temporal attribute.

- vitabact 0,05 % : x4/jour dans chaque oeil pendant 10 jours
  - Duration : 10 jours
    - temporality : present (by default)
- ciflox 500 mg par 24 heures pour une duree totale de 3 semaines
  - Duration : 3 semaines
    - temporality : present (by default)
-  a donc beneficie de sa 2ieme perfusion de remicade (200mg) sur 3h
  - Duration : 3h
    - temporality : present (by default)
- a ete traite pendant 2 ans par remicade
  - Duration : 2 ans
    - temporality : past

# Mode of administration

Describes the method for administering the medication.

## What should be annotated?

Text that expresses mode/route of administration, even when it is expressed as part of the medication name or the dosage.

Includes:

per os

- intraveineux
- topique
- sublingual
- cutanee
- sous cutanée
- intramusculaire
- perfusion
- creme
- solution buvable
- ophtalmique
- Abbreviations of the above

## How to annotate?

Follow the same basic principles as for annotating duration. If route apply to multiple medication, add a relation for each. Multiple route can be related to one drug name

- ventoline spray : 2 bouffees x4 par jour pendant 4 jours au babyhaler.
- hyperhydratation par voie intraveineuse
  - route : intraveineuse
- necessitant un traitement par kayexalate et aerosol de ventoline
  - route : aerosol

Changes in mode of administration of a drug should be included as separate entries.

- traitement pendant 5 jours par clamoxyl iv puis relais per os
  - route : iv
  - route : per os

Different ways of referring to the same mode of administration should be included in separate entries.

- nebulisation de ventoline toutes les 6 heures puis relais par chambre d inhalation (baby-haler) le 06/02/2012
  - route : nebulisation
  - route : chambre d inhalation
  - route : baby-haler

Cases where one mode applies to multiple medications need to be handled properly.

- relais par targocid puis orbenine iv jusqu au 24/03/2013
  - route : iv

# Condition

Expressions that indicate condition for which the medication is to be given. Such expressions are often conditional proposal and start with a conditional expression such as "si", "en cas de", "en fonction de"...

## What should be annotated ?

Condition for which the medication is to be given.

Includes:

- en cas de fievre
- si besoin
- si veut
- en fonction des ASAT

## How to annotate ?

Always annotate the most informative base adjective phrase or the longest base noun phrase as the condition for the medication. Longest base noun phrase has the form (det\* adj\* N+ adj\*). Longest adjective phrase often occurs as (adj+). Do not include complex phrases, do not include coordinated phrases. Instead, extract from these phrases the base phrase, even when this means you will end up with multiple conditions. A condition can be related to drug name or an event.

A certainty attribute"conditional" must not be add on the entity concerned by the condition.

- codenfan une dose/poids si besoin maximum 3x par jour
  - drug : codenfan
  - condition : besoin
    - is\_condition : codenfan

If there are different conditions mentioned for the same medication then include one entry per condition. Add relation with entity for each. In cases where multiple medications are given with the same condition, list the condition with all of the medications and add relation for each.

- il a ete explique aux parents d utiliser l oxygene en cas d inconfort, de paleur ou de gene respiratoire et non en fonction d un chiffre de saturation
  - drug : oxygene
  - condition : inconfort
  - condition : paleur
  - condition : gene respiratoire
  - condition : chiffre de saturation
    - certainty : negated

If a condition is composed of multiple sub-conditions (separated by "et"), annotate them together with one entry.

- melatonine 2mg : 1 gelule au coucher si agitation et probleme d endormissement
  - condition : si agitation et probleme d endormissement

Different ways of referring to the same condition for medication should be treated as separate conditions. Add relation "is\_equiv" between them and to the related entity from the closest one.

- en cas d'anemie aregenerative (hemolyse non mecanique) augmenter les corticoides
  - condition : anemie aregenerative
  - condition : hemolyse non mecanique
  - increase : augmenter
    - Arg : corticoides

# Events

Information on whether the medication is started, stopped, continued, increase or decrease at a defined time. This information is usually expressed in the main verb of the sentence or by a date. Annotate the event indicated by the most precise date, or, if not possible, by the main word related to the medication.

## What should be annotated?

A date of starting, stopping, continuing, increasing or decreasing a medication. If missing, annotate the main word (verb, noun…) highlighting the event such as "mise en route", "début", "poursuite", "relais"...

## Possible relations from an event

"Arg" of a medication : links the event to the related medication affected by the event. A medication could have multiple events related.

## How to annotate?

Choose from possible values: start, stop, continue, start-stop, increase or decrease.

- Start : main date or word of the medication beginning
- Stop : main date or word of the medication stop
- Start-Stop : unique intake of a medication
- Increase : increase of dosage of a medication already taken
- Decrease : decrease of dosage of a medication already taken
- Continue : main date or word of the medication pursuit or with no obvious change of dosage

By default, "present" is the temporal attribute of Events. It can be "past", "present" or "future". It must be defined according  to if the event is before, during or after current hospitalization.

If there are two Events on the same expression (even if both of them are the same, for example 2 start events), you should annotate the expression twice with an event "Start"

## Example :

- Pas de modification de la corticothérapie
  - continue : Pas de modification
    - Arg : corticothérapie
- meningocoque a + c : 11/07.
  - start-stop : 11/07
    - Arg : meningocoque a + c
    - Temporality : past
- antibiotherapie debutee lors de la chirurgie, a arrete a j5
  - start : debuter
    - Arg : antibiotherapie
  - stop : a j5
    - Arg : antibiotherapie
- arret du nubain le 14/12/2010
  - stop : 14/12/2010
    - Arg : nubain
    - Temporality : past
- augmentation des doses de morphine
  - increase : augmentation
    - No date, so main word is "augmentation"
    - Arg : morphine
    - Temporality : present (by default)
- poursuite de l'hydrea
  - continue : poursuite
    - Arg : hydrea
- janvier 2006 : nouveau syndrome thoracique aigu, mise sous hydrea.
  - start : janvier 2006
    - Arg : hydrea
    - Temporality : past
- compte-rendu d hospitalisation de jour du 27/12/2012 pour sa 16ieme perfusion de remicade
  - start-stop : 27/12/2012
    - Arg : remicade
    - Temporality : present

Switching one medication for another includes two events on the same expression. One medication is stopped and another one is started.

- relais du traitement avk pour un traitement par heparine en sous cutanee dans la phase aigue
  - stop : relais
    - Arg : avk
    - Temporality : present
  - start : relais
    - Arg : heparine
    - Temporality : present

If there are two events for an entity, include two separate entries. Make each entry as specific and complete as possible. If there are multiple medications for one event, include separate entries for each. Add a relation for each.

- la pancytopenie s est compliquee apres la chimiotherapie d un sepsis a escherichia coli resistant a la tazocilline (tazocilline\* depuis le 6 septembre 2010) traite par fortum a partir du 15 septembre 2010
  - start : 6 septembre 2010
    - Arg : tazocilline
    - Temporalité : present
  - stop : 15 septembre 2010
    - Arg : tazocilline
    - Temporalité : present
  - start : 15 septembre 2010
    - Arg : fortum
    - Temporalité : present

- traitement par endoxan avant de debuter un traitement par mabthera fludarabine endoxan etant donne la lymphocytose majeure et la presence d anemie hemolytique
  - start : debuter
    - Arg : mabthera
  - start : debuter
    - Arg : fludarabine
  - start : debuter
    - Arg : endoxan
- debut du traitement par ambisome le 29 mars 2014 a 3 mg/kg jusqu au 2 avril puis 5 mg/kg jusqu au 7 avril, puis 7,5 mg/kg jusqu au 30 avril
  - start : 29 mars 2014
    - Arg : ambisome
  - increase : 2 avril
    - Arg : ambisome
  - increase : 7 avril
    - Arg : ambisome
  - stop : 30 avril
    - Arg : ambisome

# Attributes

Information that indicates when events are to take place, and whether they are factual, suggested, conditional or uncertain

## Temporal Attributes

Information about whether the medication was administered in the past, is being administered currently, or will be administered in the future, to the extent that this information is expressed in the tense of the verbs and auxiliary verbs used to express events. One temporal attributes for each event.

### How to mark ?

Events and duration can be marked.

Choose from possible values for each event: past, present, future. The default temporal attributes is "present".

- Past: The event occurred before current hospitalization.
- Present: The event occurred during current hospitalization.
- Future: The event occurr after current hospitalization.

See Duration and Events for examples

## Certainty attributes

Information on whether the event occurs. Certainty can be expressed by uncertainty words, e.g., "suggested", or via modals, e.g., "should" indicates suggestion.

### How to mark?

Choose from possible values: conditional, suggestion, factual, uncertain or negated. The default Certainty attributes is "factual"

- Conditional: The entity occurs only under certain conditions as mentioned in the text.
- Suggestion: The entity  is/was suggested and it is not dependent on a condition.
- Factual: The entity is not marked as conditional or suggestion, it is factual. This is the default value for certainty.
- Uncertain : The entity does/did not occur for sure.
- Negated : The entity does/did not occur, e.g., a medication is not given
- Contraindicated : the medication is mentioned as a contraindication

See previous chapter for examples

# Prescription Pattern

All Medications and their attribute listed in discharge summary and given (present, past or future) or contraindicated to an experiencer.

## What should be annotated?

There is two kind of pattern : medication prescription (medication blob) <span style="color:red">(drugblob ?)</span> and ordonnance prescription (ordonnance blob). A drug (and its equivalent) and all of its attributes must be annoted such as medication prescription. A sentence with multiple drugs and their attribute must be annoted such as ordonnance prescription <span style="color:red">(drugblob ?)</span>. if an attributes is related to multiple drugs, it must not be in a medication prescription but only in ordonnance prescription.

## How to annotate ?

From the first word (medication, attributes or event) until the last. Event type must be applied on the Prescription patterns related. if a drug have no attributes, do not do a prescription pattern.

Examples :

- doliprane 1 dose poids\*4/ jour si douleurs (paracetamol 1 boite)
  - drug : doliprane
  - drug : paracetamol
  - dose : 1 dose poids
  - frequency : 4/jour
  - condition : douleurs
  - medication\_blob : doliprane 1 dose poids\*4/ jour si douleurs (paracetamol

if an attribute or event is related to multiple drugs, include it only in the "ordonnance\_blob" pattern which will include the drugs. Here "toujours" is related to the ordonnance.

- je ne modifie pas son traitement, soit toujours lasilix 20 mg/j, atacand 8 mg, ezetrol , calciparat 1 g, allopurinol 300 mg et crestor 5.
  - event : continue
    - Arg : toujours
  - drug : lasilix
  - dose :  20 mg
  - frequency : /j
  - medication\_blob : lasilix 20 mg/j
  - drug : atacand
  - dose :  8 mg
  - medication\_blob : latacand 8 mg
  - drug : ezetrol
  - drug : calciparat
  - dose :  1 g
  - medication\_blob : calciparat 1 g
  - drug : allopurinol
  - dose :  300 mg
  - medication\_blob : allopurinol 300 mg
  - drug : crestor
  - dose : 5
  - medication\_blob: crestor 5
  - ordonnance\_blob : toujours lasilix 20 mg/j, atacand 8 mg, ezetrol , calciparat 1 g, allopurinol 300 mg et crestor 5

### Informations Offset

<span style="color:red">(metanno ?)</span>

The informations' offsets will be generated automatically by "brat". It starts by 0 for the first character of the discharge summary. Each character, even space, counts as one character. Return to the line count as 2 characters.

Each entry consist of one annotation (entity or relation) and will be printed on its own line with its offset. All annotations follow the same basic structure: each annotation is given an ID that appears first on the line, separated from the rest of the annotation by a single TAB character. The rest of the structure varies by annotation type.

####  For entity, the line follows those example :

T# "type of entity" start stop "Entity"

T1 drugs 1760 1770 solumedrol

T2 dose 1771 1777 180 mg

#### Entities annotated can be fragmented in multiple part (in this document, it is represented as [...] but not in the offset). The line follows the format :

T# "type of entity" start1 stop1;start2 stop2 "Entity"

####  For relation between 2 arguments , the line follows the format ;

R# "type of relation" Arg1:fromID Arg2:toID

R1 is\_dosage Arg1:T2 Arg2:T1

#### Each event have at list 2 lines : one for the highlighting word (like an entity) and one for each "Arg" relation :

E# "Event type":"Event ID" Arg:toID

T100 start 3915 3927 introduction
E1 start:T100 Arg:T99

####  Attribute follows the format :

A# "Attribute class" "ID of Entity marked" "Attribute name"

A1 certainty T109 uncertain



[^1]: Pontus Stenetorp, Sampo Pyysalo, Goran Topić, Tomoko Ohta, Sophia Ananiadou and Jun'ichi Tsujii (2012). brat: a Web-based Tool for NLP-Assisted Text Annotation. In _Proceedings of the Demonstrations Session at EACL 2012_.