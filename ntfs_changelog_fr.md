# Changelog du format NTFS

* Version 0.1 du 15/12/2014 :
    * Ajout de la gestion des codes externes et des propriétés complémentaires génériques par objet
* Version 0.2 du 20/02/2015 :
    * Ajout des heures d'ouverture et de fermeture de la ligne
    * Ajout du taux d'émission de CO2 par mode physique et de la liste exhaustive des modes physiques
    * Ajout du type de circulation (fichier trips) pour permettre de spécifier le type de TAD
    * Ajout du fichier admin_stations.txt
    * Ajout de la gestion du TAD Zonal
        * Ajout d'une URL dans le fichier odt_conditions.txt
        * Ajout dans le fichier stops.txt des zones géographiques et du type de zone
    * Ajout de 2 fichiers sur la gestion des propriétés complémentaires sur les objets
* Version 0.3 du 14/04/2015 :
    * Modification de la gestion du TAD : suppression des zones de TAD arrêt à arrêt
    * Modification du nom de fichier companies.txt
    * Remplacement de la géométrie dans le fichier stops.txt par une liaison vers le fichier de géométries
    * Ajout des modes BSS, car et bike pour la consommation de CO2 des modes de rabattement
    * Ajout d'un fichier feed_infos
    * Création du fichier comment_links pour associer plusieurs commentaires à un objet; inclusion du TAD dans les comment (avec un type) et ajout d'une URL
    * Ajout d'un champ stop_time_id optionnel pour permettre la liaison entre les notes et les horaires
    * Modification des fichiers object_codes et object_properties pour harmoniser le format (séparation de l'id et du type d'objet)
    * Suppression des champs "external_code" car à inclure dans object_codes
* Version 0.4 du 27/04/2015 :
    * Ajout d'une destination principale à la notion de parcours
    * Ajout de la notion de groupe de ligne
* Version 0.5 du 25/10/2015 (version technique, pas de modification effective de format)
    * Gestion du champ line_text_color
    * Gestion du fichier equipments.txt
* Version 0.6 du 24/11/2015
    * Ajout de la gestion des services (fichier frames.txt) et modification des contributeurs pour ajouter une licence et un site web
    * Ajout de la gestion des données adaptées (dans feed_info.txt une clé "revised_networks" listant les réseaux impactés par une grève ou une autre perturbation)
    * Modification de l'identifiant des calendriers et des calendriers de période
    * Modification des commentaires : Ajout d'un label optionnel et modification du nom du champ "comment_name" en "comment_value"
    * Modification du champ "is_forward" en "direction_type" dans le fichier routes.txt et passage en champ de type chaine
* Version 0.6.1 du 25/02/2016
    * "frame" remplacé par "dataset" dans les attributs du fichier frames.txt
    * Fichier frames.txt rennomé à datasets.txt
    * Attribut frame_id remplacé par dataset_id dans le fichier trips.txt
    * Attribut frame_id supprimé dans le fichier stops.txt
* Version 0.6.2 du 20/06/2016
    * ajout de l'exention fares pour la gestion des tarifs
    * ajout des temps d'embarquement et de débarquement dans le fichier stop_times.txt.
* Version 0.6.3 du 25/01/2017
    * ajout du champ dataset_extrapolation dans le fichier datasets.txt
* Version 0.6.4 du 31/03/2017
    * ajout du mode physique RailShuttle pour les VAL
* Version 0.6.5 du 02/01/2018
    * precision sur les arrival/departure times
    * feed_infos.txt est optionnel en 0.6
* Version 0.7.0 du 03/01/2018
    * feed_infos.txt est obligatoire
    * le lien entre "stop" et "contributor" est supprimé (liaison possible via "vehicle_journey")
    * le lien entre "vehicle_journey" et "contributor" est supprimé, et se fait via "dataset"
    * le champ "company_fax" est supprimé et les champs adresses sont fusionnés en un seul champ en texte libre
    * ajout d'un champ "network_address" afin de pouvoir renseigner l'adresse du réseau.
    * les données adaptées (intégrées dans la version 0.6) sont déplacées dans la section "évolutions possibles"
