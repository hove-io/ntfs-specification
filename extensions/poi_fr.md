# Navitia POI

## Introduction

Les POI permettent d'enrichir Navitia avec des points de départ ou d'arrivée
connus des usagers pour la rubrique "Itinéraires" ou la présentation des lieux
publics ou des services à proximités d'un lieu.

Cette spécification décrit le format d'échange de ces POI vers Navitia2.
Certains POI ont des impacts sur les résultats d'itinéraires, les effets exacts
sont décrits dans les spécifications du moteur.

## Format des données

La structure des données est la même que celle utilisée pour le format NTFS
pour les horaires.  La seule différence est le séparateur ";".

## Liste des fichiers

Fichier | Contrainte | Commentaire
--- | --- | ---
[`poi_type.txt`](#poi_typetxt-requis) | Requis | Ce fichier liste des types de lieux associés aux POI de l'export
[`poi.txt`](#poitxt-requis) | Requis | Ce fichier contient tous ou partie des POI à ajouter au référentiel
[`poi_properties.txt`](#poi_propertiestxt-optionnel) | Optionnel | Ce fichier contient les propriétés complémentaires des POI de l'export. Attention, même si ce fichier est optionnel, certaines propriétés ont des impacts directs sur les itinéraires proposés.
[`poi_links.txt`](#poi_linkstxt-optionnel) | Optionnel | Ce fichier contient un lien de parenté entre les POI de type entrées/sorties et leur parents.
`projection.txt` | Optionnel | Ce fichier indique le système de projection des coordonnés dans le fichier poi.txt. Par défaut, le système est le WGS84 (décimale).
[`geometries.txt`](#geometriestxt-optionnel) | Optionnel | Ce fichier contient la représentation spatiale d'une géometrie au format Well Known Text (WKT). Ces géométries sont référencées dans le fichier [`poi.txt`](#linestxt-requis).

### Description des fichiers

#### poi_type.txt (requis)

Ce fichier contient la liste des types de POI de l'export.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
`poi_type_id` | chaine | Requis | Identifiant unique et pérenne de la catégorie de POI
`poi_type_name` | chaine | Requis | Nom de la catégorie de POI

Le nombre de catégories et le contenu du champ `poi_type_id` est libre, mais
des contraintes sont associées à l'utilisation dans Navitia.
En effet, certains `poi_type` peuvent influencer le comportement de Navitia :
* `poi_type_id=access_point` permet de définir un accès à un POI majeur (entrée de parc, gate du Stade de France par exemple)
* `poi_type_id=amenity:parking` permet de définir les emplacements des parcs relais
* `poi_type_id=amenity:bicycle_rental` permet de définir les emplacements des stations VLS : il est conseillé de laisser l'alimentation d'OSM pour ce type de POI

#### poi.txt (requis)

Colonne | Type | Contrainte | Valeur par défaut | Commentaire
--- | --- | --- | --- | ---
`poi_id` | chaine | Requis | | Identifiant unique et pérenne du POI
`poi_type_id` | chaine | Requis | | Identifiant du type de POI (lien vers le fichier [`poi_type.txt`](#poi_typetxt-requis))
`poi_name` | chaine | Requis | | Nom du POI
`poi_lat` | décimal | Requis | | Latitude du POI
`poi_lon` | décimal | Requis | | Longitude du POI
`poi_weight` | entier | Optionnel | 0 | Poids associé au POI dans la reconnaissance de la saisie (API "places"). Plus le poids est grand, plus le POI remontera en haut de la liste de la recherche (pour des POI avec un matching identique).
`poi_visible` | entier | Requis | | Indique si le POI doit être présenté dans l'auto-complétion.
`geometry_id` | chaine | Optionnel | | Identifiant du tracé représentant le poi (lien vers le fichier [`geometries.txt`](#geometriestxt-optionnel))

L'identifiant du poi `poi_id` est unique toutes sources de POI confondus. Il
convient donc d'être vigilent en cas de source multiple de POI pour éviter
qu'il y ait collision.

#### poi_links.txt (optionnel)

Ce fichier permet de définir les liens entre les POI de type "entrées/sorties" et les POI desservies par ces entrées/sorties. Ainsi lorsque

* un POI est de type "access_point" (poi_type_id=access_point) dans le fichier `poi.txt`
* ce POI est lié à un POI "parent" dans ce fichier
* alors Navitia le prendra en compte dans la recherche d'itinéraire

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
`poi_id` | chaine | Requis | Identifiant unique et pérenne d'un POI de type `poi_type_id=access_point`
`poi_parent_id` | chaine | Requis | Identifiant du POI parent (par exemple, si le POI courant est un accès du POI "stade de France", on trouvera ici l'indentifiant du POI "stade de France"). Un POI "parent" ne peut pas avoir de parent associé (pas de "poupées russes"). Toutefois le fichier ne sera pas rejeté, la ligne sera ignorée.

#### poi_properties.txt (optionnel)

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
`poi_id` | chaine | Requis | Identifiant du POI (lien vers le fichier [`poi.txt`](#poitxt-requis))
`key` | chaine | Requis | Nom de la propriété du POI
`value` | chaine | Requis | Valeur associée à la propriété du POI

Le couple (`poi_id`, `key`) est unique.

La liste des propriétés des POI n'est pas fixée ni limitée, mais certaines
propriétés ont des effets et donc des contraintes particulières dans Navitia et
dans les médias.

### geometries.txt (optionnel)
Ce fichier contient la représentation spatiale d'une géométrie d'un POI. Chaque ligne du fichier représente une géométrie complète de l'objet POI.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
geometry_id | chaine | Requis | Identifiant de la géométrie.
geometry_wkt | géométrie | Requis | Représentation spatiale de la géométrie selon le standard http://fr.wikipedia.org/wiki/Well-known_text.

Note : la donnée contenue dans chaque élément de "geometry_wkt" contient une liste ordonnée de coordonnées.

* La première et la dernière coordonnées doivent être identiques
* Il ne doit pas y avoir d'autres doublons de coordonnées, successifs ou non.

    Le POI peut être un POLYGON ou MULTIPOLYGON, toute autre géométrie sera ignorée.
