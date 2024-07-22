NTFS - Extension transport à la demande (TAD)
=============================================

# Introduction

Ce document vient compléter les spécifications [ntfs_fr](../ntfs_fr.md) pour y ajouter des propriétés liées à la réservation d'un TAD.

# Liste des fichiers de tarification

| Fichier                   | Contrainte | Commentaire                                                                                      |
| ------------------------- | ---------- | ------------------------------------------------------------------------------------------------ |
| odt_reservations.txt       | Optionnel  | Ce fichier contient des propriétés liées à la réservation d'un TAD                               |
| odt_reservation_links.txt | Optionnel  | Ce fichier contient les relations entre chaque réservation et les objets du référentiel associés |

## odt_reservations.txt (optionnel)

| Colonne                    | Type   | Contrainte | Commentaire                                                        |
| -------------------------- | ------ | ---------- | ------------------------------------------------------------------ |
| odt_reservation_id         | chaine | Requis     | Identifiant des propriétés liées à une réservation TAD             |
| odt_reservation_name       | chaine | Requis     | Nom de réservation                                                 |
| odt_reservation_url        | chaine | Optionnel  | URL de réservation                                                 |
| odt_reservation_phone      | chaine | Optionnel  | Numéro de téléphone pour la réservation                            |
| odt_reservation_conditions | chaine | Optionnel  | Conditions de réservations (ex : du lundi au vendredi de 9h à 18h) |
| odt_reservation_deeplink   | chaine | Optionnel  | TODO : ajouter une description                                     |

### odt_reservation_links.txt (optionnel)

Ce fichier fait le lien entre un objet du référentiel (ligne, circulation, horaire) et les propriétés d'une réservation TAD.

| Colonne            | Type   | Contrainte | Commentaire                                                                                                              |
| ------------------ | ------ | ---------- | ------------------------------------------------------------------------------------------------------------------------ |
| object_id          | chaine | Requis     | Identifiant de l'objet                                                                                                   |
| object_type        | chaine | Requis     | Type de l'objet. Les valeurs possibles sont `line`, `vehicle_journey`, `stop_time`.                                      |
| odt_reservation_id | chaine | Requis     | Identifiant de propriétés d'un réservation (lien vers le fichier [`odt_reservation.txt`](#odt_reservationstxt-optionnel)) |
