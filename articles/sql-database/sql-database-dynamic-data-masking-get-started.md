---
title: Masquage de données dynamiques dans une base de données Azure SQL | Microsoft Docs
description: Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/09/2017
ms.author: ronitr
ms.openlocfilehash: 543b4bc15177cc198aaa3c312b563a4bd6e844fc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-dynamic-data-masking"></a>Masquage de données dynamiques dans une base de données SQL

Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. 

Le masquage des données dynamiques empêche tout accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité de données sensibles à faire apparaître, avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.

Par exemple, un représentant du centre d’appel peut identifier les appelants par plusieurs chiffres de leur numéro de carte de crédit, mais ces éléments de données ne doivent pas être divulgués entièrement au conseiller du service. Une règle de masquage peut être définie pour ne faire apparaître que les quatre derniers chiffres du numéro de carte de crédit dans l’ensemble de résultats de chaque requête. Autre exemple, un masque de données approprié peut être défini pour protéger les informations d'identification personnelle (PII), de telle sorte qu'un développeur puisse interroger les environnements de production à des fins de dépannage sans aller à l'encontre des réglementations de conformité.

## <a name="sql-database-dynamic-data-masking-basics"></a>Principes fondamentaux du masquage de données dynamiques de base de données SQL
Pour définir une stratégie de masquage de données dynamiques sur le Portail Azure, sélectionnez l’opération de masquage des données dynamiques dans votre panneau de configuration ou de paramètres SQL Database.

### <a name="dynamic-data-masking-permissions"></a>Autorisations du masquage des données dynamiques
Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

### <a name="dynamic-data-masking-policy"></a>Stratégie de masquage des données dynamiques
* **Utilisateurs SQL exclus du masquage** : ensemble d’utilisateurs SQL ou d’identités AAD qui obtiennent des données non masquées dans les résultats de requêtes SQL. Les utilisateurs possédant des privilèges administrateur sont toujours exclus du masquage et voient les données d’origine sans masque.
* **Règles de masquage** : ensemble de règles qui définissent les champs désignés à masquer et la fonction de masquage à utiliser. Les champs désignés peuvent être définis avec un nom de schéma de base de données, un nom de table et un nom de colonne.
* **Fonctions de masquage** : ensemble de méthodes qui contrôlent l'exposition des données dans différents scénarios.

| Fonction de masquage | Logique de masquage |
| --- | --- |
| **Par défaut** |**Masquage complet en fonction des types de données des champs désignés**<br/><br/>• Utilisez XXXX ou moins de X si la taille du champ est inférieure à 4 caractères pour les types de données String (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilisez 01-01-1900 pour les types de données date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Pour la variante SQL, la valeur par défaut du type actuel est utilisé.<br/>• Pour XML, le document <masked/> est utilisé.<br/>• Utilisez une valeur vide pour les types de données spéciaux (table timestamp, hierarchyid, GUID, binary, image, varbinary spatial). |
| **Carte de crédit** |**Méthode de masquage qui affiche les quatre derniers chiffres des champs de masquage désignés** et ajoute une chaîne constante comme préfixe sous la forme d’une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Méthode de masquage qui affiche la première lettre et remplace le domaine par XXX.com** avec en préfixe une chaîne constante sous la forme d’une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** |**Méthode de masquage qui génère un nombre aléatoire** selon les limites sélectionnées et les types de données réels. Si les limites désignées sont égales, la fonction de masquage est un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texte personnalisé** |**Méthode de masquage qui affiche le premier et le dernier caractères** et ajoute une chaîne de remplissage personnalisée entre les deux. Si la chaîne d’origine est plus courte que le préfixe et le suffixe affichés, seule la chaîne de remplissage est utilisée. <br/>préfixe[remplissage]suffixe<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Champs à masquer recommandés
Le moteur de recommandations DDM signale certains champs de votre base de données en tant que champs potentiellement sensibles, qui peuvent être de bons candidats au masquage. Dans le volet Masquage des données dynamiques du portail, vous verrez les colonnes recommandées pour votre base de données. Il vous suffit de cliquer sur **Ajouter un masque** pour une ou plusieurs colonnes, puis de cliquer sur **Enregistrer** pour appliquer un masque à ces champs.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configuration du masquage des données dynamiques pour votre base de données à l’aide des cmdlets Powershell
Voir [Cmdlets de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configuration du masquage des données dynamiques pour votre base de données à l’aide de l’API REST
Voir [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

