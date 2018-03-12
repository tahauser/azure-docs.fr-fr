---
title: "Référence des paramètres d’itinérance Windows 10 | Microsoft Docs"
description: "Liste complète de tous les paramètres destinés à l’itinérance ou à la sauvegarde dans Windows 10."
services: active-directory
keywords: "Enterprise State Roaming, Cloud windows"
documentationcenter: 
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 21d21c945b622c1695d8856c4baff02c098218cf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="windows-10-roaming-settings-reference"></a>Référence des paramètres d’itinérance Windows 10
Voici une liste complète de tous les paramètres destinés à l’itinérance ou à la sauvegarde dans Windows 10. 

## <a name="devices-and-endpoints"></a>Appareils et points de terminaison
Consultez le tableau suivant pour obtenir un récapitulatif des périphériques et des types de comptes pris en charge par la structure de synchronisation, de sauvegarde et de restauration framework dans Windows 10.

| Type de compte et fonctionnement | Bureau | Mobile |
| --- | --- | --- |
| Azure Active Directory : synchronisation |Oui |Non |
| Azure Active Directory : sauvegarde/restauration |Non |Non |
| Compte Microsoft : synchronisation |Oui |Oui |
| Compte Microsoft : sauvegarde/restauration |Non |Oui |

## <a name="what-is-backup"></a>Qu’est-ce qu’une sauvegarde ?
En règle générale, les paramètres Windows se synchronisent par défaut, cependant, certains paramètres sont simplement sauvegardés, notamment la liste des applications installées sur un périphérique. La sauvegarde est destinée uniquement aux appareils mobiles et n’est pas disponible pour les utilisateurs d’Enterprise State Roaming. La sauvegarde utilise un compte Microsoft et stocke les paramètres et données d’application dans OneDrive. Si, sur le périphérique , un utilisateur désactive la synchronisation avec l’application Settings, les données de l’application normalement synchronisées se transforment en simple sauvegarde. Les données de sauvegarde sont uniquement accessibles via l’opération de restauration lors de la première utilisation d’un nouvel appareil. Les sauvegardes peuvent être désactivées via les paramètres du périphérique et peuvent être gérées et supprimées via le compte OneDrive de l’utilisateur.

## <a name="windows-settings-overview"></a>Vue d’ensemble des paramètres Windows
Les groupes de paramètres suivants sont disponibles pour les utilisateurs finaux et servent à activer/désactiver la synchronisation des paramètres sur les appareils Windows 10.

* Thème : Arrière-plan du Bureau, Vignette de l’utilisateur, Position de la barre des tâches, etc. 
* Paramètres Internet Explorer : Historique de navigation, URL saisies, Favoris, etc. 
* Mots de passe : [Stockage sécurisé des informations d’identification Windows](https://technet.microsoft.com/library/jj554668.aspx), et notamment des profils Wi-Fi 
* Préférences linguistiques : Dictionnaire, Paramètres de langue système 
* Options d’ergonomie : Narrateur, Loupe, Clavier visuel 
* Autres paramètres Windows : consultez les détails de Paramètres Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)

La synchronisation de groupes de paramètres pour le navigateur Microsoft Edge (favoris, liste de lecture) peut être activée ou désactivée par les utilisateurs finaux dans le menu Paramètres du navigateur Microsoft Edge.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-sync-content.png)

## <a name="windows-settings-details"></a>Détails des Paramètres Windows
Dans le tableau qui suit, les autres entrées de la colonne Groupe de paramètres font référence aux paramètres pouvant être désactivés en accédant à Paramètres > Comptes > Synchroniser vos paramètres > Autres Paramètres Windows. 

Les entrées internes dans la colonne Groupe de paramètres font référence aux paramètres et aux applications qui ne peuvent être désactivées de la synchronisation qu’au sein de l’application elle-même ou par la désactivation de l’ensemble du périphérique à l’aide des paramètres Gestion des périphériques mobiles ou des paramètres de stratégie de groupe.
Les paramètres non itinérants ou la synchronisation n’appartiennent pas à un groupe.

