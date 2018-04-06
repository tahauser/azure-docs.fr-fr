---
title: Historique de publication des versions du connecteur | Microsoft Docs
description: Cette rubrique répertorie toutes les versions des connecteurs de Forefront Identity Manager (FIM) et Microsoft Identity Manager (MIM).
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.author: davidste
ms.openlocfilehash: 5b13338646abda7eefec44c42dc0159e9338adfa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="connector-version-release-history"></a>Historique de publication des versions du connecteur
Les connecteurs de Forefront Identity Manager (FIM) et Microsoft Identity Manager (MIM) sont fréquemment mis à jour.

> [!NOTE]
> Cette rubrique ne concerne que FIM et MIM. Ces connecteurs ne sont pas pris en charge pour l’installation sur Azure AD Connect. Les connecteurs finaux sont préinstallés sur AADConnect lors de la mise à niveau vers la version spécifiée.


Cette rubrique répertorie toutes les versions des connecteurs qui ont été publiées.

Liens connexes :

* [Télécharger les derniers connecteurs](http://go.microsoft.com/fwlink/?LinkId=717495)
* [connecteur LDAP générique](active-directory-aadconnectsync-connector-genericldap.md)
* [connecteur SQL générique](active-directory-aadconnectsync-connector-genericsql.md)
* [connecteur WebServices](https://docs.microsoft.com/en-us/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws)
* [connecteur PowerShell](active-directory-aadconnectsync-connector-powershell.md)
* [connecteur Lotus Domino](active-directory-aadconnectsync-connector-domino.md)


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Problèmes résolus :
* ConnectorsLog System.Diagnostics.EventLogInternal.InternalWriteEvent(Message : un appareil joint au système ne fonctionne pas) résolu
* Dans cette version de connecteurs, vous devez mettre à jour la redirection de liaison de 3.3.0.0-4.1.3.0 à 4.1.4.0 dans miiserver.exe.config
* Services web génériques :
    * La réponse de JSON valide résolue n’a pas pu être enregistrée dans l’outil de configuration
* SQL générique :
    * L’exportation génère toujours la requête de mise à jour uniquement pour l’opération de suppression. Ajouté pour générer une requête Suppression
    * La requête SQL qui obtient des objets pour l’opération d’importations Delta, si « Stratégie Delta » est « Change Tracking » a été résolue. Dans la limitation connue de cette implémentation : l’importation Delta avec le mode « Change Tracking » ne suit pas les modifications apportées aux attributs à valeurs multiples
    * Il est possible de générer une requête Suppression pour les cas où il est nécessaire de supprimer la dernière valeur d’attribut à valeurs multiples. Cette ligne ne contient pas toutes les autres données à l’exception de la valeur qu’il est nécessaire de supprimer.
    * Gestion de System.ArgumentException lors de l’implémentation de paramètres SORTIE par SP 
    * Requête incorrecte pour effectuer l’opération d’exportation dans le champ qui a le type varbinary (max)
    * Le problème avec la variable parameterList a été initialisé deux fois (dans les fonctions ExportAttributes et GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Problèmes résolus :

* Lotus Notes :
  * Option de filtrage des certificateurs personnalisés.
  * La définition des opérations pouvant être exécutées dans le mode « Vues » et dans le mode « Recherche » dans le cadre de l’importation de la classe ImportOperations a été corrigée.
* LDAP générique :
  * OpenLDAP Directory utilise le nom unique (DN) comme point d’ancrage au lieu de l’attribut entryUUID. Nouvelle option permettant de modifier le point d’ancrage pour le connecteur GLDAP
* SQL générique :
  * Correction de l’exportation dans le champ présentant le type varbinary(max).
  * Lors de l’ajout de données binaires dans un objet CSEntry à partir d’une source de données, la fonction DataTypeConversion échouait avec un nombre d’octets nul. Correction de la fonction DataTypeConversion de la classe CSEntryOperationBase.




### <a name="enhancements"></a>Améliorations :

* SQL générique :
  * La possibilité de configurer le mode d’exécution d’une procédure stockée avec des paramètres nommés ou non nommés a été ajoutée dans une fenêtre de configuration de l’agent de gestion SQL générique. La page « Paramètres globaux » présente une case à cocher avec le libellé « Use named parameters to execute a stored procedure » (Utiliser des paramètres nommés pour exécuter une procédure stockée), qui permet de définir le mode d’exécution d’une procédure stockée avec des paramètres nommés ou non.
    * Actuellement, l’exécution d’une procédure stockée avec des paramètres nommés fonctionne uniquement pour les bases de données IBM DB2 et MSSQL. Cette approche ne fonctionne pas pour les bases de données Oracle et MySQL : 
      * Les syntaxes SQL de MySQL ne prennent pas en charge les paramètres nommés dans les procédures stockées.
      * Le pilote ODBC pour Oracle ne prend pas en charge les paramètres nommés dans les procédures stockées.

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Problèmes résolus :

* Services web génériques :
  * Correction d’un problème empêchant la création d’un projet SOAP lorsqu’il existait deux points de terminaison ou plus.
* SQL générique :
  * Dans l’opération d’importation, GSQL ne convertissait pas l’heure correctement lors de l’enregistrement dans l’espace du connecteur. Le format de date et d’heure par défaut de l’espace de connecteur de GSQL « aaaa-MM-jj hh:mm:ssZ » a été remplacé par « aaaa-MM-jj HH:mm:ssZ ».

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Problèmes résolus :

* Services web génériques :
  * L’outil Wsconfig n’a pas converti correctement le tableau Json à partir de « exemple de demande » pour la méthode de service REST. Ceci entraînait des problèmes lors de la sérialisation de ce tableau Json pour la demande REST.
  * L’outil de configuration du connecteur de service web ne prend pas en charge l’utilisation de symboles d’espace dans les noms d’attribut JSON 
    * Un modèle de substitution peut être ajouté manuellement dans le fichier WSConfigTool.exe.config, par exemple ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```
> [!NOTE]
> La clé JSONSpaceNamePattern est requise car pour l’exportation, vous recevrez l’erreur suivante : Message : le nom vide n’est pas légal. 

* Lotus Notes :
  * Quand l’option **Allow custom certifiers for Organization/Organizational Units** (Autoriser les certificateurs personnalisés pour les organisations/unités d’organisation) est désactivée, le connecteur échoue pendant l’exportation (mise à jour). Après le flux d’exportation, tous les attributs sont exportés vers Domino, mais au moment de l’exportation KeyNotFoundException est retournée à la synchronisation. 
    * Cela se produit car l’opération de renommage échoue quand elle tente de changer le nom unique (attribut UserName) en modifiant l’un des attributs ci-dessous :  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - Unité d’organisation
      - altcommonname

  * Quand l’option **Allow custom certifiers for Organization/Organizational Units** (Autoriser les certificateurs personnalisés pour les organisations/unités d’organisation) est activée, mais que les certificateurs nécessaires sont encore vides, une exception KeyNotFoundException se produit.

### <a name="enhancements"></a>Améliorations :

* SQL générique :
  * **Scénario : repensé Implémenté  : fonctionnalité**  « * »
  * **Description de la solution :** modification de l’approche pour la [gestion des attributs de référence à valeurs multiples](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Problèmes résolus :

* Services web génériques :
  * Impossible d’importer la configuration serveur si le connecteur de service web est présent
  * Le connecteur de service web ne fonctionne pas avec plusieurs services web

* SQL générique :
  * Aucun type d’objet n’est répertorié pour l’attribut référencé à valeur unique
  * L’importation différentielle sur la stratégie Change Tracking supprime objet lorsque la valeur est supprimée de la table à valeurs multiples
  * OverflowException dans le connecteur GSQL avec DB2 sur AS/400

Lotus :
  * Ajout de l’option pour activer/désactiver la recherche d’unités d’organisation avant d’ouvrir la page GlobalParameters

## <a name="114430"></a>1.1.443.0

Publié : mars 2017

### <a name="enhancements"></a>Améliorations

* SQL générique :</br>
  **Symptômes du scénario :** Avec cette limitation connue du connecteur SQL, seule une référence à un type d’objet est autorisée et une référence croisée avec des membres est requise. </br>
  **Description de la solution :** À l’étape de traitement des références pour lesquelles l’option « * » est sélectionnée, toutes les combinaisons de types d’objets sont renvoyées au moteur de synchronisation.

>[!Important]
- Cela crée de nombreux espaces réservés.
- L’utilisation d’une dénomination unique pour l’ensemble des types d’objets est requise.


* LDAP générique :</br>
 **Scénario :** Lorsque seuls certains conteneurs sont sélectionnés dans une partition spécifique, la recherche est tout de même effectuée dans la partition entière. La partition spécifique est filtrée par le service de synchronisation et non par MA, ce qui peut entraîner une dégradation des performances. </br>

 **Description de la solution :** Le code du connecteur GLDAP a été modifié pour pouvoir accéder à tous les conteneurs et rechercher des objets dans chacun d’eux, au lieu de rechercher dans la partition entière.


* Lotus Domino :

  **Scénario :** Prise en charge de la suppression d’un courrier Domino pour la suppression d’une personne pendant une exportation. </br>
  **Solution :** Prise en charge de la suppression d’un courrier configurable pour la suppression d’une personne pendant une exportation.

### <a name="fixed-issues"></a>Problèmes résolus :
* Services web génériques :
 * Lors de la modification de l’URL du service dans les projets wsconfig SAP par défaut via l’outil de configuration de service web, l’erreur suivante se produit : Impossible de trouver une partie du chemin d’accès

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* LDAP générique :
 * Le connecteur GLDAP ne voit pas tous les attributs dans AD LDS
 * L’assistant se bloque lorsqu’aucun attribut UPN n’est détecté dans le schéma d’annuaire LDAP
 * Les échecs d’importation delta avec erreurs de détection n’apparaissent pas pendant l’importation complète, lorsque l’attribut « objectclass » n’est pas sélectionné.
 * Une page de configuration « Configurer des partitions et des hiérarchies », n’affiche aucun objet de type égal à la partition des nouveaux serveurs dans le LDAP MA  
générique. Seuls les objets de la partition RootDSE sont affichés.


* SQL générique :
 * Correction du bogue empêchant l’importation d’un attribut à valeurs multiples d’importation delta de filigrane SQL générique
 * Lors de l’exportation des valeurs supprimées\ajoutées d’un attribut à valeurs multiples, celles-ci ne sont pas supprimées\ajoutées dans la source de données.  


* Lotus Notes :
 * Un champ spécifique « Nom complet » est affiché correctement dans le métaverse, mais lors de l’exportation vers Notes, la valeur de l’attribut est Null ou vide.
 * Correction d’erreur de certification en double
 * Lorsque l’objet ne comportant aucune donnée est sélectionné sur le connecteur Lotus Domino avec d’autres objets, nous recevons une erreur de détection lors de l’importation complète.
 * Lorsque l’importation delta est exécutée sur le connecteur Lotus Domino, à la fin de l’exécution, le service Microsoft.IdentityManagement.MA.LotusDomino.Service.exe renvoie parfois une erreur d’application.
 * L’appartenance au groupe global fonctionne correctement et est conservée, sauf lors de l’exécution de l’exportation visant à supprimer un utilisateur de l’appartenance qui apparaît comme ayant réussi avec une mise à jour, alors que l’utilisateur n’a pas réellement été supprimé de l’appartenance dans Lotus Notes.
 * Une option permettant de choisir le mode d’exportation « Append Item at bottom » (Ajouter un élément en bas) a été ajoutée dans l’interface utilisateur de configuration de Lotus MA pour ajouter de nouveaux éléments en bas pendant l’exportation des attributs à valeurs multiples.
 * Le connecteur ajoute la logique requise pour supprimer le fichier du dossier des courriers et du coffre d’ID.
 * La suppression d’appartenance ne fonctionne pas pour un membre NAB croisé.
 * Les valeurs devraient être supprimées correctement de l’attribut à valeurs multiples.

## <a name="111170"></a>1.1.117.0
Publié : mars 2016

**Nouveau connecteur**  
Version initiale du [connecteur SQL générique](active-directory-aadconnectsync-connector-genericsql.md).

**Nouvelles fonctionnalités :**

* Connecteur LDAP générique :
  * Prise en charge supplémentaire pour l’importation delta avec Isode.
* Connecteur des services web :
  * Mise à jour de l’activité csEntryChangeResult et de l’activité setImportErrorCode pour autoriser le renvoi des erreurs au niveau de l’objet vers le moteur de synchronisation.
  * Mise à jour des modèles SAP6 et SAP6User pour utiliser les nouvelles fonctionnalités d’erreur au niveau de l’objet.
* Connecteur Lotus Domino :
  * Lors de l’exportation, vous avez besoin d’une autorité de certification par carnet d’adresses. Vous pouvez désormais utiliser le même mot de passe pour toutes les autorités de certification pour simplifier la gestion.

**Problèmes résolus :**

* Connecteur LDAP générique :
  * Pour IBM Tivoli DS, certains attributs de référence n’étaient pas été détectés correctement.
  * Pour Open LDAP lors d’une importation delta, les espaces blancs au début et à la fin des chaînes étaient tronqués.
  * Pour Novell et NetIQ, une exportation qui déplaçait un objet entre des unités d’organisation/conteneurs avec renommage simultané de celui-ci, échouait.
* Connecteur des services web :
  * Si le service web avait plusieurs points de terminaison pour la même liaison, alors le connecteur ne détectait pas correctement ces points de terminaison.
* Connecteur Lotus Domino :
  * Une exportation de l’attribut fullName vers une Base courrier en arrivée ne fonctionnait pas.
  * Une exportation qui ajoutait et supprimait un membre d’un groupe exportait uniquement les membres ajoutés.
  * Si un document Notes n’est pas valide (attribut isValid défini sur false), le connecteur échoue.

## <a name="older-releases"></a>Versions plus anciennes
Avant mars 2016, les connecteurs étaient publiés sous forme de rubriques de prise en charge.

**LDAP générique**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, septembre 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, mars 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, janvier 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, septembre 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, mars 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, septembre 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, septembre 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, septembre 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, mars 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, août 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, février 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, octobre 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, août 2013

## <a name="troubleshooting"></a>Résolution de problèmes 

> [!NOTE]
> Lors de la mise à jour de Microsoft Identity Manager ou AADConnect pour l’utilisation d’un des connecteurs ECMA2. 

Si vous n’actualisez pas la définition du connecteur au moment de la mise à niveau, vous recevrez le message d’erreur suivant, qui signale l’avertissement nº 6947, dans le journal des événements de l’application : « Assembly version in AAD Connector configuration ("X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll". » (La version de l’assembly dans la configuration du connecteur AAD (« X.X.XXX.X ») est antérieure à la version réelle (« X.X.XXX.X ») de « C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ».).

Pour actualiser la définition :
* Ouvrez les propriétés de l’instance du connecteur.
* Cliquez sur l’onglet Connection / Connect to (Connexion / Se connecter à).
  * Entrez le mot de passe du compte du connecteur.
* Cliquez sur chacun des onglets de propriétés.
  * Si ce type de connecteur présente un onglet Partitions avec un bouton Actualiser, cliquez sur ce bouton.
* Une fois que vous avez accédé à tous les onglets de propriétés, cliquez sur le bouton OK pour enregistrer les modifications.


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
