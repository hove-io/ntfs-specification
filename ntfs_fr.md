Spécification NTFS
==================

# Introduction

NTFS est le format d'échange de données de Hove : Navitia Transit Feed Specification.
Il a pour objectif de remplacer le format csv/Fusio afin de combler ses lacunes et permettre la gestion de tout type de données dans un seul format (horaires, TAD, etc.).

Ce nouveau format s'inspire très fortement du format GTFS (https://developers.google.com/transit/gtfs/reference?hl=fr-FR), tout en l'adaptant afin de permettre une description des données la plus exhaustive possible. A ce titre, le format est amené à évoluer (voir le [changelog](./ntfs_changelog.md) ).

# Format des données

Les données sont formatées de la manière suivante :

* Les fichiers sont compressées dans un fichier **ZIP**
* Encodage des fichiers : tous les fichiers sont encodés en **UTF-8**
* Chaque fichier est un fichier **CSV** contenant une ligne d'entête : http://tools.ietf.org/html/rfc4180
* Le séparateur de champ est la **","**
* Le système de coordonnées est le **WGS84**
* Le séparateur décimal est le **"."**
* Le format de date est **YYYYMMDD**
* Le format de l'heure est **HH:MM:SS**
* le format d'un instant (date + heure) est au format ISO-8601 en UTC; il doit inclure le suffixe d'offset ou le `Z`
* Le formatage des **URL** doit respecter le format du **W3C** : http://www.w3.org/Addressing/URL/4_URI_Recommentations.html
* Les fuseaux horaires http://en.wikipedia.org/wiki/List_of_tz_zones
* Les langues indiquées doivent respecter la norme **ISO 639-2** : http://www.loc.gov/standards/iso639-2/php/code_list.php
* Les couleurs sont spécifiées en RVB hexadécimal (par exemple **00FFFF**)
* Les géométries sont décrites en utilisant le format **WKT** : http://fr.wikipedia.org/wiki/Well-known_text
* Les identifiants des objets ne doivent pas contenir le type de l'objet. Ce dernier sera ajouté directement dans l'API navitia

