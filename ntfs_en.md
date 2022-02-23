NTFS version 0.12
=================

# Introduction

NTFS is Kisio Digital's data exchange format : Navitia Transit Feed Specification. It aims to replace the csv/Fusio format by addressing its weaknesses and managing all types of data in a single format (schedules, ODT, etc.).

This new data format is strongly based on the GTFS data format (https://developers.google.com/transit/gtfs/reference?hl=en-US), with some enhancements allowing a more exhaustive description of the data. As such, the data format is constantly evolving (voir le [changelog](./ntfs_changelog_fr.md) ).

# Data format

Data must be compliant with the following rules :

* All files are compressed into one **ZIP** file
* File encoding : all files are **UTF-8** encoded
* Each file is a **CSV** file with a header : http://tools.ietf.org/html/rfc4180
* The field separator is the **","**
* The coordinate system is [**WGS84**](https://www.wikiwand.com/en/World_Geodetic_System)
* The decimal separator is the **"."**
* The date format is **YYYYMMDD**
* The time format is **HH:MM:SS**
* The datetime format is ISO-8601 in UTC ; it must include the time offset or the `Z`
* The **URL** format must be compliant with [**W3C** recommandations](http://www.w3.org/Addressing/URL/4_URI_Recommentations.html)
* TimeZone reference http://en.wikipedia.org/wiki/List_of_tz_zones
* Languages codes must follow the [**ISO639-2** standard](http://www.loc.gov/standards/iso639-2/php/code_list.php)
* Colors are encoded as Hexadecimal RGB characters (for example **00FFFF**)
* Geometries are described using the format [**WKT**](http://fr.wikipedia.org/wiki/Well-known_text)
* Objects' identifiers must not contain the object's type, as it will be added directly in Navitia's API.

# List of the format files
## Special files
These files are used to describe the origin of the data in the case of a merge of multiple sources of data. A _contributor_ represents a source of data (an operator may require the use of several contributors). A _dataset_ represents a set of data provided by a contributor.
The file [`trips.txt`](#tripstxt-required) refers to the term dataset_id to link a trip to its source of data.

File | Constraint | Description
--- | --- | ---
[`contributors.txt`](#contributorstxt-required) | Required | This file contains the contributors.
[`datasets.txt`](#datasetstxt-required) | Required | This file contains the sets of data of a contributor.

## Fichiers de base
File | Constraint | Description
--- | --- | ---
[`feed_infos.txt`](#feed_infostxt-required) | Required | This file contient des informations complémentaires sur la plage de validité des données, le fournisseur ou toute autre information complémentaires.
[`networks.txt`](#networkstxt-required) | Required | This file contient la description des différents réseaux.
[`commercial_modes.txt`](#commercial_modestxt-required) | Required | This file contient les modes commerciaux (Mode NAViTiA 1)
[`companies.txt`](#companiestxt-required) | Required | This file contient les compagnies
[`lines.txt`](#linestxt-required) | Required | This file contient les lignes
[`physical_modes.txt`](#physical_modestxt-required) | Required | This file contient les modes physiques (ModeType NAViTiA 1)
[`routes.txt`](#routestxt-required) | Required | This file contient les parcours
[`stop_times.txt`](#stop_timestxt-required) | Required | This file contient les horaires
[`stops.txt`](#stopstxt-required) | Required | This file contient les arrêts, les entrées/sorties et les points utilisés par les pathways.
[`trips.txt`](#tripstxt-required) | Required | This file contient les circulations
[`calendar.txt`](#calendartxt-required) | Required | This file contient les jours de fonctionnement
[`calendar_dates.txt`](#calendar_datestxt-optional) | Optional | This file contient les exceptions sur les jours de fonctionnement décrits dans le File[`calendar.txt`](#calendartxt-required)
[`comments.txt`](#commentstxt-optional) | Optional | This file contient les commentaires
[`comment_links.txt`](#comment_linkstxt-optional) | Optional | This file contient les relations entre chaque commentaire et les objets du référentiels associés

## Fichiers complémentaires (hors calendriers par période)
File | Constraint | Description
--- | --- | ---
[`frequencies.txt`](#frequenciestxt-optional) | Optional | This file contient les propriétés des fréquences
[`equipments.txt`](#equipmentstxt-optional)  | Optional | This file contient les propriétés (notamment l’accessibilité) pour les arrêts  et les correspondances
[`transfers.txt`](#transferstxt-optional) | Optional | This file contient les déclarations des correspondances
[`trip_properties.txt`](#trip_propertiestxt-optional) | Optional | This file contient l’accessibilité au niveau des circulations
[`geometries.txt`](#geometriestxt-optional) | Optional | This file contient la représentation spatiale d'une géometrie au format Well Known Text (WKT). Ces géométries sont référencées dans les fichiers [`lines.txt`](#linestxt-required), [`routes.txt`](#routestxt-required), [`trips.txt`](#tripstxt-required).
[`object_properties.txt`](#object_propertiestxt-optional) | Optional | This file contient la description des propriétés complémentaires sur les différents objets du référentiel.
[`object_codes.txt`](#object_codestxt-optional) | Optional | This file contient la liste des codes d'identification complémentaires dans les systèmes externes des différents objets du référentiel.
[`admin_stations.txt`](#admin_stationstxt-optional) | Optional | This file contient la liste des arrêts d'accroche des communes pour les itinéraires au départ ou à l'arrivée d'une commune
[`line_groups.txt`](#line_groupstxt-optional) | Optional | This file contient la définition de groupes de lignes
[`line_group_links.txt`](#line_group_linkstxt-optional) | Optional | This file contient la liaison entre un groupe de ligne et la liste des lignes qui le compose
[`pathways.txt`](#pathwaystxt-optional) | Optional | This file contient les cheminements au sein d'une zone d'arrêt. Ces cheminements ne sont pas nécessairement géographiques, il peut y avoir des simplifications.
[`levels.txt`](#levelstxt-optional) | Optional | This file contient la liste des niveaux au sein d'une zone d'arrêt.
[`addresses.txt`](#addressestxt-optional) | Optional | This file contient la liste des adresses des arrêts physiques.

## Fichiers des calendriers par période
File | Constraint | Description
--- | --- | ---
[`grid_calendars.txt`](#grid_calendarstxt-optional) | Optional |  This file contient les jours de fonctionnement des calendriers
[`grid_exception_dates.txt`](#grid_exception_datestxt-optional) | Optional | This file contient les exceptions sur les jours de fonctionnement des calendriers
[`grid_periods.txt`](#grid_periodstxt-optional) | Optional | This file contient les périodes des calendriers
[`grid_rel_calendar_line.txt`](#grid_rel_calendar_linetxt-optional) | Optional | This file contient les liens entre les lignes et ces calendriers

## Visualisation 
Pour aider à la compréhension, vous pouvez trouver un diagramme des relations entre les fichiers ici: https://dbdiagram.io/embed/5e218b4a9e76504e0ef05fcd ([editer le diagramme](https://dbdiagram.io/d/5e218b4a9e76504e0ef05fcd))

# Description des fichiers
### networks.txt (required)
This file contient la description des différents réseaux.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
network_id | chaine | Required | Identifiant unique et pérenne du réseau
network_name | chaine | Required | Nom du réseau
network_url | chaine | Optional | Lien vers le site institutionnel
network_timezone | chaine | Optional |
network_lang | chaine | Optional |
network_phone | chaine | Optional | Numéro de téléphone de contact
network_address | chaine | Optional | Adresse postale du réseau.
network_sort_order | entier | Optional | Ordre de trie des réseaux, les plus petit sont en premier.

### calendar.txt (required)
This file décrit les périodes de circulation associés aux trips.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
service_id | chaine | Required | Identifiant du calendrier de circulation
monday | entier | Required | (1)
tuesday | entier | Required | (1)
wednesday | entier | Required | (1)
thursday | entier | Required | (1)
friday | entier | Required | (1)
saturday | entier | Required | (1)
sunday | entier | Required | (1)
start_date | date | Required | Date de début du calendrier de circulation
end_date | date | Required | Date de fin du calendrier de circulation incluse dans l'intervalle.

(1) Les valeurs possibles sont :

* 0 - Ne circule pas ce jour
* 1 - Circule ce jour

### calendar_dates.txt (optional)
This file décrit des exceptions aux calendriers définis dans le fichier [`calendar.txt`](#calendartxt-required). Pour faciliter la description de calendriers pour des circulations très ponctuelles, il est possible de définir un calendrier en n'utilisant que le fichier [`calendar_dates.txt`](#calendar_datestxt-optional). De ce fait, le `service_id` ne sera pas présant dans le fichier [`calendar.txt`](#calendartxt-required).

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
service_id | chaine | Required | Identifiant du calendrier de circulation
date | date | Required | Date de l'exception
exception_type | entier | Required | (1)

(1) Les valeurs possibles sont :

* 1 - Le service est ajouté pour cette date
* 2 - Le service est supprimé pour cette date

### comments.txt (optional)

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
comment_id | chaine | required | Identifiant du commentaire
comment_type | chaine | Optional | (1)
comment_label | chaine | Optional | Caractère de renvoi associé au commentaire. Si celui-ci n'est pas précisé, il sera généré automatiquement.
comment_name | chaine | Requis | Texte du commentaire
comment_url | chaine | Optional | URL associé à la note et permettant d'avoir plus d'info, comme par exemple un lien vers la page de description du service de TAD.

(1) Catégorie de commentaire afin de pouvoir les différentier à l'affichage. Les valeurs possibles sont :

* information (ou non renseigné) : indique une note d'information générale
* on_demand_transport : indique qu'il s'agit d'une note d'information sur le Transport à la demande. Ce type de note doit préciser de manière succinte les conditions et le numéro de téléphone de réservation

### comment_links.txt (optional)
This file fait le lien entre un objet du référentiel (ligne, arrêt, horaire, etc.) et un commentaire afin de permettre d'associer plusieurs notes à un objet. et plusieurs objets à une note.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_id | chaine | Requis | Identifiant de l'objet associé à la note
object_type | chaine | Requis | Type de l'objet associé au commentaire. Les valeurs possibles sont stop_area, stop_point, line, route, trip, stop_time ou line_group.
comment_id | chaine | Requis | Identifiant du commentaire (lien vers le fichier [`comments.txt`](#commentstxt-optional))

### commercial_modes.txt (requis)
This file décrit les modes commerciaux, c'est à dire un libellé particulier de mode de transport. Par exemple, BusWay est un nom particulier de BHNS à Nantes.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
commercial_mode_id | chaine | Requis | Identifiant du mode commercial
commercial_mode_name | chaine | Requis | Nom du mode commercial

### companies.txt (requis)
This file décrit l'opérateur de transport exploitant tout ou partie d'un des réseaux contenus dans les données.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
company_id | chaine | Requis | Identifiant de la compagnie
company_name | chaine | Requis | Nom de la compagnie
company_address | chaine | Optional | Adresse complète de société.
company_url | chaine | Optional | Url du site institutionnel de la société. A ne pas confondre avec le lien vers le site du réseau.
company_mail | chaine | Optional | Adresse mail de contact de la société
company_phone | chaine | Optional | Numéro de téléphone de contact

### contributors.txt (requis)
This file permet d'identifier la ou les sources fournissant les données du présent jeu de données.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
contributor_id | chaine | Requis | Identifiant du contributeur
contributor_name | chaine | Requis | Nom du contributeur
contributor_license | chaine | Optional | licence d'utilisation des données du contributeur pour le référentiel
contributor_website | chaine | Optional | URL du site web associé au fournisseur de données

### datasets.txt (requis)
This file liste des jeux de données du contributeur associé contenus dans le référentiel.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
dataset_id | chaine | Requis | Identifiant du jeu de données
contributor_id | chaine | Requis | Identifiant du contributeur (lien vers le fichier [`contributors.txt`](#contributorstxt-required))
dataset_start_date | date | Requis | Date de début de prise en compte du jeu de données (peut-être différent de la date de début de validité de l'export source)
dataset_end_date | date | Requis | Date de fin de prise en compte du jeu de données (peut-être différent de la date de fin de validité de l'export source)
dataset_type | entier (1) | Optional | Type de données représentant la "fraicheur"
dataset_extrapolation | entier | Optional | Indique si les données du service ont été extrapolées (le champ a pour valeur 1) ou non (le champ a pour valeur 0)
dataset_desc | chaine | Optional | Note indiquant le contenu du jeu de données
dataset_system | chaine | Optional | Nom du système source ayant généré les données ou du format des données

(1) Spécifie le type de données :

* 0 - il s'agit de données théoriques
* 1 - il s'agit de données de grèves ou révisées
* 2 - il s'agit de données de production du jour J

### frequencies.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
trip_id | chaine | Requis | Identifiant de la circulation
start_time | heure | Requis | Heure de début de la fréquence
end_time | heure | Requis | Heure de fin de la fréquence. Spécifier 26:00:00 pour 2h du matin du jour considéré.
headway_secs | entier | Requis | Fréquence de départ en secondes

### lines.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_id | chaine | Requis | Identifiant de la ligne commerciale
line_code | chaine | Optional | Code de la ligne commerciale
line_name | chaine | Requis | Nom de la ligne commerciale
forward_line_name | chaine | Optional | Nom de la ligne en sens aller
backward_line_name | chaine | Optional | Nom de la ligne en sens retour
line_color | couleur | Optional | Couleur de la ligne
line_text_color | couleur | Optional | Couleur du code de la ligne
line_sort_order | entier | Optional | Clé de trie de la ligne au sein du réseau. Les indices les plus petits sont retournés en premier.
network_id | chaine | Requis | Identifiant du réseau principal de la ligne (lien vers le fichier [`networks.txt`](#networkstxt-required))
commercial_mode_id | chaine | Requis | Identifiant du mode commercial (lien vers le fichier [`commercial_modes.txt`](#commercial_modestxt-required))
geometry_id | chaine | Optional | Identifiant du tracé représentant la ligne (lien vers le fichier [`geometries.txt`](#geometriestxt-optional))
line_opening_time | heure | Optional | Heure de début de service de la ligne (quelque soit le type de jour ou la periode). Si cette information n'est pas fournie, elle sera recalculée.
line_closing_time | heure | Optional | Heure de fin de service de la ligne (quelque soit le type de jour ou la periode). Si cette information n'est pas fournie, elle sera recalculée. Spécifier une heure superieure à 24 pour indiquer une heure sur le jour d'après.

### routes.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
route_id | chaine | Requis | Identifiant du parcours
route_name | chaine | Requis | Nom du parcours
direction_type | chaine (1) | Optional | Description de la direction de la route. Ce champ est libre, mais il est préconisé d'utiliser un des éléments recommandés ci-dessous.
line_id | chaine | Requis | Identifiant de la ligne commerciale (lien vers le fichier [`lines.txt`](#linestxt-required))
geometry_id | chaine | Optional | Identifiant du tracé représentant le parcours (lien vers le fichier [`geometries.txt`](#geometriestxt-optional))
destination_id | chaine | Optional | Identifiant de la destination principale (lien vers le fichier [`stops.txt`](#stopstxt-required) de type zone d'arrêt)

(1) Liste des valeurs recommandées pour le champ _direction_type_ :

* Pour des sens aller et retour : _forward_ et _backward_
* Pour des parcours en boucle : _clockwise_ et _anticlockwise_
* Pour des parcours entrant et sortants : _inbound_ et _outbound_

### physical_modes.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
physical_mode_id | chaine | Requis | Identifiant du mode physique obligatoirement dans la liste ci-dessous.
physical_mode_name | chaine | Requis | Nom du mode physique
co2_emission | décimal | Optional | Taux d’émission de CO2 du mode physique par voyageur et par km.

**Liste des modes physique disponible :**

Il existe une hiérarchie des modes permettant de classer les zones d'arrêt par son mode de plus haut niveau (cf. norme [NeTEx](http://www.normes-donnees-tc.org/wp-content/uploads/2014/05/NF_Profil_NeTEx_pour_les_arrets-_F-_-_v2.pdf) , chapitre 6.2.3)

    1. Aérien
    2. Maritime/Fluvial
    3. Ferré
    4. Métro
    5. Tram
    6. Funiculaire/Câble
    7. Bus/Car/Trolley

La liste ci-dessous indique la hierarchie NeTEx associée à chaque mode (sans le préfixe du type d'objet retourné par l'API) :

Code du mode physique | Nom du mode physique | Hierarchie NeTEx
--- | --- | ---
Air | Avion | 1
Boat | Navette maritime/fluviale | 2
Bus | Bus | 7
BusRapidTransit | Bus à haut niveau de service | 7
Coach | Autocar | 7
Ferry | Ferry | 2
Funicular | Funiculaire | 6
LocalTrain | Train régional / TER | 3
LongDistanceTrain | Train grande vitesse | 3
Metro | Métro | 4
RapidTransit | Train de banlieue / RER | 3
RailShuttle | Navette ferrée (VAL) | 3
Shuttle | Navette | 7
SuspendedCableCar | Téléphérique / télécabine | 6
Taxi | Taxi | 7
Train | Train | 3
Tramway | Tramway | 5

La liste ci-dessous complète cette liste de modes physiques avec les modes de rabattement possibles dans un itinéraire, afin de leur associer un taux de CO2 dans l'export.

Code du mode physique | Nom du mode physique
--- | ---
BikeSharingService | Vélo en libre service
Bike | Vélo
Car | Voiture

### equipments.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
equipment_id | chaine | Requis | Identifiant de l'équipement |
wheelchair_boarding | entier (1) | Optional | Accès UFR |
sheltered | entier (1) | Optional | Abris couvert
elevator | entier (1) | Optional | Ascenseur
escalator | entier (1) | Optional | Escalier mécanique
bike_accepted | entier (1) | Optional | Embarquement vélo
bike_depot | entier (1) | Optional | Parc vélo
visual_announcement | entier (1) | Optional | Annonce visuelle
audible_announcement | entier (1) | Optional | Annonce sonore
appropriate_escort | entier (1) | Optional | Accompagnement à l'arrêt
appropriate_signage | entier (1) | Optional | Information claire à l'arrêt

    (1) Les valeurs possibles sont :
        0 ou non spécifié - aucune information disponible
        1 - l'équipement est disponible
        2 - l'équipement n'est pas disponible

### stops.txt (requis)
Une ligne du fichier [`stops.txt`](#stopstxt-required) représente un point ou une zone où un véhicule dépose ou fait monter des voyageurs.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
stop_id | chaine | Requis | Identifiant de l'arrêt
visible | entier | Optional | Indique si le stop peut être retourné dans l'autocomplétion (valeur 1) ou s'il est ignoré (valeur 0).
stop_name | chaine | Requis | Nom de l'arrêt
stop_code | chaine | Optional | Code de l'arrêt connu du voyageur. Dans le cas d'une entrée/sortie, contient le code/le numéro de l'entrée/sortie.
stop_lat | décimal | Requis (Spécial) | Latitude. Ce champ est obligatoire, sauf pour les noeuds et les zones d'embarquement (location_type = 4 et 5).
stop_lon | décimal | Requis (Spécial) | Longitude. Ce champ est obligatoire, sauf pour les noeuds et les zones d'embarquement (location_type = 4 et 5).
fare_zone_id | chaine | Optional | Zone tarifaire de l'arrêt. Ce champ ne s'applique que sur les arrêts physiques (location_type = 0)
location_type | entier (1) | Requis | Type de l'arrêt ou de la zone
geometry_id | géometrie | Optional | Ce champ est un lien vers le fichier [`geometries.txt`](#geometriestxt-optional) qui décrit la géométrie associée à une zone géographique (type 2) afin de permettre au moteur de définir les adresses couvertes en cas de TAD zonal "adresse à adresse". Ce champ peut également être utilisé pour préciser une géométrie pour les zones d'arrêts (type 1) et les communes (type 4) pour enrichir le web service.
parent_station | chaine | Optional | Identifiant de la zone d'arrêt. Ne doit pas être renseigné pour les zones d'arrêts et les zones géographiques (location_type = 1 ou 2)
stop_timezone | timezones | Optional | Fuseau horaire, se référer à http://en.wikipedia.org/wiki/List_of_tz_zones. Ce champ n'est pris en compte que sur les points d'arrêts (location_type = 0). L'horaire associé à cet arrêt utilise la timezone du réseau (network) de la course, et non pas la timezone du point d'arrêt même si celle-ci est différente.
equipment_id | chaine | Optional | Identifiant de la propriété accessibilité
level_id | chaine | Optional | lien vers un niveau décrit dans le fichier [`levels.txt`](#levelstxt-optional)
platform_code | chaine | Optional | Identifiant de la plateforme d'un arrêt (par exemple `G` ou `3`). Ne peut être renseigné que pour les arrêts physiques (`location_type=0`) ou les zones d'embarquements (`location_type=5`)
address_id | chaine | Optional | Identifiant de l'adresse de l'arrêt (lien vers le fichier [`addresses.txt`]). Ce champ ne s'applique que sur les arrêts physiques (`location_type=0`)

    (1) Type de l'arrêt ou de la zone :
        0 ou non spécifié - Arrêt physique (objet stop_point)
        1 - Zone d'arrêt (objet stop_area)
        2 - Zone géographique (pour le TAD zonal de type "adressse à adresse", objet stop_zone)
        3 - Entrée / Sortie
        4 - Noeud d'interconnexion de pathways
        5 - Zone d'embarquement (par exemple pour indiquer "milieu du quai")


### stop_times.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
stop_time_id | chaine | Optional | Identifiant unique de l'horaire dans le jeu de données. Cette information n'est pas pérenne et permet uniquement de faire le lien entre un horaire (fichier [`stop_times.txt`](#stop_timestxt-required)) et un commentaire (fichier [`comments.txt`](#commentstxt-optional)) en utilisant le fichier [`comment_links.txt`](#comment_linkstxt-optional). Si ce champ n'est pas fourni, l'horaire ne pourra pas êter lié à un commentaire.
trip_id | chaine | Requis | Identifiant de la circulation
arrival_time | heure | Requis | Heure d'arrivée. Si l'heure d'arrivée n'est pas connue, elle doit être estimée par le système fournissant les données et le champ *stop_time_precision* doit être spécifié à 1. Si la descente est interdite à cet arrêt, l'heure d'arrivée doit être indiquée et le champ *drop_off_type* doit être spécifié à 1.
departure_time | heure | Requis | Heure de départ. Si l'heure de départ n'est pas connue, elle doit être estimée par le système fournissant les données et le champ *stop_time_precision* doit être spécifié à 1. Si la montée est interdite à cet arrêt, l'heure de départ doit être indiquée et le champ *pickup_type* doit être spécifié à 1.
boarding_duration | entier | Optional | Durée nécessaire à l'embarquement en secondes (train, avion, ferry, etc.). Cette valeur est obligatoirement positive ou nulle.
alighting_duration | entier | Optional | Durée nécessaire au débarquement en secondes (train, avion, ferry, etc.). Cette valeur est obligatoirement positive ou nulle.
stop_id | chaine | Requis | Identifiant de l'arrêt physique de passage (cas général). Ce champ peut également référencer une "zone géographique" (stop de type 2) ou une commune (stop de type 3) dans le cas de TAD zonal.
stop_sequence | entier | Requis | Ordre de passage de desserte dans la circulation. Cette valeur est obligatoirement positive ou nulle, et doit être strictement croissante.
stop_headsign | chaine | Optional | Libellé qui doit être affiché au voyageur à la place du `trip_headsign` à cet arrêt.
trip_short_name_at_stop | chaine | Optional | Nom qui doit être affiché au voyageur à la place du `trip_short_name` à cet arrêt.
pickup_type | entier (1) | Optional | Indication sur l'horaire (issues du gtfs)
drop_off_type | entier (1) | Optional | Indication sur l'horaire (issues du gtfs)
local_zone_id  | entier | Optional | identifiant de la zone d'ITL de l'horaire
stop_time_precision | entier (2) | Optional | Précise si l'heure de passage est fiable ou si elle est donnée à titre indicative

    (1) Indication sur l'horaire (issues du gtfs) :
        0 (par défaut) - Horaire régulier
        1 - Montée ou descente interdite
        2 - Horaire sur réservation associé à un TAD (si un message est associé au TAD, voir la liaison avec [`comment_links.txt`](#comment_linkstxt-optional))
        3 - Le véhicule ne s'arrête pas (point de passage); dans ce cas, `pickup_type` et `drop_off_type` doivent tous les deux avoir la même valeur 3

    (2) La fiabilité peut prendre les valeurs suivantes :
        0 - L'heure de passage est fiable, i.e. l'horaire est exact.
        1 - L'heure de passage est approximative (correspond au cas du GTFS où timepoint vaut 0), e.g. un horaire régulier mais qui n'est pas associé à un arrêt de régulation.
        2 - L'heure de passage n'est pas garantie, e.g. l'horaire estimé d'un TAD.
        non spécifiée :
            s'il s'agit d'un horaire associé à une zone (stop de location_type de valeur 2) : l'heure n'est pas garantie
            sinon, l'heure de passage est fiable

### transfers.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
from_stop_id | chaine | Requis | Identifiant de l'arrêt de l’origine de la correspondance (lien vers le fichier [`stops.txt`](#stopstxt-required))
to_stop_id | chaine | Requis | Identifiant de l'arrêt de la destination de la correspondance (lien vers le fichier [`stops.txt`](#stopstxt-required))
min_transfer_time | entier | Optional | Durée minimale de la correspondance en secondes. Cette valeur correspond à la durée de marche à pied qui sera affichée dans les médias. Si la valeur n'est pas spécifié, le système calcul un temps minimum sur la base de la distance Manhattan entre les deux arrêts. La valeur automatique alors calculée a une valeur minimum de 60 secondes. Note : Il est possible que la valeur fournie soit inferieur à 60 (ex : 0 dans le cas d'une correspondance garantie)
real_min_transfer_time | entier | Optional | Durée réelle de correspondance en secondes. Cette valeur correspond à la durée de marche à pied (min_transfer_time) à laquelle on ajoute une durée de tolérance d'exécution (temps minimum de correspondance). Si la valeur n'est pas spécifié, le système utilise (en plus du min_transfer_time) un paramètre par défaut qui est de 120 secondes en général.  La valeur automatique alors calculée sera donc supérieur ou égale à 120 secondes. La valeur saisie ne peut être inférieure à min_transfer_time (mais peut-être égale).
equipment_id | string | Optional | Identifiant de description des propriétés (lien vers le fichier [`equipments.txt`](#equipmentstxt-optional))

### trip_properties.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
trip_property_id | chaine | Requis | Identifiant de la propriété |
wheelchair_accessible | entier (1) | Optional | Le véhicule est accessible aux UFR |
bike_accepted | entier (1) | Optional | Le véhicule permet l'embarquement de vélo
air_conditioned | entier (1) | Optional | Le véhicule dispose de l'air conditionné
visual_announcement | entier (1) | Optional | Le véhicule dispose d'annonces visuelles
audible_announcement | entier (1) | Optional | Le véhicule dispose d'annonces sonores
appropriate_escort | entier (1) | Optional | Un service d'accompagnement à bord est possible (à la montée et à la descente)
appropriate_signage | entier (1) | Optional | L'affichage à bord est est claire et adapté aux personnes en déficience mentale
school_vehicle_type | entier (2) | Optional | Type de transport scolaire

    (1) Les valeurs possibles sont :
        0 ou non spécifié - aucune information disponible
        1 - l'équipement est disponible
        2 - l'équipement n'est pas disponible

    (2) Type de transport scolaire :
        0 ou non spécifié  : transport régulier (non scolaire)
        1 : transport scolaire exclusif
        2 : transport mixte (scolaire et régulier)

### trips.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
route_id | chaine | Requis | Identifiant du parcours (lien vers le fichier [`routes.txt`](#routestxt-required))
service_id | chaine | Requis | Identifiant dues jours de fonctionnements
trip_id | chaine | Requis | Identifiant de la circulation
trip_headsign | chaine | Optional | Texte affiché au voyageur sur le vehicule (par exemple la destination du bus ou le code mission du RER)
trip_short_name | chaine | Optional | Nom de la circulation connu du voyageur permettant de l'identifier de manière unique sur la journée (en général un numéro de train)
block_id | chaine | Optional | Identifiant du prolongement de service
company_id | chaine | Requis | Identifiant de la compagnie (lien vers le fichier [`companies.txt`](#companiestxt-required))
physical_mode_id | chaine | Requis | Identifiant du mode physique (lien vers le fichier [`physical_modes.txt`](#physical_modestxt-required))
trip_property_id | chaine | Optional | Identifiant de la propriété accessibilité (lien vers le fichier [`trip_properties.txt`](#trip_propertiestxt-optional))
dataset_id | chaine | Requis | Identifiant du jeu de données ayant fourni la circulation (lien vers le fichier [`datasets.txt`](#datasetstxt-required)).
geometry_id | chaine | Optional | Identifiant du tracé représentant la circulation (lien vers le fichier [`geometries.txt`](#geometriestxt-optional))
journey_pattern_id | chaine | Optional | Identifiant de la mission (i.e. une séquence ordonnée d'arrêts ayant les mêmes propriétés et parfois connue du voyageur)

    Pour préciser si la circulation est sur réservation (tout ou partie), il faut :
        Indiquer au niveau de l'horaire (fichier [`stop_times.txt`](#stop_timestxt-required)) si la montée et/ou la descente est à réservation
        Indiquer un commentaire (optional) de type TAD via les fichiers [`comments.txt`](#commentstxt-optional) et [`comment_links.txt`](#comment_linkstxt-optional)

### geometries.txt (optional)
This file contient la représentation spatiale d'une géométrie (pour des lignes, parcours et/ou circulations). Chaque ligne du fichier représente une géométrie complète de l'objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
geometry_id | chaine | Requis | Identifiant de la géométrie.
geometry_wkt | géométrie | Requis | Représentation spatiale de la géométrie selon le standard http://fr.wikipedia.org/wiki/Well-known_text.

    Les lignes et parcours peuvent être des LINESTRING ou des MULTILINESTRING.
    Les circulations ne peuvent être que des LINESTRING. Si une MULTILINESTRING est spécifiée, seule la première LINESTRING sera utilisée.
    Les points d'arrêts sont des POINT.
    Les zones d'arrêt peuvent être des POINT, POLYGON ou MULTIPOLYGON.
    Les zones géographiques et communes peuvent être des POLYGON ou MULTIPOLYGON.

    Seules les types de géométries spécifiées sont retenues, les autres types géométries sont ignorées.
    Le format du fichier est volontairement simple, une évolution pourra être envisagée si le besoin est rencontré.

### object_properties.txt (optional)
This file contient la description des propriétés complémentaires sur les différents objets du référentiel.
Ces propriétés sont sous forme de liste de clés / valeurs qui doivent être standardisées par processus.
Une clé ne peut être utilisée qu'une seule fois (avec une seule valeur) pour un même objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_type | chaine | Requis | Type d'objet sur lequel la propriété porte (line, route, trip, stop_area, stop_point)
object_id | chaine | Requis | Identifiant de l'objet sur lequel la propriété porte
object_property_name | chaine | Requis | Nom de la propriété complémentaire (texte libre)
object_property_value | chaine | Requis | Valeur de la propriété complémentaire (texte libre)

### object_codes.txt (optional)
This file contient la liste des codes d'identification complémentaires dans les systèmes externes des différents objets du référentiel.
Ces propriétés sont sous forme de liste de clés / valeurs qui doivent être standardisées par processus.
Une clé peut être utilisée plusieurs fois (avec des valeurs différentes) pour un même objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_type | chaine | Requis | Type d'objet sur lequel la propriété porte (company, network, line, route, trip, stop_area, stop_point)
object_id | chaine | Requis | Identifiant de l'objet sur lequel la propriété porte
object_system | chaine | Requis | Nom du système d'identification de l'objet  (texte libre). Par exemple : "Timeo" ou "UIC" pour les arrêts, "Reflex" pour les lignes.
object_code | chaine | Requis | Code d'identification de l'objet dans le système considéré.

Kisio Digital fournit dans This file :
* les identifiants des objets dans l'ancien système Navitia pour les objets "network", "line", "route", "trip", "stop_point" et "stop_area" avec pour object_system la chaine **"navitia1"**.
* les identifiants des objets déclarés dans la source d'alimentation (NTFS ou GTFS par exemple) avec pour object_system la chaine **"source"**.

### admin_stations.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
admin_id | chaine | Requis | Identifiant de la commune (ou du quartier) tel que retourné par l'API Navitia
admin_name | chaine | Requis | Nom de la commune (ou quartier).
stop_id | chaine | Requis | Identifiant de la zone d'arrêt utilisée comme accroche de la commune (lien vers le fichier [`stops.txt`](#stopstxt-required)). Stop de type 1 oligatoirement.
stop_name | chaine | Optional | Nom de la zone d'arrêt (pour faciliter la lisibilité du fichier)

### pathways.txt (optional)
Attention, This file décrit une modélisation de la station qui n'est pas nécessairement géographique, et peut également être simplifiée.
La modélisation des chemins d'une zone d'arrêt ne peut pas être partielle. Dès qu'un chemin est renseigné pour une zone d'arrêt, il est considéré que toute la station est renseignée.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
pathway_id | chaine | Requis | Identifiant du chemin
from_stop_id | chaine | Requis | Identifiant noeud de début du chemin dans le fichier [`stops.txt`](#stopstxt-required). Ce noeud de départ peut être un point d'arrêt, une entrée/sortie, un noeud générique ou une zone d'embarquement.
to_stop_id | chaine | Requis | Identifiant noeud de fin du chemin (même contraintes que `from_stop_id`).
pathway_mode | entier(1) | Requis | Type de chemin. voir ci-dessous pour les valeurs possibles.
is_bidirectional | booléen | Requis | Indique si le chemin est utilisable dans les deux sens ou uniquement dans le sens from->to.
length | décimal | Optional | Distance en mètres entre les deux extrémités du chemin
traversal_time | entier | Optional | Temps moyen de parcours en secondes.
stair_count | entier | Optional | Nombre de marches (approximatif).
max_slope | décimal | Optional | Ratio maximum de la pente sur ce chemin.
min_width | décimal | Optional | Largeur minimale de ce chemin
signposted_as | chaine | Optional | Texte indiqué au voyageur indiquant ce chemin
reversed_signposted_as | chaine | Optional | Texte indiqué au voyageur dans le sens inverse (si le chemin est indiqué comme bidirectionnel)

    (1) pathway_mode - Les valeurs possibles sont :
        1 - Couloir
        2 - Escalier
        3 - Trottoir roulant / travelator
        4 - Escalier mécanique
        5 - Ascenseur
        6 - Entrée dans une zone payante ("fare gate")
        7 - Sortie de zone payante ("exit gate")


### levels.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
level_id | chaine | Requis | Identifiant du niveau
level_index | décimal | Requis | Numéro de l'étage, le rez-de-chaussée est indiqué à 0, les étages sous le sol sont avec une valeur négative.
level_name | chaine | Optional | Nom associé au niveau (comme par exemple "Mezzanine").

### addresses.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
address_id | chaine | Requis | Identifiant de l'adresse
street_name | chaine | Requis | Nom de la voierie
house_number | chaine | Optional | Numéro du seuil

### line_groups.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_group_id | chaine | Requis | Identifiant du groupe de ligne
line_group_name | chaine | Requis | Nom du groupe de ligne
main_line_id | chaine | Requis | Identifiant de la ligne principale du groupe de lignes (lien vers le fichier [`lines.txt`](#linestxt-required))

Un commentaire peut être associé à un groupe de lignes dans les fichiers [`comments.txt`](#commentstxt-optional) et et [`comment_links.txt`](#comment_linkstxt-optional) .

### line_group_links.txt (optional)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_group_id | chaine | Requis | Identifiant du groupe de ligne
line_id | chaine | Requis | Identifiant de la ligne faisant partie du groupe de lignes (lien vers le fichier [`lines.txt`](#linestxt-required)). Attention, une ligne peut faire partie de plusieurs groupes de lignes.

### feed_infos.txt (requis)
This file contient des informations sur le jeu de données et le système amont qui l'a généré. Pour faciliter son utilisation, la structure du fichier est générique, et la liste des informations est listée ci-dessous.

#### Description du format du fichier
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
feed_info_param | chaine | Requis | Nom du paramètre
feed_info_value | chaine | Requis | Valeur du paramètre

#### Description du contenu du fichier

This file contient 3 types de paramètres :
* des paramètres obligatoires
* des paramètres recommandés (optionals)
* des paramètres libres (possibilité d'ajouter autant de paramètre que souhaité)

Le tableau ci-dessous liste les paramètres obligatoires et recommandés.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
ntfs_version | chaine | Requis | Version du format NTFS utilisé dans l'export (par exemple : "0.3")
feed_start_date | date | Optional | Date de début de validité du jeu de données
feed_end_date | date | Optional | Date de fin de validité du jeu de données
feed_creation_date |  date |  Optional | Date (UTC) de génération du jeu de données
feed_creation_time | heure | Optional | Heure (UTC) de génération du jeu de données
feed_creation_datetime | instant | Optional | Date et heure (UTC) de génération du jeu de données

Le tableau ci-dessous indique les paramètres libres renseignés par Kisio Digital (dépend de l'outil qui génère les données).

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
feed_publisher_name | chaine | Libre | Société/Entité fournissant le jeu de données
feed_license | chaine | Libre | Licence d'utilisation des données globale du référentiel
feed_license_url | chaine | Libre | URL associée à la license d'utilisation des données
fusio_url | chaine | Libre | URL du système ayant généré le jeu de données
fusio_version | chaine | Libre | Version du système ayant généré le jeu de données
tartare_platform | chaine | Libre | Tag indiquant la plateforme qui a généré les données
tartare_coverage_id | chaine | Libre | Id du coverage Tartare ayant généré le jeu de données (1)
tartare_contributor_id | chaine | Libre | Id du contributeur Tartare ayant généré le jeu de données (1)

    (1) seul l'un des champs `tartare_coverage_id` et `tartare_contributor_id` sera présent. Il servent a tracer la source de la donnée dans Tartare afin de faciliter les diagnostiques.

### grid_calendars.txt (optional)
This file contient les calendriers.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | chaine | Requis | Identifiant du calendrier
name | chaine | Requis | Nom du calendrier
monday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
tuesday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
wednesday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
thursday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
friday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
saturday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour
sunday | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour

### grid_exception_dates.txt (optional)
This file contient les exceptions sur les calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | string | Requis | Identifiant du calendrier de grille horaire
date | date | Requis | Date de l'exception
type | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour

### grid_periods.txt (optional)
This file contient les périodes des calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | chaine | Requis | Identifiant du calendrier de grille horaire
start_date | date | Requis | Date de début
end_date | date | Requis | Date de fin

### grid_rel_calendar_line.txt (optional)
This file contient toutes les relations entre les lignes et les calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | chaine | Requis | Identifiant du calendrier de grille horaire
line_id | chaine | Requis | Identifiant de la ligne associée à ce calendrier (lien vers le fichier [`lines.txt`](#linestxt-required)). Ce champ peut être vide si le champ line_external_code est renseigné.
line_external_code | chaine | Requis | cette colonne contient le code externe NAViTiA 1 de la ligne (lien vers le fichier [`lines.txt`](#linestxt-required)). Ce champ peut être vide si le champ line_id et renseigné

# Évolutions possibles du format
Ce chapitre liste des évolutions du format qui peuvent être intéressantes si elles sont utiles concrètement.
## Modification du champ service_id et du nom du fichier [`calendar.txt`](#calendartxt-required)
L'identifiant d'un calendrier ne suit pas la même convention que les autres identifiants (même s'il est cohérent avec le GTFS). Une évolution possible serait de changer le libellé du champ pour indiquer "calendar_id".
De plus, le nom du fichier pourra être changé en `calendars.txt` pour améliorer la cohérence.

## Gestion des données perturbées / de grèves
Afin de limiter la complexité du format, la gestion des données de grève sera effectuée par plusieurs exports :

1. un export contenant toutes les données théoriques du référentiel. L'export NTFS est un export classique, et dont la clé "revised_networks" du fichier [`feed_infos.txt`](#feed_infostxt-required) est vide ou non renseignée.
2. un ou plusieurs exports NTFS de grèves, dont chaque export fournit toutes les données (impactées par la grève ou non) :
    * de un ou plusieurs réseaux spécifiés par la clé **revised_networks** du fichier [`feed_infos.txt`](#feed_infostxt-required)
    * pour des données valides entre les dates spécifiées par **feed_start_date** et **feed_end_date**

Un champ complémentaire et optionnel "base_trip_id" est à prévoir dans le fichier [`trips.txt`](#tripstxt-required) afin de permettre d'associer la circulation théorique et la circulation adaptée (en cas de données de grève par exemple).

## Gestion avancée des géométries (tracés des lignes, parcours et circulations)
Afin de ne pas complexifier inutilement le format NTFS et les outils qui vont le manipuler, le fichier [`geometries.txt`](#geometriestxt-optional) indique un tracé complet pour une géométrie, comme une ligne en fourche ou une ligne à tiroir.  Afin de pouvoir afficher le tracé réel des bus dans la feuille de route (ie. n'avoir que la portion utilisée de la ligne), un découpage de cette géométrie est réalisé dans Navitia de manière automatique.
Si le besoin d'affiner cette gestion est validé, une évolution du format du fichier [`geometries.txt`](#geometriestxt-optional) peut être envisagé de la manière suivante (à confirmer) :
* Une ligne représentera un segment de la ligne/parcours/circulation entre deux points d'arrêts consécutifs (et de manière orientée ?)
* La précision des points d'arrêts d'origine et de destination du segment est faite par l'ajout de deux colonnes optionnelles

## Gestion de l'émission de CO2 par ligne ou véhicule
L'émission de CO2 est gérée en France par mode de transport. Les valeurs sont spécifiées par l'ADEME. Le format NTFS permet d'échanger cette information au niveau du mode physique. En fonction des besoins rencontrés à l'avenir, un ajout de cette informaion au niveau de la ligne et/ou du véhicule est envisagée. Dans ce cas, un système de "surcharge" de l'inforamtion sera à mettre en place pour prendre en compte par priorité :

1. le taux au niveau du véhicule s'il est disponible
2. le taux au niveau de la ligne s'il est disponible
3. le taux au niveau du mode s'il est disponible
4. une indication sur le fait que la valeur est inconnue

## Gestion d'un service (ex : Acces+)
Ajouter la notion de "service" pour un accompagnement sur un réseau de transport, comme par exemple :
* Accès+ TER ou Accès+ TGV sur le réseau SNCF
* OptiBus de Keolis PMR Rhone d'accompagnement sur le réseau de TCL

Ce service permet d'ajouter de l'accessibilité (UFR, Cognitif ou autre) sur un ou plusieurs objets ou directement dans le calcul.

# Exemples de modélisation de TAD
Voici quelques exemples de modélisation de TAD dans les fichiers NTFS. Seuls les fichiers impactés sont représentés ([`stops.txt`](#stopstxt-required) et [`stop_times.txt`](#stop_timestxt-required)).
Il est à également à noter qu'il est possible :
* de faire du rabattement vers horaire en spécifiant bien un horaire à un point d'arrivé (et pas une zone) dans le ficheir stop_time,
* de faire de la fréquence sur du TAD zonal en utilisant le fichier [`frequencies.txt`](#frequenciestxt-optional)

**Attention, les coordonnées et les surfaces des zones ne sont pas cohérentes, c'est l'architecture des données dans les fichiers qui est importante ici.**

Pour résumé, il suffit de connaitre les points suivants:
* Les horaires sont portés par des « stop » (un stop est une ligne issue du fichier stop)
* Les stop peuvent être
    * Des stop_points (type=0)
    * Des stop_area (type=1)
    * Des area (type=2): représentant une surface géographique (commune, quartier, zone...)
* Et ensuite, on peut accrocher des horaires sur ces stops, quelque soient leur type
    * dans les faits, on accrochera des horaires sur les stop_points et les area, mais pas sur les stop_areas: les impacts sur les différentes utilisations (itinéraire, fiche horaire) seraient non maitrisés

Cette modélisation unique traite l'ensemble des possibilité:
* TAD zonal d'adresse à adresse
* TAD zonal d'arrêts à arrêts
* TAD de rabattement d'adresse à arrêts
* TAD de rabattement d'arrêts à arrêts
* TAD "multiple" Zones - Arrêts - Zones, Arrêts - Zones - Arrêts, etc...

### Modélisation NTFS

* Les lignes intégralement TAD, sans horaire, sont déclarée en "fréquence"
* Les stop_point du fichier [`stops.txt`](#stopstxt-required) de type "shape", ne devraient pas être en correspondance avec d'autres stop_point.
    * A l'intégration des données, les correspondances éventuellement déclarées seront ignorées
* Les correspondances entre 2 circulations ne sont autorisées QUE si un des 2 horaires est fixe
    * Les correspondances entre 2 horaires estimés sont interdites (stop.estimated vers stop.estimated)
* Des lignes peuvent traiter de zones d'adresse à adresse
    * Les itinéraires adresse à adresse au sein de ces zones ne seront pas proposés
* les lignes TAD ne sont plus typées
    * Ce sont les stop_times qui détermine le type de TAD
* Par exemple:
    * Gestion du TAD zonal adresse à adresse et arrêt à arrêt en horaire estimé
        * tous les horaires sont estimés
            * adresse à adresse=depuis un stop_point de type shape vers un stop_point de type shape
                * donc pas en correspondance parce que les hsape ne sotn pas en correspondance
            * arrêt à arrêt
                * correspondance proposée avec des lignes à horaires fixes
    * Gestion des correspondances entre TAD de rabattement, la même règle est appliquée
        * si l'horaire de rabattement est fixe
            * la correspondance sera proposée
        * si l'horaire de rabattement est estimé
            * la correspondance avec un horaire fixe (train) est possible
            * la correspondance avec un autre TAD à rabattement estimé ne sera pas possible


### Exemples
#### Ligne mixte
Le cas de la zone 2 ci-dessous est trivial: on définit cette zone à l'aide d'un shape qui englobe les adresses ayant accès au TAD. Il suffit ensuite d'associer un horaire à ce shape.

![Exemple de ligne mixte](NTFS_image1.png)

Les zones de dessertes ne remontent pas en autocompletion: elles permettent de déterminer l'offre uniquement.
On peut alimenter le fichier [`stop_times.txt`](#stop_timestxt-required) en mettant des horaires précis sur chacun des points d'arrêt pouvant appartenir à des zones d'arrêts différentes, avec des informations ITL manuelles:

**Fichier stop: déclare les "arrêts"**

stop_id | stop_name | stop_lat | stop_lon | location_type | geometry_id | parent_station
--- | --- | --- | --- | --- | --- | ---
stop_point_A | A | 47.01 | 1.01 | 0 |  | stop_area_A
stop_point_B | A | 47.01 | 1.01 | 0 |  | stop_area_B
stop_point_C | A | 47.01 | 1.01 | 0 |  | stop_area_C
stop_point_D | A | 47.01 | 1.01 | 0 |  | stop_area_D
stop_point_E | A | 47.01 | 1.01 | 0 |  | stop_area_E
stop_point_H | A | 47.01 | 1.01 | 0 |  | stop_area_H
stop_area_A | A | 47.01 | 1.01 | 1 |  |
stop_area_B | A | 47.01 | 1.01 | 1 |  |
stop_area_C | A | 47.01 | 1.01 | 1 |  |
stop_area_D | A | 47.01 | 1.01 | 1 |  |
stop_area_E | A | 47.01 | 1.01 | 1 |  |
stop_area_H | A | 47.01 | 1.01 | 1 |  |
zone_2 | Zone 1 | 47.01 | 1.01 | 2 | id_vers_POLYGON((1 1,5 1,5 5,1 5,1 1)) |

**Fichier [`stop_times.txt`](#stop_timestxt-required): déclare les "horaires", estimés ou non**

trip_id | stop_id | arrival_time | departure_time | stop_sequence | pickup_type | drop_off_type | stop_time_precision | zone_itl
--- | --- | --- | --- | --- | --- | --- | --- | ---
trip_1 | stop_point_A |  | 09:02:00 | 1 | 0 | 0 | 0 | 0
trip_1 | stop_point_B | 09:15:00 | 09:05:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_C | 09:15:00 | 09:07:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_D | 09:15:00 | 09:09:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_E | 09:17:00 | 09:18:00 | 1 | 0 | 0 | 0 | 2
trip_1 | zone_2 | 09:25:00 | 09:20:00 | 2 | 2 | 2 | 1 | 3
trip_1 | stop_point_H | 09:27:00 |  | 1 | 0 | 0 | 0 | 4

#### Ligne arrêt à arrêt sans horaires
![Exemple de ligne arrêt à arrêt](NTFS_image2.png)

Les zones n'ont pas de valeur dans le NTFS: la prise des passagers se fait sur des stop_point. L'idée est de déclarer des "blocs d'horaires" sur chacun des stop_point de chaque zone :

* A>B>C
    * descente interdite
    * 08h00 partout
* A>B>C
    * montée interdite
    * 08h10 partout
* D>E>F
    * descente interdite
    * 09h00 partout
* D>E>F
    * montée interdite
    * 09h10 partout
* G>H>I
    * descente interdite
    * 10h00 partout
* G>H>I
    * montée interdite
    * 10h10 partout
