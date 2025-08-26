NTFS - Extension Object Locks
=============================================

Introduction
------------

Ce document vient compléter les spécifications [NTFS](../ntfs_fr.md) pour y ajouter la possibilité de verrouiller des objets d'un NTFS.

Les objets orphelins, c'est-à-dire sans relation avec au moins une circulation, doivent normalement être absents d'un NTFS standard.  
Les verrous de suppression permettent de conserver ces objets malgré leur statut d'orphelin.

Format des données
------------------

Le format du fichier est celui [par défaut](../ntfs_fr.md#format-des-donn%C3%A9es) du NTFS.

Liste des fichiers
-------------------

| Fichier          | Contrainte | Commentaire                                                                   |
| ---------------- | ---------- | ----------------------------------------------------------------------------- |
| object_locks.txt | Optionnel  | Ce fichier contient la liste des objets protégés par un verrou de suppression |

object_locks.txt (optionnel)
----------------------------

Ce fichier permet de définir des verrous sur des objets du NTFS pour les protéger de la suppression automatique lors du nettoyage des objets orphelins.

| Colonne     | Type   | Contrainte | Commentaire                                                                    |
| ----------- | ------ | ---------- | ------------------------------------------------------------------------------ |
| object_id   | chaîne | Requis     | Identifiant de l'objet                                                         |
| object_type | chaîne | Requis     | Type de l'objet. Les valeurs possibles sont `stop_point`, `stop_area`, `line`. |