# Liste des fichiers du format
## Fichiers spéciaux
Ces fichiers permettent de décrire précisément d'où viennent les données dans le cas d'un référentiel agrégeant plusieurs sources de données. La notion de _contributor_ correspond à une source de données (un exploitant peut nécessiter l'utilisation de plusieurs contributeurs). La notion de _dataset_ correspond à un jeu de données provenant d'un contributeur.
Le fichier [`trips.txt`](#tripstxt-requis) fait une référence à _dataset_id_ afin de lier une circulation à sa donnée source.

Fichier | Contrainte | Commentaire
--- | --- | ---
[`contributors.txt`](#contributorstxt-requis) | Requis | Ce fichier contient les contributeurs.
[`datasets.txt`](#datasetstxt-requis) | Requis | Ce fichier contient les sources de données d'un contributeur.

## Fichiers de base
Fichier | Contrainte | Commentaire
--- | --- | ---
[`feed_infos.txt`](#feed_infostxt-requis) | Requis | Ce fichier contient des informations complémentaires sur la plage de validité des données, le fournisseur ou toute autre information complémentaires.
[`networks.txt`](#networkstxt-requis) | Requis | Ce fichier contient la description des différents réseaux.
[`commercial_modes.txt`](#commercial_modestxt-requis) | Requis | Ce fichier contient les modes commerciaux (Mode NAViTiA 1)
[`companies.txt`](#companiestxt-requis) | Requis | Ce fichier contient les compagnies
[`lines.txt`](#linestxt-requis) | Requis | Ce fichier contient les lignes
[`physical_modes.txt`](#physical_modestxt-requis) | Requis | Ce fichier contient les modes physiques (ModeType NAViTiA 1)
[`routes.txt`](#routestxt-requis) | Requis | Ce fichier contient les parcours
[`stop_times.txt`](#stop_timestxt-requis) | Requis | Ce fichier contient les horaires
[`stops.txt`](#stopstxt-requis) | Requis | Ce fichier contient les arrêts, les entrées/sorties et les points utilisés par les pathways.
[`trips.txt`](#tripstxt-requis) | Requis | Ce fichier contient les circulations
[`calendar.txt`](#calendartxt-requis) | Requis | Ce fichier contient les jours de fonctionnement
[`calendar_dates.txt`](#calendar_datestxt-optionnel) | Optionnel | Ce fichier contient les exceptions sur les jours de fonctionnement décrits dans le fichier [`calendar.txt`](#calendartxt-requis)
[`comments.txt`](#commentstxt-optionnel) | Optionnel | Ce fichier contient les commentaires
[`comment_links.txt`](#comment_linkstxt-optionnel) | Optionnel | Ce fichier contient les relations entre chaque commentaire et les objets du référentiels associés

## Fichiers complémentaires (hors calendriers par période)
Fichier | Contrainte | Commentaire
--- | --- | ---
[`frequencies.txt`](#frequenciestxt-optionnel) | Optionnel | Ce fichier contient les propriétés des fréquences
[`equipments.txt`](#equipmentstxt-optionnel)  | Optionnel | Ce fichier contient les propriétés (notamment l’accessibilité) pour les arrêts  et les correspondances
[`transfers.txt`](#transferstxt-optionnel) | Optionnel | Ce fichier contient les déclarations des correspondances
[`trip_properties.txt`](#trip_propertiestxt-optionnel) | Optionnel | Ce fichier contient l’accessibilité au niveau des circulations
[`geometries.txt`](#geometriestxt-optionnel) | Optionnel | Ce fichier contient la représentation spatiale d'une géometrie au format Well Known Text (WKT). Ces géométries sont référencées dans les fichiers [`lines.txt`](#linestxt-requis), [`routes.txt`](#routestxt-requis), [`trips.txt`](#tripstxt-requis).
[`object_properties.txt`](#object_propertiestxt-optionnel) | Optionnel | Ce fichier contient la description des propriétés complémentaires sur les différents objets du référentiel.
[`object_codes.txt`](#object_codestxt-optionnel) | Optionnel | Ce fichier contient la liste des codes d'identification complémentaires dans les systèmes externes des différents objets du référentiel.
[`admin_stations.txt`](#admin_stationstxt-optionnel) | Optionnel | Ce fichier contient la liste des arrêts d'accroche des communes pour les itinéraires au départ ou à l'arrivée d'une commune
[`line_groups.txt`](#line_groupstxt-optionnel) | Optionnel | Ce fichier contient la définition de groupes de lignes
[`line_group_links.txt`](#line_group_linkstxt-optionnel) | Optionnel | Ce fichier contient la liaison entre un groupe de ligne et la liste des lignes qui le compose
[`pathways.txt`](#pathwaystxt-optionnel) | Optionnel | Ce fichier contient les cheminements au sein d'une zone d'arrêt. Ces cheminements ne sont pas nécessairement géographiques, il peut y avoir des simplifications.
[`levels.txt`](#levelstxt-optionnel) | Optionnel | Ce fichier contient la liste des niveaux au sein d'une zone d'arrêt.
[`addresses.txt`](#addressestxt-optionnel) | Optionnel | Ce fichier contient la liste des adresses des arrêts physiques.
[`administrative_regions.txt`](#administrative_regionstxt-optionnel) | Optionnel | Ce fichier contient les régions administratives associées aux arrêts.
[`occupancies.txt`](#occupanciestxt-optionnel-expérimental) | Optionnel | Ce fichier contient les informations d'affluence sur le réseau [**expérimental**]

## Fichiers des calendriers par période
Fichier | Contrainte | Commentaire
--- | --- | ---
[`grid_calendars.txt`](#grid_calendarstxt-optionnel) | Optionnel |  Ce fichier contient les jours de fonctionnement des calendriers
[`grid_exception_dates.txt`](#grid_exception_datestxt-optionnel) | Optionnel | Ce fichier contient les exceptions sur les jours de fonctionnement des calendriers
[`grid_periods.txt`](#grid_periodstxt-optionnel) | Optionnel | Ce fichier contient les périodes des calendriers
[`grid_rel_calendar_line.txt`](#grid_rel_calendar_linetxt-optionnel) | Optionnel | Ce fichier contient les liens entre les lignes et ces calendriers

## Visualisation 
Pour aider à la compréhension, vous pouvez trouver un diagramme des relations entre les fichiers ici: https://dbdiagram.io/embed/5e218b4a9e76504e0ef05fcd ([editer le diagramme](https://dbdiagram.io/d/5e218b4a9e76504e0ef05fcd))

# Description des fichiers
### networks.txt (requis)
Ce fichier contient la description des différents réseaux.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
network_id | chaine | Requis | Identifiant unique et pérenne du réseau
network_name | chaine | Requis | Nom du réseau
network_url | chaine | Optionnel | Lien vers le site institutionnel
network_timezone | chaine | Optionnel |
network_lang | chaine | Optionnel |
network_phone | chaine | Optionnel | Numéro de téléphone de contact
network_address | chaine | Optionnel | Adresse postale du réseau.
network_fare_url | chaine | Optionnel | Lien vers le site institutionnel pour les informations tarifaires
network_sort_order | entier | Optionnel | Ordre de tri des réseaux, les plus petits sont en premier.

### calendar.txt (requis)
Ce fichier décrit les périodes de circulation associés aux trips.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
service_id | chaine | Requis | Identifiant du calendrier de circulation
monday | entier | Requis | (1)
tuesday | entier | Requis | (1)
wednesday | entier | Requis | (1)
thursday | entier | Requis | (1)
friday | entier | Requis | (1)
saturday | entier | Requis | (1)
sunday | entier | Requis | (1)
start_date | date | Requis | Date de début du calendrier de circulation
end_date | date | Requis | Date de fin du calendrier de circulation incluse dans l'intervalle.

(1) Les valeurs possibles sont :

* 0 - Ne circule pas ce jour
* 1 - Circule ce jour

### calendar_dates.txt (optionnel)
Ce fichier décrit des exceptions aux calendriers définis dans le fichier [`calendar.txt`](#calendartxt-requis). Pour faciliter la description de calendriers pour des circulations très ponctuelles, il est possible de définir un calendrier en n'utilisant que le fichier [`calendar_dates.txt`](#calendar_datestxt-optionnel). De ce fait, le `service_id` ne sera pas présent dans le fichier [`calendar.txt`](#calendartxt-requis).

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
service_id | chaine | Requis | Identifiant du calendrier de circulation
date | date | Requis | Date de l'exception
exception_type | entier | Requis | (1)

(1) Les valeurs possibles sont :

* 1 - Le service est ajouté pour cette date
* 2 - Le service est supprimé pour cette date

### comments.txt (optionnel)

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
comment_id | chaine | Requis | Identifiant du commentaire
comment_type | chaine | Optionnel | (1)
comment_label | chaine | Optionnel | Caractère de renvoi associé au commentaire. Si celui-ci n'est pas précisé, il sera généré automatiquement.
comment_name | chaine | Requis | Texte du commentaire
comment_url | chaine | Optionnel | URL associé à la note et permettant d'avoir plus d'info, comme par exemple un lien vers la page de description du service de TAD.

(1) Catégorie de commentaire afin de pouvoir les différentier à l'affichage. Les valeurs possibles sont :

* information (ou non renseigné) : indique une note d'information générale
* on_demand_transport : indique qu'il s'agit d'une note d'information sur le Transport à la demande. Ce type de note doit préciser de manière succinte les conditions et le numéro de téléphone de réservation. Voir également l'extension [`booking_rules.md`](https://github.com/hove-io/ntfs-specification/blob/master/extensions/booking_rules.md)

### comment_links.txt (optionnel)
Ce fichier fait le lien entre un objet du référentiel (ligne, arrêt, horaire, etc.) et un commentaire afin de permettre d'associer plusieurs notes à un objet. et plusieurs objets à une note.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_id | chaine | Requis | Identifiant de l'objet associé à la note
object_type | chaine | Requis | Type de l'objet associé au commentaire. Les valeurs possibles sont stop_area, stop_point, line, route, trip, stop_time ou line_group.
comment_id | chaine | Requis | Identifiant du commentaire (lien vers le fichier [`comments.txt`](#commentstxt-optionnel))

### commercial_modes.txt (requis)
Ce fichier décrit les modes commerciaux, c'est à dire un libellé particulier de mode de transport. Par exemple, BusWay est un nom particulier de BHNS à Nantes.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
commercial_mode_id | chaine | Requis | Identifiant du mode commercial
commercial_mode_name | chaine | Requis | Nom du mode commercial

### companies.txt (requis)
Ce fichier décrit les opérateurs de transport (exploitant tout ou partie d'un des réseaux contenus dans les données) et/ou les autorités organisatrice de transport.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
company_id | chaine | Requis | Identifiant de la compagnie
company_name | chaine | Requis | Nom de la compagnie
company_address | chaine | Optionnel | Adresse complète de société.
company_url | chaine | Optionnel | Url du site institutionnel de la société. A ne pas confondre avec le lien vers le site du réseau.
company_mail | chaine | Optionnel | Adresse mail de contact de la société
company_phone | chaine | Optionnel | Numéro de téléphone de contact
role | chaine | Optionnel | Indique quel rôle exerce la compagnie (1)

(1) Les valeurs possibles sont:

* authority - La compagnie exerce le rôle d'autorité organisatrice
* operator - La compagnie exerce le rôle d'opérateur de transport 

### contributors.txt (requis)
Ce fichier permet d'identifier la ou les sources fournissant les données du présent jeu de données.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
contributor_id | chaine | Requis | Identifiant du contributeur
contributor_name | chaine | Requis | Nom du contributeur
contributor_license | chaine | Optionnel | licence d'utilisation des données du contributeur pour le référentiel
contributor_website | chaine | Optionnel | URL du site web associé au fournisseur de données

### datasets.txt (requis)
Ce fichier liste des jeux de données du contributeur associé contenus dans le référentiel.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
dataset_id | chaine | Requis | Identifiant du jeu de données
contributor_id | chaine | Requis | Identifiant du contributeur (lien vers le fichier [`contributors.txt`](#contributorstxt-requis))
dataset_start_date | date | Requis | Date de début de prise en compte du jeu de données (peut-être différent de la date de début de validité de l'export source)
dataset_end_date | date | Requis | Date de fin de prise en compte du jeu de données (peut-être différent de la date de fin de validité de l'export source)
dataset_type | entier (1) | Optionnel | Type de données représentant la "fraicheur"
dataset_extrapolation | entier | Optionnel | Indique si les données du service ont été extrapolées (le champ a pour valeur 1) ou non (le champ a pour valeur 0)
dataset_desc | chaine | Optionnel | Note indiquant le contenu du jeu de données
dataset_system | chaine | Optionnel | Nom du système source ayant généré les données ou du format des données

(1) Spécifie le type de données :

* 0 - il s'agit de données théoriques
* 1 - il s'agit de données de grèves ou révisées
* 2 - il s'agit de données de production du jour J

### frequencies.txt (optionnel)
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
line_code | chaine | Optionnel | Code de la ligne commerciale
line_name | chaine | Requis | Nom de la ligne commerciale
forward_line_name | chaine | Optionnel | Nom de la ligne en sens aller
backward_line_name | chaine | Optionnel | Nom de la ligne en sens retour
line_color | couleur | Optionnel | Couleur de la ligne
line_text_color | couleur | Optionnel | Couleur du code de la ligne
line_sort_order | entier | Optionnel | Clé de trie de la ligne au sein du réseau. Les indices les plus petits sont retournés en premier.
network_id | chaine | Requis | Identifiant du réseau principal de la ligne (lien vers le fichier [`networks.txt`](#networkstxt-requis))
commercial_mode_id | chaine | Requis | Identifiant du mode commercial (lien vers le fichier [`commercial_modes.txt`](#commercial_modestxt-requis))
geometry_id | chaine | Optionnel | Identifiant du tracé représentant la ligne (lien vers le fichier [`geometries.txt`](#geometriestxt-optionnel))
line_opening_time | heure | Optionnel | Heure de début de service de la ligne (quelque soit le type de jour ou la periode). Si cette information n'est pas fournie, elle sera recalculée.
line_closing_time | heure | Optionnel | Heure de fin de service de la ligne (quelque soit le type de jour ou la periode). Si cette information n'est pas fournie, elle sera recalculée. Spécifier une heure superieure à 24 pour indiquer une heure sur le jour d'après.

### routes.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
route_id | chaine | Requis | Identifiant du parcours
route_name | chaine | Requis | Nom du parcours
direction_type | chaine (1) | Optionnel | Description de la direction de la route. Ce champ est libre, mais il est préconisé d'utiliser un des éléments recommandés ci-dessous.
line_id | chaine | Requis | Identifiant de la ligne commerciale (lien vers le fichier [`lines.txt`](#linestxt-requis))
geometry_id | chaine | Optionnel | Identifiant du tracé représentant le parcours (lien vers le fichier [`geometries.txt`](#geometriestxt-optionnel))
destination_id | chaine | Optionnel | Identifiant de la destination principale (lien vers le fichier [`stops.txt`](#stopstxt-requis) de type zone d'arrêt)

(1) Liste des valeurs recommandées pour le champ _direction_type_ :

* Pour des sens aller et retour : _forward_ et _backward_
* Pour des parcours en boucle : _clockwise_ et _anticlockwise_
* Pour des parcours entrant et sortants : _inbound_ et _outbound_

### physical_modes.txt (requis)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
physical_mode_id | chaine | Requis | Identifiant du mode physique obligatoirement dans la liste ci-dessous.
physical_mode_name | chaine | Requis | Nom du mode physique
co2_emission | décimal | Optionnel | Taux d’émission de CO2 du mode physique en grammes par voyageur et par km.

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

### equipments.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
equipment_id | chaine | Requis | Identifiant de l'équipement |
wheelchair_boarding | entier (1) | Optionnel | Accès UFR |
sheltered | entier (1) | Optionnel | Abris couvert
elevator | entier (1) | Optionnel | Ascenseur
escalator | entier (1) | Optionnel | Escalier mécanique
bike_accepted | entier (1) | Optionnel | Embarquement vélo
bike_depot | entier (1) | Optionnel | Parc vélo
visual_announcement | entier (1) | Optionnel | Annonce visuelle
audible_announcement | entier (1) | Optionnel | Annonce sonore
appropriate_escort | entier (1) | Optionnel | Accompagnement à l'arrêt
appropriate_signage | entier (1) | Optionnel | Information claire à l'arrêt

    (1) Les valeurs possibles sont :
        0 ou non spécifié - aucune information disponible
        1 - l'équipement est disponible
        2 - l'équipement n'est pas disponible

### stops.txt (requis)
Une ligne du fichier [`stops.txt`](#stopstxt-requis) représente un point ou une zone où un véhicule dépose ou fait monter des voyageurs.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
stop_id | chaine | Requis | Identifiant de l'arrêt
visible | entier | Optionnel | Indique si le stop peut être retourné dans l'autocomplétion (valeur 1) ou s'il est ignoré (valeur 0).
stop_name | chaine | Requis | Nom de l'arrêt
stop_code | chaine | Optionnel | Code de l'arrêt connu du voyageur. Dans le cas d'une entrée/sortie, contient le code/le numéro de l'entrée/sortie.
stop_lat | décimal | Requis (Spécial) | Latitude. Ce champ est obligatoire, sauf pour les noeuds et les zones d'embarquement (location_type = 4 et 5).
stop_lon | décimal | Requis (Spécial) | Longitude. Ce champ est obligatoire, sauf pour les noeuds et les zones d'embarquement (location_type = 4 et 5).
fare_zone_id | chaine | Optionnel | Zone tarifaire de l'arrêt. Ce champ ne s'applique que sur les arrêts physiques (location_type = 0)
location_type | entier (1) | Requis | Type de l'arrêt ou de la zone
geometry_id | géometrie | Optionnel | Ce champ est un lien vers le fichier [`geometries.txt`](#geometriestxt-optionnel) qui décrit la géométrie associée à une zone géographique (type 2) afin de permettre au moteur de définir les adresses couvertes en cas de TAD zonal "adresse à adresse". Ce champ peut également être utilisé pour préciser une géométrie pour les zones d'arrêts (type 1) et les communes (type 4) pour enrichir le web service.
parent_station | chaine | Optionnel | Identifiant de la zone d'arrêt. Ne doit pas être renseigné pour les zones d'arrêts et les zones géographiques (location_type = 1 ou 2)
stop_timezone | timezones | Optionnel | Fuseau horaire, se référer à http://en.wikipedia.org/wiki/List_of_tz_zones. Ce champ n'est pris en compte que sur les points d'arrêts (location_type = 0). L'horaire associé à cet arrêt utilise la timezone du réseau (network) de la course, et non pas la timezone du point d'arrêt même si celle-ci est différente.
equipment_id | chaine | Optionnel | Identifiant de la propriété accessibilité
level_id | chaine | Optionnel | lien vers un niveau décrit dans le fichier [`levels.txt`](#levelstxt-optionnel)
platform_code | chaine | Optionnel | Identifiant de la plateforme d'un arrêt (par exemple `G` ou `3`). Ne peut être renseigné que pour les arrêts physiques (`location_type=0`) ou les zones d'embarquements (`location_type=5`)
address_id | chaine | Optionnel | Identifiant de l'adresse de l'arrêt (lien vers le fichier [`addresses.txt`]).

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
stop_time_id | chaine | Optionnel | Identifiant unique de l'horaire dans le jeu de données. Cette information n'est pas pérenne et permet uniquement de faire le lien entre un horaire (fichier [`stop_times.txt`](#stop_timestxt-requis)) et un commentaire (fichier [`comments.txt`](#commentstxt-optionnel)) en utilisant le fichier [`comment_links.txt`](#comment_linkstxt-optionnel). Si ce champ n'est pas fourni, l'horaire ne pourra pas êter lié à un commentaire.
trip_id | chaine | Requis | Identifiant de la circulation
arrival_time | heure | Optionnel | Heure d'arrivée. Si l'heure d'arrivée n'est pas connue, elle doit être estimée par le système fournissant les données et le champ *stop_time_precision* doit être spécifié à 1. Si la descente est interdite à cet arrêt, l'heure d'arrivée doit être indiquée et le champ *drop_off_type* doit être spécifié à 1. Le champ doit être laissé vide si *start_pickup_drop_off_window* ou *end_pickup_drop_off_window* est défini.
departure_time | heure | Optionnel | Heure de départ. Si l'heure de départ n'est pas connue, elle doit être estimée par le système fournissant les données et le champ *stop_time_precision* doit être spécifié à 1. Si la montée est interdite à cet arrêt, l'heure de départ doit être indiquée et le champ *pickup_type* doit être spécifié à 1. Le champ doit être laissé vide si *start_pickup_drop_off_window* ou *end_pickup_drop_off_window* est défini.
start_pickup_drop_off_window | heure | Optionnel | Heure d'ouverture du service de TAD sur le point d'arrêt. Valeur obligatoire si *end_pickup_drop_off_window* est défini, vide si *arrival_time* ou *departure_time* est défini.
end_pickup_drop_off_window | heure | Optionnel | Heure de fermeture du service de TAD sur le point d'arrêt. Valeur obligatoire si *start_pickup_drop_off_window* est défini, vide si *arrival_time* ou *departure_time* est défini.
boarding_duration | entier | Optionnel | Durée nécessaire à l'embarquement en secondes (train, avion, ferry, etc.). Cette valeur est obligatoirement positive ou nulle.
alighting_duration | entier | Optionnel | Durée nécessaire au débarquement en secondes (train, avion, ferry, etc.). Cette valeur est obligatoirement positive ou nulle.
stop_id | chaine | Requis | Identifiant de l'arrêt physique de passage (cas général). Ce champ peut également référencer une "zone géographique" (stop de type 2) ou une commune (stop de type 3) dans le cas de TAD zonal.
stop_sequence | entier | Requis | Ordre de passage de desserte dans la circulation. Cette valeur est obligatoirement positive ou nulle, et doit être strictement croissante.
stop_headsign | chaine | Optionnel | Libellé qui doit être affiché au voyageur à la place du `trip_headsign` à cet arrêt.
trip_short_name_at_stop | chaine | Optionnel | Nom qui doit être affiché au voyageur à la place du `trip_short_name` à cet arrêt.
pickup_type | entier (1) | Optionnel | Indication sur l'horaire (issues du gtfs)
drop_off_type | entier (1) | Optionnel | Indication sur l'horaire (issues du gtfs)
local_zone_id  | entier | Optionnel | identifiant de la zone d'ITL de l'horaire
stop_time_precision | entier (2) | Optionnel | Précise si l'heure de passage est fiable ou si elle est donnée à titre indicative

    (1) Indication sur l'horaire (issues du gtfs) :
        0 (par défaut) - Horaire régulier
        1 - Montée ou descente interdite
        2 - Horaire sur réservation associé à un TAD (si un message est associé au TAD, voir la liaison avec [`comment_links.txt`](#comment_linkstxt-optionnel))
        3 - Le véhicule ne s'arrête pas (point de passage); dans ce cas, `pickup_type` et `drop_off_type` doivent tous les deux avoir la même valeur 3

    (2) La fiabilité peut prendre les valeurs suivantes :
        0 - L'heure de passage est fiable, i.e. l'horaire est exact.
        1 - L'heure de passage est approximative (correspond au cas du GTFS où timepoint vaut 0), e.g. un horaire régulier mais qui n'est pas associé à un arrêt de régulation.
        2 - L'heure de passage n'est pas garantie, e.g. l'horaire estimé d'un TAD.
        non spécifiée :
            s'il s'agit d'un horaire associé à une zone (stop de location_type de valeur 2) : l'heure n'est pas garantie
            sinon, l'heure de passage est fiable

### transfers.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
from_stop_id | chaine | Requis | Identifiant de l'arrêt de l’origine de la correspondance (lien vers le fichier [`stops.txt`](#stopstxt-requis))
to_stop_id | chaine | Requis | Identifiant de l'arrêt de la destination de la correspondance (lien vers le fichier [`stops.txt`](#stopstxt-requis))
min_transfer_time | entier | Optionnel | Durée minimale de la correspondance en secondes. Cette valeur correspond à la durée de marche à pied qui sera affichée dans les médias. Si la valeur n'est pas spécifié, le système calcul un temps minimum sur la base de la distance Manhattan entre les deux arrêts. La valeur automatique alors calculée a une valeur minimum de 60 secondes. Note : Il est possible que la valeur fournie soit inferieur à 60 (ex : 0 dans le cas d'une correspondance garantie)
real_min_transfer_time | entier | Optionnel | Durée réelle de correspondance en secondes. Cette valeur correspond à la durée de marche à pied (min_transfer_time) à laquelle on ajoute une durée de tolérance d'exécution (temps minimum de correspondance). Si la valeur n'est pas spécifié, le système utilise (en plus du min_transfer_time) un paramètre par défaut qui est de 120 secondes en général.  La valeur automatique alors calculée sera donc supérieur ou égale à 120 secondes. La valeur saisie ne peut être inférieure à min_transfer_time (mais peut-être égale).
equipment_id | string | Optionnel | Identifiant de description des propriétés (lien vers le fichier [`equipments.txt`](#equipmentstxt-optionnel))

### trip_properties.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
trip_property_id | chaine | Requis | Identifiant de la propriété |
wheelchair_accessible | entier (1) | Optionnel | Le véhicule est accessible aux UFR |
bike_accepted | entier (1) | Optionnel | Le véhicule permet l'embarquement de vélo
air_conditioned | entier (1) | Optionnel | Le véhicule dispose de l'air conditionné
visual_announcement | entier (1) | Optionnel | Le véhicule dispose d'annonces visuelles
audible_announcement | entier (1) | Optionnel | Le véhicule dispose d'annonces sonores
appropriate_escort | entier (1) | Optionnel | Un service d'accompagnement à bord est possible (à la montée et à la descente)
appropriate_signage | entier (1) | Optionnel | L'affichage à bord est est claire et adapté aux personnes en déficience mentale
school_vehicle_type | entier (2) | Optionnel | Type de transport scolaire

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
route_id | chaine | Requis | Identifiant du parcours (lien vers le fichier [`routes.txt`](#routestxt-requis))
service_id | chaine | Requis | Identifiant dues jours de fonctionnements
trip_id | chaine | Requis | Identifiant de la circulation
trip_headsign | chaine | Optionnel | Texte affiché au voyageur sur le vehicule (par exemple la destination du bus ou le code mission du RER)
trip_short_name | chaine | Optionnel | Nom de la circulation connu du voyageur permettant de l'identifier de manière unique sur la journée (en général un numéro de train)
block_id | chaine | Optionnel | Identifiant du prolongement de service
company_id | chaine | Requis | Identifiant de la compagnie (lien vers le fichier [`companies.txt`](#companiestxt-requis))
physical_mode_id | chaine | Requis | Identifiant du mode physique (lien vers le fichier [`physical_modes.txt`](#physical_modestxt-requis))
trip_property_id | chaine | Optionnel | Identifiant de la propriété accessibilité (lien vers le fichier [`trip_properties.txt`](#trip_propertiestxt-optionnel))
dataset_id | chaine | Requis | Identifiant du jeu de données ayant fourni la circulation (lien vers le fichier [`datasets.txt`](#datasetstxt-requis)).
geometry_id | chaine | Optionnel | Identifiant du tracé représentant la circulation (lien vers le fichier [`geometries.txt`](#geometriestxt-optionnel))
journey_pattern_id | chaine | Optionnel | Identifiant de la mission (i.e. une séquence ordonnée d'arrêts ayant les mêmes propriétés et parfois connue du voyageur)

    Pour préciser si la circulation est sur réservation (tout ou partie), il faut :
        Indiquer au niveau de l'horaire (fichier [`stop_times.txt`](#stop_timestxt-requis)) si la montée et/ou la descente est à réservation
        Indiquer un commentaire (optionnel) de type TAD via les fichiers [`comments.txt`](#commentstxt-optionnel) et [`comment_links.txt`](#comment_linkstxt-optionnel) ou via l'extension [`booking_rules.md`](https://github.com/hove-io/ntfs-specification/blob/master/extensions/booking_rules.md)

### geometries.txt (optionnel)
Ce fichier contient la représentation spatiale d'un objet géométrique (Tracé de ligne, de parcours et/ou de circulation, forme d'une zone d'arrêt). Chaque ligne du fichier représente une géométrie complète de l'objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
geometry_id | chaine | Requis | Identifiant de la géométrie.
geometry_wkt | géométrie | Requis | Représentation spatiale de la géométrie selon le standard http://fr.wikipedia.org/wiki/Well-known_text.

    Les lignes et parcours peuvent être des LINESTRING ou des MULTILINESTRING.
    Les circulations ne peuvent être que des LINESTRING. Si une MULTILINESTRING est spécifiée, seule la première LINESTRING sera utilisée.
    Les points d'arrêts sont des POINT.
    Les zones d'arrêt peuvent être des POINT, POLYGON ou MULTIPOLYGON.
    Les zones géographiques et communes peuvent être des MULTIPOLYGON. Par exemple, pour définir une zone d'achalandage de TAD zonal, il convient de décrire la zone dans le fichier stops.txt en utilisant une "location_type=2", et de lui associer une geometry suivant le format `MULTIPOLYGON(((2.86185260296 42.69555802563,2.85206790447 42.6934131755,.... )))`

    Seules les types de géométries spécifiées sont retenues, les autres types géométries sont ignorées.

### object_properties.txt (optionnel)
Ce fichier contient la description des propriétés complémentaires sur les différents objets du référentiel.
Ces propriétés sont sous forme de liste de clés / valeurs qui doivent être standardisées par processus.
Une clé ne peut être utilisée qu'une seule fois (avec une seule valeur) pour un même objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_type | chaine | Requis | Type d'objet sur lequel la propriété porte (line, route, trip, stop_area, stop_point)
object_id | chaine | Requis | Identifiant de l'objet sur lequel la propriété porte
object_property_name | chaine | Requis | Nom de la propriété complémentaire (texte libre)
object_property_value | chaine | Requis | Valeur de la propriété complémentaire (texte libre)

### object_codes.txt (optionnel)
Ce fichier contient la liste des codes d'identification complémentaires dans les systèmes externes des différents objets du référentiel.
Ces propriétés sont sous forme de liste de clés / valeurs qui doivent être standardisées par processus.
Une clé peut être utilisée plusieurs fois (avec des valeurs différentes) pour un même objet.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
object_type | chaine | Requis | Type d'objet sur lequel la propriété porte (company, network, line, route, trip, stop_area, stop_point)
object_id | chaine | Requis | Identifiant de l'objet sur lequel la propriété porte
object_system | chaine | Requis | Nom du système d'identification de l'objet  (texte libre). Par exemple : "Timeo" ou "UIC" pour les arrêts, "Reflex" pour les lignes.
object_code | chaine | Requis | Code d'identification de l'objet dans le système considéré.

Hove fournit dans ce fichier :
* les identifiants des objets dans l'ancien système Navitia pour les objets "network", "line", "route", "trip", "stop_point" et "stop_area" avec pour object_system la chaine **"navitia1"**.
* les identifiants des objets déclarés dans la source d'alimentation (NTFS ou GTFS par exemple) avec pour object_system la chaine **"source"**.

### admin_stations.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
admin_id | chaine | Requis | Identifiant de la commune (ou du quartier) tel que retourné par l'API Navitia
admin_name | chaine | Requis | Nom de la commune (ou quartier).
stop_id | chaine | Requis | Identifiant de la zone d'arrêt utilisée comme accroche de la commune (lien vers le fichier [`stops.txt`](#stopstxt-requis)). Stop de type 1 oligatoirement.
stop_name | chaine | Optionnel | Nom de la zone d'arrêt (pour faciliter la lisibilité du fichier)

### pathways.txt (optionnel)
Attention, ce fichier décrit une modélisation de la station qui n'est pas nécessairement géographique, et peut également être simplifiée.
La modélisation des chemins d'une zone d'arrêt ne peut pas être partielle. Dès qu'un chemin est renseigné pour une zone d'arrêt, il est considéré que toute la station est renseignée.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
pathway_id | chaine | Requis | Identifiant du chemin
from_stop_id | chaine | Requis | Identifiant noeud de début du chemin dans le fichier [`stops.txt`](#stopstxt-requis). Ce noeud de départ peut être un point d'arrêt, une entrée/sortie, un noeud générique ou une zone d'embarquement.
to_stop_id | chaine | Requis | Identifiant noeud de fin du chemin (même contraintes que `from_stop_id`).
pathway_mode | entier(1) | Requis | Type de chemin. voir ci-dessous pour les valeurs possibles.
is_bidirectional | booléen | Requis | Indique si le chemin est utilisable dans les deux sens ou uniquement dans le sens from->to.
length | décimal | Optionnel | Distance en mètres entre les deux extrémités du chemin
traversal_time | entier | Optionnel | Temps moyen de parcours en secondes.
stair_count | entier | Optionnel | Nombre de marches (approximatif).
max_slope | décimal | Optionnel | Ratio maximum de la pente sur ce chemin.
min_width | décimal | Optionnel | Largeur minimale de ce chemin
signposted_as | chaine | Optionnel | Texte indiqué au voyageur indiquant ce chemin
reversed_signposted_as | chaine | Optionnel | Texte indiqué au voyageur dans le sens inverse (si le chemin est indiqué comme bidirectionnel)

    (1) pathway_mode - Les valeurs possibles sont :
        1 - Couloir
        2 - Escalier
        3 - Trottoir roulant / travelator
        4 - Escalier mécanique
        5 - Ascenseur
        6 - Entrée dans une zone payante ("fare gate")
        7 - Sortie de zone payante ("exit gate")


### levels.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
level_id | chaine | Requis | Identifiant du niveau
level_index | décimal | Requis | Numéro de l'étage, le rez-de-chaussée est indiqué à 0, les étages sous le sol sont avec une valeur négative.
level_name | chaine | Optionnel | Nom associé au niveau (comme par exemple "Mezzanine").

### addresses.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
address_id | chaine | Requis | Identifiant de l'adresse
street_name | chaine | Requis | Nom de la voierie
house_number | chaine | Optionnel | Numéro du seuil
admin_level_8_id | chaine | Optionnel | Identifiant de l'administration de niveau 8 dans le fichier `administrative_regions.txt`
admin_level_9_id | chaine | Optionnel | Identifiant de l'administration de niveau 9 dans le fichier `administrative_regions.txt`
admin_level_10_id | chaine | Optionnel | Identifiant de l'administration de niveau 10 dans le fichier `administrative_regions.txt`

### administrative_regions.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
admin_id | chaine | Requis | Identifiant de la région administrative
admin_name | chaine | Optionnel | Nom de la région administrative
admin_label | chaine | Optionnel | Label de la région administrative
admin_level | entier | Optionnel | Niveau de la région administrative
admin_insee | chaine | Optionnel | Code INSEE
admin_zip_codes | chaine | Optionnel | Liste de code postaux
admin_lon | décimal | Optionnel | Longitude de la région administrative
admin_lat | décimal | Optionnel | Latitude de la région administrative

### occupancies.txt (optionnel) [**expérimental**]
La notion d'affluence est appliquée sur l'intervalle entre 2 horaires (affluence à bord du véhicule).
Pour chaque ligne de ce fichier, l'affluence est appliquée à tous les intervalles d'une circulation entre deux horaires (successifs ou non) remplissant l'ensemble des conditions suivantes :

* le premier des intervalles débute à l'arrêt `from_stop_area` et le dernier des intervalles se termine l'arrêt `to_stop_area` (inclus)
* le jour de circulation est inclus entre `from_date` et `to_date` (inclus)
* le jour de semaine de la circulation est actif (voir champs `monday`, `tuesday`, etc.)
* le `departure_time` du premier intervalle et le `arrival_time` du dernier intervalle sont inclus entre `from_time` et `to_time` (inclus)
* il est possible de monter dans le véhicule à l'arrêt `from_stop_area`
* il est possible de descendre du véhicule à l'arrêt `to_stop_area`

Les lignes du fichier sont appliquées dans l'ordre.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_id | chaine | Requis | Identifiant de la ligne concernées
from_stop_area | chaine | Requis | Identifiant de la zone d'arrêt à partir de laquelle s'applique l'affluence
to_stop_area | chaine | Requis | Identifiant de la zone d'arrêt jusqu'à laquelle s'applique l'affluence
from_date | date | Requis | Date à partir de laquelle s'applique l'affluence
to_date | date | Requis | Date jusqu'à laquelle s'applique l'affluence
from_time | heure | Requis | Heure à partir de laquelle s'applique l'affluence, appliquée sur chacun des jours entre `from_date` et `to_date`
to_time | heure | Requis | Heure jusqu'à laquelle s'applique l'affluence, appliquée sur chacun des jours entre `from_date` et `to_date`
occupancy | occupancy (1) | Requis | L'affluence à appliquer
monday | booléen | Optionnel | Indique si l'affluence s'applique sur les lundis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
tuesday | booléen | Optionnel | Indique si l'affluence s'applique sur les mardis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
wednesday | booléen | Optionnel | Indique si l'affluence s'applique sur les mercredis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
thursday | booléen | Optionnel | Indique si l'affluence s'applique sur les jeudis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
friday | booléen | Optionnel | Indique si l'affluence s'applique sur les vendredis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
saturday | booléen | Optionnel | Indique si l'affluence s'applique sur les samedis entre `from_date` et `to_date`. L'affluence s'applique par défaut.
sunday | booléen | Optionnel | Indique si l'affluence s'applique sur les dimanches entre `from_date` et `to_date`. L'affluence s'applique par défaut.

(1) la valeur est une énumération basée sur les niveaux d'affluence définis dans le GTFS-RT (voir [`OccupancyStatus`](https://developers.google.com/transit/gtfs-realtime/reference/#enum-occupancystatus):

* EMPTY
* MANY_SEATS_AVAILABLE
* FEW_SEATS_AVAILABLE
* STANDING_ROOM_ONLY
* CRUSHED_STANDING_ROOM_ONLY
* FULL
* NOT_ACCEPTING_PASSENGERS
* NO_DATA_AVAILABLE
* NOT_BOARDABLE

### line_groups.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_group_id | chaine | Requis | Identifiant du groupe de ligne
line_group_name | chaine | Requis | Nom du groupe de ligne
main_line_id | chaine | Requis | Identifiant de la ligne principale du groupe de lignes (lien vers le fichier [`lines.txt`](#linestxt-requis))

Un commentaire peut être associé à un groupe de lignes dans les fichiers [`comments.txt`](#commentstxt-optionnel) et et [`comment_links.txt`](#comment_linkstxt-optionnel) .

### line_group_links.txt (optionnel)
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
line_group_id | chaine | Requis | Identifiant du groupe de ligne
line_id | chaine | Requis | Identifiant de la ligne faisant partie du groupe de lignes (lien vers le fichier [`lines.txt`](#linestxt-requis)). Attention, une ligne peut faire partie de plusieurs groupes de lignes.

### feed_infos.txt (requis)
Ce fichier contient des informations sur le jeu de données et le système amont qui l'a généré. Pour faciliter son utilisation, la structure du fichier est générique, et la liste des informations est listée ci-dessous.

#### Description du format du fichier
Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
feed_info_param | chaine | Requis | Nom du paramètre
feed_info_value | chaine | Requis | Valeur du paramètre

#### Description du contenu du fichier

Ce fichier contient 3 types de paramètres :
* des paramètres obligatoires
* des paramètres recommandés (optionnels)
* des paramètres libres (possibilité d'ajouter autant de paramètre que souhaité)

Le tableau ci-dessous liste les paramètres obligatoires et recommandés.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
ntfs_version | chaine | Requis | Version du format NTFS utilisé dans l'export (par exemple : "0.3")
feed_start_date | date | Optionnel | Date de début de validité du jeu de données
feed_end_date | date | Optionnel | Date de fin de validité du jeu de données
feed_creation_date |  date |  Optionnel | Date (UTC) de génération du jeu de données
feed_creation_time | heure | Optionnel | Heure (UTC) de génération du jeu de données
feed_creation_datetime | instant | Optionnel | Date et heure (UTC) de génération du jeu de données

Le tableau ci-dessous indique les paramètres libres renseignés par Hove (dépend de l'outil qui génère les données).

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

### grid_calendars.txt (optionnel)
Ce fichier contient les calendriers.

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

### grid_exception_dates.txt (optionnel)
Ce fichier contient les exceptions sur les calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | string | Requis | Identifiant du calendrier de grille horaire
date | date | Requis | Date de l'exception
type | entier | Requis | 0 : Ne circule pas ce jour <br> 1 : Circule ce jour

### grid_periods.txt (optionnel)
Ce fichier contient les périodes des calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | chaine | Requis | Identifiant du calendrier de grille horaire
start_date | date | Requis | Date de début
end_date | date | Requis | Date de fin

### grid_rel_calendar_line.txt (optionnel)
Ce fichier contient toutes les relations entre les lignes et les calendriers des grilles horaires.

Colonne | Type | Contrainte | Commentaire
--- | --- | --- | ---
grid_calendar_id | chaine | Requis | Identifiant du calendrier de grille horaire
line_id | chaine | Requis | Identifiant de la ligne associée à ce calendrier (lien vers le fichier [`lines.txt`](#linestxt-requis)). Ce champ peut être vide si le champ line_external_code est renseigné.
line_external_code | chaine | Requis | cette colonne contient le code externe NAViTiA 1 de la ligne (lien vers le fichier [`lines.txt`](#linestxt-requis)). Ce champ peut être vide si le champ line_id et renseigné

# Évolutions possibles du format
Ce chapitre liste des évolutions du format qui peuvent être intéressantes si elles sont utiles concrètement.
## Modification du champ service_id et du nom du fichier [`calendar.txt`](#calendartxt-requis)
L'identifiant d'un calendrier ne suit pas la même convention que les autres identifiants (même s'il est cohérent avec le GTFS). Une évolution possible serait de changer le libellé du champ pour indiquer "calendar_id".
De plus, le nom du fichier pourra être changé en `calendars.txt` pour améliorer la cohérence.

## Gestion des données perturbées / de grèves
Afin de limiter la complexité du format, la gestion des données de grève sera effectuée par plusieurs exports :

1. un export contenant toutes les données théoriques du référentiel. L'export NTFS est un export classique, et dont la clé "revised_networks" du fichier [`feed_infos.txt`](#feed_infostxt-requis) est vide ou non renseignée.
2. un ou plusieurs exports NTFS de grèves, dont chaque export fournit toutes les données (impactées par la grève ou non) :
    * de un ou plusieurs réseaux spécifiés par la clé **revised_networks** du fichier [`feed_infos.txt`](#feed_infostxt-requis)
    * pour des données valides entre les dates spécifiées par **feed_start_date** et **feed_end_date**

Un champ complémentaire et optionnel "base_trip_id" est à prévoir dans le fichier [`trips.txt`](#tripstxt-requis) afin de permettre d'associer la circulation théorique et la circulation adaptée (en cas de données de grève par exemple).

## Gestion avancée des géométries (tracés des lignes, parcours et circulations)
Afin de ne pas complexifier inutilement le format NTFS et les outils qui vont le manipuler, le fichier [`geometries.txt`](#geometriestxt-optionnel) indique un tracé complet pour une géométrie, comme une ligne en fourche ou une ligne à tiroir.  Afin de pouvoir afficher le tracé réel des bus dans la feuille de route (ie. n'avoir que la portion utilisée de la ligne), un découpage de cette géométrie est réalisé dans Navitia de manière automatique.
Si le besoin d'affiner cette gestion est validé, une évolution du format du fichier [`geometries.txt`](#geometriestxt-optionnel) peut être envisagé de la manière suivante (à confirmer) :
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
Voici quelques exemples de modélisation de TAD dans les fichiers NTFS. Seuls les fichiers impactés sont représentés ([`stops.txt`](#stopstxt-requis) et [`stop_times.txt`](#stop_timestxt-requis)).
Il est à également à noter qu'il est possible :
* de faire du rabattement vers horaire en spécifiant bien un horaire à un point d'arrivé (et pas une zone) dans le ficheir stop_time,
* de faire de la fréquence sur du TAD zonal en utilisant le fichier [`frequencies.txt`](#frequenciestxt-optionnel)

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
* Les stop_point du fichier [`stops.txt`](#stopstxt-requis) de type "shape", ne devraient pas être en correspondance avec d'autres stop_point.
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
On peut alimenter le fichier [`stop_times.txt`](#stop_timestxt-requis) en mettant des horaires précis sur chacun des points d'arrêt pouvant appartenir à des zones d'arrêts différentes, avec des informations ITL manuelles:

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
zone_2 | Zone 1 | 47.01 | 1.01 | 2 | id_vers_MULTIPOLYGON((1 1,5 1,5 5,1 5,1 1)) |

**Fichier [`stop_times.txt`](#stop_timestxt-requis): déclare les "horaires", estimés ou non**

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
