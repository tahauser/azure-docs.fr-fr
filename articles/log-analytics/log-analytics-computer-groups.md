---
title: "Groupes d’ordinateurs dans les recherches de journal Azure Log Analytics | Microsoft Docs"
description: "Les groupes d’ordinateurs dans Log Analytics vous permettent d’étendre des recherches de journal à un ensemble spécifique d’ordinateurs.  Cet article décrit les différentes méthodes permettant de créer des groupes d’ordinateurs et la manière de les utiliser dans une recherche de journal."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: bwren
ms.openlocfilehash: 4d6a80082711f09e9c189d53fb4fda00a7d73c29
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Groupes d’ordinateurs dans les recherches de journal Log Analytics

Les groupes d’ordinateurs dans Log Analytics vous permettent d’étendre des [recherches de journal](log-analytics-log-search-new.md) à un ensemble spécifique d’ordinateurs.  Vous peuplez chaque groupe d’ordinateurs soit à l’aide d’une requête que vous définissez, soit en important des groupes à partir de différentes sources.  Quand un groupe est inclus dans une recherche de journal, les résultats sont limités aux enregistrements correspondant aux ordinateurs du groupe.

## <a name="creating-a-computer-group"></a>Création d’un groupe d’ordinateurs
Vous pouvez créer un groupe d’ordinateurs dans Log Analytics en utilisant l’une des méthodes répertoriées dans le tableau suivant.  Des détails sur chaque méthode sont fournis dans les sections ci-dessous. 

| Méthode | Description |
|:--- |:--- |
| Recherche dans les journaux |Créer une recherche dans les journaux qui retourne une liste d’ordinateurs. |
| API Recherche de journal |Utiliser l’API Recherche de journal pour créer par programme un groupe d’ordinateurs basé sur les résultats d’une recherche de journal. |
| Active Directory |Analyser automatiquement l’appartenance au groupe de tous les ordinateurs agents membres d’un domaine Active Directory, et créer un groupe dans Log Analytics pour chaque groupe de sécurité. |
| Gestionnaire de configuration | Importer des regroupements de System Center Configuration Manager et créer un groupe dans Log Analytics pour chacun. |
| Windows Server Update Services |Analyser automatiquement des serveurs ou clients WSUS pour des groupes de ciblage, et créer un groupe pour chacun d’eux dans Log Analytics. |

### <a name="log-search"></a>Recherche dans les journaux
Les groupes d’ordinateurs créés à partir d’une recherche dans les journaux contiennent tous les ordinateurs retournés par une requête que vous définissez.  Cette requête est exécutée chaque fois que le groupe d’ordinateurs est utilisé, de façon à refléter toutes les modifications apportées depuis la création du groupe.  

Vous pouvez utiliser une requête pour un groupe d’ordinateurs, mais elle doit retourner un ensemble distinct d’ordinateurs à l’aide de `distinct Computer`.  Voici un exemple type de recherche que vous pouvez utiliser pour un groupe d’ordinateurs.

    Heartbeat | where Computer contains "srv" | distinct Computer

Le tableau suivant décrit les propriétés qui définissent un groupe d’ordinateurs.

| Propriété | Description |
|:---|:---|
| Nom d’affichage   | Nom de la recherche à afficher dans le portail. |
| Catégorie       | Catégorie pour organiser les recherches dans le portail. |
| Requête          | Requête pour le groupe d’ordinateurs. |
| Alias de fonction | Alias unique utilisé pour identifier le groupe d’ordinateurs dans une requête. |

Utilisez la procédure suivante pour créer un groupe d’ordinateurs à partir d’une recherche dans les journaux dans le portail Azure.

2. Ouvrez **Recherche dans les journaux**, puis cliquez sur **Recherches enregistrées** en haut de l’écran.
3. Cliquez sur **Ajouter** et entrez des valeurs pour chaque propriété du groupe d’ordinateurs.
4. Sélectionnez **Enregistrer cette requête comme groupe d’ordinateurs**, puis cliquez sur **OK**.