| Paramètres | Bureau | Mobile | Groupe |
| --- | --- | --- | --- |
| **Comptes**: avatar du compte |sync |X |Thème |
| **Comptes**: autres paramètres de compte |X |X | |
| **Bande passante mobile avancée**: nom de réseau de partage de connexion Internet (active la détection automatique des points d’accès Wi-Fi mobiles Bluetooth) |X |X |Mot de passe |
| **Données d’application**: les applications individuelles peuvent synchroniser des données |sauvegarde de synchronisation |sauvegarde de synchronisation |Interne |
| **Liste d’applications**: liste des applications installées |X |backup |Autres |
| **Bluetooth**: tous les paramètres Bluetooth |X |X | |
| **Invite de commandes** : paramètres Defaults de l’invite de commandes |sync |X | |
| **Informations d’identification**: stockage sécurisé des informations d’identification |sync |sync |password |
| **Date, heure et région**: horaire automatique (synchronisation Internet) |sync |sync |language |
| **Date, heure et région**: 24 heures |sync |X |language |
| **Date, heure et région**: date et heure |sync |X |language |
| **Date, heure et région**: fuseau horaire | |X |language |
| **Date, heure et région**: heure d’été |sync |X |language |
| **Date, heure et région**: pays/région |sync |X |language |
| **Date, heure et région**: premier jour de la semaine |sync |X |language |
| **Date, heure et région**: format régional (paramètres régionaux) |sync |X |language |
| **Date, heure et région**: date courte |sync |X |language |
| **Date, heure et région**: date longue |sync |X |language |
| **Date, heure et région**: heure courte |sync |X |language |
| **Date, heure et région**: heure complète |sync |X |language |
| **Personnalisation du bureau**: thème du bureau (arrière-plan, couleur système, sons système par défaut, écran de veille) |sync |X |Thème |
| **Personnalisation du bureau**: papier peint en diaporama |sync |X |Thème |
| **Personnalisation du bureau**: paramètres de la barre des tâches (position, masquage automatique, etc.) |sync |X |Thème |
| **Personnalisation du bureau**: disposition de l’écran d’accueil |X |backup | |
| **Appareils**: imprimantes partagées auxquelles vous êtes connectées |X |X |Autres |
| **Navigateur Microsoft Edge**: liste de lecture |sync |sync |Interne |
| **Navigateur Microsoft Edge**: favoris |sync |sync |Interne |
| **Navigateur Edge** : sites les plus consultés <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : URL entrées <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : paramètres de la barre des favoris <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : afficher le bouton Accueil <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : bloquer les fenêtres publicitaires <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : me demander ce qu’il faut faire avec chaque téléchargement <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : proposer d’enregistrer les mots de passe <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : envoyer les demandes Ne pas me suivre <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : enregistrer les entrées des formulaires <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : afficher les suggestions de recherche et de site au fil de la frappe <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : préférences pour les cookies <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : permettre aux sites d’enregistrer les licences des médias protégés sur mon appareil <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Navigateur Edge** : paramètre du lecteur d’écran <sup>[[1]](#footnote-1)</sup> |sync |sync |Interne |
| **Contraste élevé**: activé ou désactivé |sync |X |options d’ergonomie |
| **Contraste élevé**: paramètres du thème |sync |X |options d’ergonomie |
| **Internet Explorer**: ouvrir les onglets (URL et titre) |sync |sync |Internet Explorer |
| **Internet Explorer**: liste de lecture |sync |sync |Internet Explorer |
| **Internet Explorer**: URL saisies |sync |sync |Internet Explorer |
| **Internet Explorer**: historique de navigation |sync |sync |Internet Explorer |
| **Internet Explorer**: favoris |sync |sync |Internet Explorer |
| **Internet Explorer**: URL exclues |sync |sync |Internet Explorer |
| **Internet Explorer**: pages d’accueil |sync |sync |Internet Explorer |
| **Internet Explorer**: suggestions de domaine |sync |sync |Internet Explorer |
| **Clavier**: les utilisateurs peuvent activer/désactiver le clavier visuel |sync |X |options d’ergonomie |
| **Clavier**: activer un oui rémanent (désactivé par défaut) |sync |X |options d’ergonomie |
| **Clavier**: activer les clés de filtre (désactivé par défaut) |sync |X |options d’ergonomie |
| **Clavier**: activer les touches bascules (désactivé par défaut) |sync |X |options d’ergonomie |
| **Internet Explorer**: langue du domaine : chinois (CHS) QWERTY – activer l’apprentissage automatique |sync |X |language |
| **Langue**: QWERTY CHS - activer le classement de candidats dynamique |sync |X |language |
| **Langue**: QWERTY CHS - jeu de caractères chinois simplifié |sync |X |language |
| **Langue**: QWERTY CHS - jeu de caractères chinois traditionnel |sync |X |language |
| **Langue**: QWERTY CHS - pinyin approximatif |sync |backup |Langage |
| **Langue**: QWERTY CHS - paires approximatives |sync |backup |Langage |
| **Langue**: QWERTY CHS - pinyin complet |sync |X |language |
| **Langue**: QWERTY CHS - pinyin double |sync |X |language |
| **Langue**: QWERTY CHS - lecture de correction automatique |sync |X |language |
| **Langue**: QWERTY CHS - touche bascule C/E, MAJ |sync |X |language |
| **Langue**: QWERTY CHS - touche bascule C/E, Ctrl |sync |X |language |
| **Langue**: WUBI CHS - mode de saisie de caractère unique |sync |X |language |
| **Langue**: WUBI CHS - afficher le codage de candidats restant |sync |X |language |
| **Langue**: WUBI CHS - émission d’un bip sonore lorsque le codage des 4 coins est non valide |sync |X |Langage |
| **Langue** : Bopomofo CHS - inclure CJK Ext-A |sync |X |language |
| **Langue**: IME japonais - frappe prédictive et mots personnalisés |sync |sync |language |
| **Langue**: coréen (KOR) IME |X |X |language |
| **Langue**: reconnaissance d’écriture manuscrite |X |X |language |
| **Langue**: profil de langue |sync |backup |language |
| **Langue**: vérification orthographique et mise en évidence des fautes d’orthographe |sync |backup |language |
| **Langue**: liste de claviers |sync |backup |language |
| **Écran de verrouillage**: tous les paramètres de verrouillage |X |X | |
| **Loupe**: activer ou désactiver (bascule maître) |X |X |options d’ergonomie |
| **Loupe**: activer ou désactiver les couleurs d’inversion (désactivé par défaut) |sync |X |options d’ergonomie |
| **Loupe**: suivre les éléments sélectionnés au clavier |sync |X |options d’ergonomie |
| **Loupe**: suivre le curseur de la souris |sync |X |options d’ergonomie |
| **Loupe**: démarrer lorsque les utilisateurs se connectent (désactivé par défaut) |sync |X |options d’ergonomie |
| **Souris**: modifier la taille du curseur de la souris |sync |X |Autres |
| **Souris**: modifier la couleur du curseur de la souris |sync |X |Autres |
| **Souris**: tous les autres paramètres |X |X | |
| **Narrateur**: lancement rapide |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent modifier la tonalité du narrateur |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent activer ou désactiver les conseils de lecture du narrateur pour les éléments communs (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent l’activer ou le désactiver s’ils peuvent entendre des caractères tapés (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent décider d’activer ou de désactiver le narrateur s’ils entendent les mots (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: insérer le curseur après le Narrateur (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: activer la mise en évidence visuelle du curseur du Narrateur (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: lire les pistes audio (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: activer les touches du clavier tactile lorsque vous levez le doigt (désactivé par défaut) |sync |X |options d’ergonomie |
| **Options d’ergonomie**: définir l’épaisseur du curseur clignotant |sync |X |options d’ergonomie |
| **Options d’ergonomie**: supprimer des images d’arrière-plan (désactivé par défaut) |sync |X |options d’ergonomie |
| **Alimentation et mise en veille**: tous les paramètres |X |X | |
| **Personnalisation de l’écran d’accueil** : couleur d’accentuation (téléphone uniquement) |X |sync |Thème |
| **Frappe**: dictionnaire |sync |backup |language |
| **Frappe**: correction automatique du mot mal orthographié |sync |backup |language |
| **Frappe**: mettre en surbrillance les mots mal orthographiés |sync |backup |language |
| **Frappe**: afficher les suggestions de texte en cours de frappe |sync |backup |language |
| **Frappe**: ajouter un espace après la sélection d’une suggestion de texte |sync |backup |language |
| **Frappe**: ajouter un point après un double-clic sur la barre d’espace |sync |backup |language |
| **Frappe**: mettre une majuscule sur la première lettre de chaque phrase |sync |backup |language |
| **Frappe**: utiliser des lettres majuscules lorsque je double-clique sur la touche Maj |sync |backup |language |
| **Frappe**: les touches émettent des sons pendant la frappe |sync |backup |language |
| **Frappe**: données de personnalisation pour le clavier tactile |sync |backup |language |
| **Wi-Fi**: profils Wi-Fi (WPA uniquement) |sync |sync |Mot de passe |

###### <a name="footnote-1"></a>Note de bas de page 1
Version minimale du système d’exploitation prise en charge : Windows Creators Update (Build 15063). 

## <a name="related-topics"></a>Rubriques connexes
* [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Dépannage](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