* Version 0.7.1 du 27/02/2018
    * les fichiers contributors.txt et datasets.txt sont obligatoires, ainsi que le lien entre "trip" et "dataset_id"
    * le champ "comment_value" est renommé "comment_name" (conformité avec le code)
    * les valeurs de comment_type changent
* Version 0.7.2 du 30/10/2018
    * dans le fichier stops.txt, suppresion du "location_type = 3" qui n'est pas utilisé
* Version 0.7.3 du 12/11/2018
    * ajout du mode physique SuspendedCableCar pour les Téléphérique / télécabine
* Version 0.7.4 du 21/02/2019
    * modification des propriétés présentes dans le fichier feed_info
* Version 0.8 du 25/03/2019
    * Ajout de la gestion des pathways
* Version 0.9 du 16/05/2019
    * Ajout du nouveau modèle tarifaire
* Version 0.9.1 du 18/07/2019
    * Ajout des `platform_code` dans `stops.txt`
* Version 0.9.2 du 09/09/2019
    * Suppression des champs `forward_direction` et `backward_direction` dans `lines.txt`
* Version 0.10.0 du 11/09/2019
    * Ajout du champ `trip_short_name` et `stop_short_name`
* Version 0.11.0 du 11/10/2019
    * Modification du nom et de la gestion pour le champ `date_time_estimated` dans `stop_times.txt`
* Version 0.11.1 du 03/02/2020
    * Ajout du champ `journey_pattern_id` dans `trips.txt`
    * Amélioration de la description du modèle tarifaire
    * Amélioration de la description des `stop_sequence` dans `stop_times.txt`
    * Ajout d'un graphe du modèle dans la documentation
* Version 0.11.2 du 26/02/2020
    * Amélioration de la description du modèle tarifaire
    * Ajout d'un champ `feed_creation_datetime` recommandé dans `feed_infos.txt`
* Version 0.11.3 du 10/09/2020
    * Amélioration de documentation des champs `network_address`, `stop_timezone` et `feed_creation_date`
* Version 0.11.4 du 27/09/2020
    * Ajout de la gestion des codes externes pour les compagnies
* Version 0.11.5 du 12/04/2021
    * Amélioration de documentation pour `object_properties` et `object_codes`
* Version 0.11.6 du 26/07/2021
    * Amélioration de la documentation pour les `stop_sequence`
* Version 0.12.0 du 19/08/2021
    * Ajout des adresses des arrêts physiques
* Version 0.12.1 du 01/09/2021
    * Les `stop_times` peuvent maintenant être des points de passage (pas d'arrêt du véhicule)
* Version 0.12.2 du 20/10/2021
    * Correction de la documentation sur `date_time_estimated` qui est maintenant `stop_time_precision`
* Version 0.12.3 du 31/05/2022
    * Précision de documentation concernant l'unité utilisée pour le CO2
* Version 0.13.0 du 25/04/2023
    * Ajout du fichier optionnel `occupancies.txt`
* Version 0.14.0 du 29/09/2023
    * le champ `address_id` dans `stops.txt` s'applique désormais à tous les types d'arrêt
    * Ajout de 3 champs dans `addresses.txt`
      * `admin_level_8_id`
      * `admin_level_9_id`
      * `admin_level_10_id`
    * Ajout du fichier `administrative_regions.txt`
* Version 0.15.0 du 06/02/2024
    * Ajout du champ `network_fare_url` dans `networks.txt`
* Version 0.16.0 du 22/07/2024
    * Ajout des fichiers `odt_reservations.txt` et `odt_reservation_links.txt`
* Version 0.16.1 du 27/08/2024
    * Renommage du champ `odt_reservation_condition` dans `odt_reservations.txt`
* Version 0.17.0 du 02/09/2024
    * Renommage et évolution des fichiers `odt_reservations.txt` et `odt_reservation_links.txt` en `booking_rules.txt` et `booking_rule_links` pour rapprochement avec le standard GTFS