Utilisez la procédure suivante pour créer un groupe d’ordinateurs à partir d’une recherche dans les journaux dans le portail OMS.

1. Ouvrez **Recherche dans les journaux** et créez la recherche dans les journaux pour le groupe d’ordinateurs.  
2. Cliquez sur le bouton **Enregistrer** en haut de l’écran.
3. Sélectionnez **Oui** à **Enregistrer cette requête comme groupe d’ordinateurs**.
5. Entrez des valeurs pour chaque propriété du groupe d’ordinateurs. 


>[!NOTE]
> Si votre espace de travail utilise encore le [langage de requête Log Analytics hérité](log-analytics-log-search-upgrade.md), suivez la même procédure de création d’un groupe d’ordinateurs en veillant à utiliser la syntaxe du langage de requête hérité.


### <a name="log-search-api"></a>API Recherche de journal
Les groupes d’ordinateurs créés avec l’API Recherche de journal sont les mêmes que les recherches créées avec une Recherche de journal.  Pour plus d’informations sur la création d’un groupe d’ordinateurs à l’aide de l’API Recherche de journal, voir [Groupes d’ordinateur dans l’API REST de recherche de journal Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Lorsque vous configurez Log Analytics pour importer les appartenances aux groupes Active Directory, Log Analytics analyse l’appartenance au groupe des ordinateurs joints à un domaine avec l’Agent OMS.  Un groupe d’ordinateurs est créé dans Log Analytics pour chaque groupe de sécurité dans Active Directory, et chaque ordinateur est ajouté aux groupes d’ordinateurs correspondant aux groupes de sécurité auxquels il appartient.  Cet appartenance est mise à jour toutes les 4 heures.  

Vous configurez Log Analytics pour importer des groupes de sécurité Active Directory à partir des **paramètres avancés** de Log Analytics dans le portail Azure.  Sélectionnez **Groupes d’ordinateurs**, **Active Directory**, puis **Importer les appartenances à des groupes Active Directory depuis les ordinateurs**.  Aucune configuration supplémentaire n’est requise.

![Groupes d’ordinateurs d’Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Une fois des groupes importés, le menu répertorie le nombre d’ordinateurs détectés avec une appartenance à un groupe et le nombre de groupes importés.  Vous pouvez cliquer sur l’un de ces liens pour retourner les enregistrements **ComputerGroup**avec ces informations.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Lorsque vous configurez Log Analytics pour importer les appartenances aux groupes WSUS, le service analyse les appartenances aux groupes de ciblage de tous les ordinateurs avec l’Agent OMS.  Si vous utilisez un ciblage côté client, l’appartenance à un groupe de tout ordinateur connecté à Log Analytics et faisant partie d’un groupe de ciblage WSUS est importée dans Log Analytics. Si vous utilisez un ciblage côté serveur, l’Agent OMS doit être installé sur le serveur WSUS afin que les informations d’appartenance au groupe soient importées dans Log Analytics.  Cet appartenance est mise à jour toutes les 4 heures. 

Vous configurez Log Analytics pour importer des groupes WSUS à partir des **paramètres avancés** de Log Analytics dans le portail Azure.  Sélectionnez **Groupes d’ordinateurs**, **WSUS**, puis **Importer les appartenances à un groupe WSUS**.  Aucune configuration supplémentaire n’est requise.

![Groupes d’ordinateurs à partir de WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Une fois des groupes importés, le menu répertorie le nombre d’ordinateurs détectés avec une appartenance à un groupe et le nombre de groupes importés.  Vous pouvez cliquer sur l’un de ces liens pour retourner les enregistrements **ComputerGroup**avec ces informations.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quand vous configurez Log Analytics pour importer les appartenances aux regroupements Configuration Manager, un groupe d’ordinateurs est créé pour chaque regroupement.  Les informations d’appartenance au regroupement sont récupérées toutes les 3 heures pour tenir les groupes d’ordinateurs à jour. 

Avant de pouvoir importer des regroupements Configuration Manager, vous devez [connecter Configuration Manager à Log Analytics](log-analytics-sccm.md).  Vous pouvez ensuite configurer l’importation à partir des **paramètres avancés** de Log Analytics dans le portail Azure.  Sélectionnez **Groupes d’ordinateurs**, **SCCM**, puis **Importer les appartenances aux regroupements Configuration Manager**.  Aucune configuration supplémentaire n’est requise.

![Groupes d’ordinateurs à partir de SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Une fois les regroupements importés, le menu répertorie le nombre d’ordinateurs détectés avec une appartenance à un groupe et le nombre de groupes importés.  Vous pouvez cliquer sur l’un de ces liens pour retourner les enregistrements **ComputerGroup**avec ces informations.

## <a name="managing-computer-groups"></a>Gestion de groupes d’ordinateurs
Vous pouvez afficher les groupes d’ordinateurs créés à partir d’une recherche dans les journaux ou de l’API Recherche dans les **paramètres avancés** de Log Analytics dans le portail Azure.  Sélectionnez **Groupes d’ordinateurs**, puis **Groupes enregistrés**.  

Cliquez sur le signe **x** dans la colonne **Supprimer** pour supprimer le groupe d’ordinateurs.  Cliquez sur l’icône **Afficher les membres** correspondant à un groupe pour exécuter la recherche de journal du groupe qui retourne les membres de celui-ci.  Pour modifier un groupe d’ordinateurs, vous devez le supprimer et le recréer avec les paramètres modifiés.

![Groupes d’ordinateurs enregistrés](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Utilisation d’un groupe d’ordinateurs dans une recherche de journal
Pour utiliser un groupe d’ordinateurs créé à partir d’une recherche dans les journaux dans une requête, traitez son alias comme une fonction, en général avec la syntaxe suivante :

  `Table | where Computer in (ComputerGroup)`

Par exemple, vous pouvez utiliser les éléments suivants pour retourner les enregistrements UpdateSummary pour les ordinateurs contenus dans un groupe d’ordinateurs appelé mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Les groupes d’ordinateurs importés et leurs ordinateurs inclus sont stockés dans la table **ComputerGroup**.  Par exemple, la requête suivante retourne une liste d’ordinateurs du groupe d’ordinateurs du domaine d’Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

La requête suivante retourne les enregistrements UpdateSummary pour les seuls ordinateurs du domaine.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```



  

>[!NOTE]
> Si votre espace de travail utilise encore le [langage de requête Log Analytics hérité](log-analytics-log-search-upgrade.md), utilisez la syntaxe suivante pour faire référence à un groupe d’ordinateurs dans une recherche dans les journaux.  La spécification de la **Catégorie** est facultative. Elle n’est obligatoire que si vous avez des groupes d’ordinateurs du même nom dans des catégories différentes. 
>
>    `$ComputerGroups[Category: Name]`
>
>Les groupes d’ordinateurs sont généralement utilisés avec la clause **IN**dans la recherche de journal, comme dans l’exemple suivant :
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Enregistrements de groupe d’ordinateurs
Un enregistrement est créé dans l’espace de travail Log Analytics pour chaque appartenance à un groupe d’ordinateur créée à partir d’Active Directory ou de WSUS.  Ces enregistrements sont de type **ComputerGroup** et ont les propriétés décrites dans le tableau suivant.  Des enregistrements ne sont pas créés pour des groupes d’ordinateurs basés sur des recherches de journal.

| Propriété | Description |
|:--- |:--- |
| type |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Ordinateur |Nom de l’ordinateur membre. |
| Groupe |Nom du groupe. |
| GroupFullName |Chemin d’accès complet au groupe, incluant la source et le nom de la source. |
| GroupSource |Source à partir de laquelle ce groupe a été collecté. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nom de la source à partir de laquelle le groupe a été collecté.  Pour Active Directory, il s’agit du nom de domaine. |
| ManagementGroupName |Nom du groupe d'administration pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI-\<workspace ID\> |
| TimeGenerated |Date et heure de création ou de mise à jour du groupe d’ordinateurs. |

## <a name="next-steps"></a>étapes suivantes
* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.  

