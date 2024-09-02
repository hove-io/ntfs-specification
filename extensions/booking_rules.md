NTFS - Extension transport à la demande (TAD)
=============================================

# Introduction

Ce document vient compléter les spécifications [ntfs_fr](../ntfs_fr.md) pour y ajouter des règles liées à la réservation de Transport à la Demande (ou ODT = On Demand Transportation).

# Liste des fichiers

| Fichier                | Contrainte | Commentaire                                                                                |
| ---------------------- | ---------- | ------------------------------------------------------------------------------------------ |
| booking_rules.txt      | Optionnel  | Ce fichier contient des règles liées à la réservation de TAD                               |
| booking_rule_links.txt | Optionnel  | Ce fichier contient les relations entre chaque règle et les objets du référentiel associés |

## booking_rules.txt (optionnel)

| Colonne         | Type   | Contrainte | Commentaire                                                        |
| --------------- | ------ | ---------- | ------------------------------------------------------------------ |
| booking_rule_id | chaine | Requis     | Identifiant de la règle de réservation TAD                         |
| name            | chaine | Optionnel  | Nom de la règle de réservation                                     |
| info_url        | chaine | Optionnel  | URL d'information pour la réservation                              |
| phone_number    | chaine | Optionnel  | Numéro de téléphone pour la réservation                            |
| message         | chaine | Optionnel  | Conditions de réservation (ex : du lundi au vendredi de 9h à 18h)  |
| booking_url     | chaine | Optionnel  | URL permettant de faire le lien avec une API de réservation de TAD |

### booking_rule_links.txt (optionnel)

Ce fichier fait le lien entre un objet du référentiel (ligne, circulation) et la règle de réservation TAD.

| Colonne         | Type   | Contrainte | Commentaire                                                                                                      |
| --------------- | ------ | ---------- | ---------------------------------------------------------------------------------------------------------------- |
| object_id       | chaine | Requis     | Identifiant de 'objet                                                                                            |
| object_type     | chaine | Requis     | Type de l'objet. Les valeurs possibles sont `line`, `trip`.                                                      |
| booking_rule_id | chaine | Requis     | Identifiant d'une règle de réservation (lien vers le fichier [`booking_rules.txt`](#booking_rulestxt-optionnel)) |
