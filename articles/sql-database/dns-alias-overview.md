---
title: Alias DNS pour Azure SQL Database | Microsoft Docs
description: Vos applications peuvent se connecter à un alias du nom de votre serveur Azure SQL Database. Vous pouvez pendant ce temps modifier la base de données SQL sur laquelle pointe l’alias à tout moment, pour faciliter les tests et autres.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: DNS alias
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;ayolubek
ms.author: dmalik
ms.openlocfilehash: c2a2bce3b385bd5700bf327f283289cf4d94b057
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pour Azure SQL Database

Azure SQL Database dispose d’un serveur DNS (Domain Name System). Les API PowerShell et REST acceptent les [appels pour créer et gérer des alias DNS](#anchor-powershell-code-62x) du nom du serveur SQL Database.

Un *alias DNS* peut être utilisé à la place du nom du serveur Azure SQL Database, notamment dans la chaîne de connexion des programmes clients. Il représente une couche de traduction capable de rediriger les programmes clients sur différents serveurs. Il évite ainsi d’avoir à rechercher et à modifier tous les clients et leur chaîne de connexion.

Voici quelques-unes des utilisations courantes d’un alias DNS :

- Créer un nom facile à retenir pour un serveur Azure SQL.
- Pendant le développement initial, l’alias peut faire référence à un serveur SQL Database de test. Lorsque l’application est publiée, vous pouvez le modifier afin qu’il fasse référence au serveur de production. La transition du test à la production n’implique aucune modification des configurations de plusieurs clients qui se connectent au serveur de base de données.
- Supposons que l’unique base de données d’une application soit déplacée vers un autre serveur SQL Database. Vous pourrez alors modifier l’alias sans avoir à changer les configurations de plusieurs clients.

#### <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) d’Internet

Internet s’appuie sur le serveur DNS. Le serveur DNS traduit les noms conviviaux pour trouver le nom du serveur Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scénarios avec un seul alias DNS

Supposons que vous deviez faire passer votre système à un nouveau serveur Azure SQL Database. Auparavant, il vous fallait rechercher et mettre à jour la chaîne de connexion de chacun des programmes clients. Maintenant, si les chaînes de connexion utilisent un alias DNS, il n’y a qu’une propriété d’alias à modifier.

La fonctionnalité d’alias DNS d’Azure SQL Database est utile dans les cas suivants :

#### <a name="test-to-production"></a>Passage des tests à la production

Lorsque vous commencez à développer les programmes clients, demandez-leur d’utiliser un alias DNS dans leur chaîne de connexion. Faites en sorte que les propriétés de l’alias pointent vers une version de test de votre serveur Azure SQL Database.

Lorsque le nouveau système passera en production, vous pourrez les mettre à jour afin qu’elles pointent vers le serveur SQL Database de production. Aucune modification des programmes clients n’est nécessaire.

#### <a name="cross-region-support"></a>Prise en charge interrégionale

Il peut arriver qu’une récupération d’urgence déplace un serveur SQL Database dans une autre région géographique. Si le système utilisait un alias DNS, il n’est pas nécessaire de rechercher et de mettre à jour la chaîne de connexion de tous les clients. Vous pouvez modifier un alias de sorte qu’il fasse référence au nouveau serveur SQL Database qui héberge maintenant votre base de données.




## <a name="properties-of-a-dns-alias"></a>Propriétés d’un alias DNS

Les propriétés suivantes s’appliquent à tous les alias DNS du serveur SQL Database :

- *Nom unique :* chaque nom d’alias créé est unique sur tous les serveurs Azure SQL Database, tout comme les noms de serveurs.

- *Serveur requis :* il n’est possible de créer un alias DNS que s’il fait référence à exactement un serveur et que celui-ci existe déjà. Un alias mis à jour doit toujours faire référence à un seul serveur existant.
    - En cas de suppression d’un serveur SQL Database, le système Azure supprime également tous les alias DNS qui y font référence.

- *Aucune liaison à une région :* les alias DNS ne sont pas liés à une région. Ils peuvent être mis à jour de façon à faire référence à un serveur Azure SQL Database quelle que soit sa région géographique.
    - Toutefois, en cas de référence à un autre serveur, les deux serveurs doivent se trouver dans le même *abonnement* Azure.

- *Autorisations :* pour gérer un alias DNS, l’utilisateur doit au moins avoir l’autorisation *Collaborateur serveur*. Pour plus d’informations, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure](../active-directory/role-based-access-control-what-is.md).





## <a name="manage-your-dns-aliases"></a>Gérer les alias DNS

Les cmdlets PowerShell comme les API REST permettent de gérer les alias DNS par programme.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>API REST pour gérer les alias DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

La documentation des API REST est disponible près de l’emplacement web suivant :
- [API REST Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

Par ailleurs, les API REST peuvent être consultées sur GitHub à l’adresse :
- [Serveur Azure SQL Database, alias DNS et API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell pour gérer les alias DNS

Les cmdlets PowerShell appellent les API REST.

Un exemple de code des cmdlets PowerShell utilisées pour gérer les alias DNS est documenté ici :
- [PowerShell pour les alias DNS sur Azure SQL Database](dns-alias-powershell.md)


Voici les cmdlets utilisées dans l’exemple de code :
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : crée un alias DNS dans le système de service Azure SQL Database. L’alias fait référence au serveur 1 Azure SQL Database.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : récupère et liste tous les alias DNS affectés au serveur 1 SQL DB.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : modifie le nom du serveur auquel l’alias est configuré pour faire référence, en passant du serveur 1 au serveur 2 SQL DB.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : supprime l’alias DNS du serveur 2 SQL DB, en utilisant le nom de l’alias.


Les cmdlets précédentes ont été ajoutées au module **AzureRM.Sql** à partir de la version 5.1.1.




## <a name="limitations-during-preview"></a>Limitations de la préversion

Actuellement, un alias DNS présente les limitations suivantes :

- *Retard pouvant atteindre deux minutes :* la mise à jour et la suppression d’un alias DNS peuvent prendre jusqu’à deux minutes.
    - Indépendamment du bref délai, l’alias arrête immédiatement de faire référence aux connexions clientes au serveur hérité.

- *Recherche DNS :* pour l’instant, le seul moyen sûr de vérifier à quel serveur un alias DNS donné fait référence consiste à effectuer une [recherche DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Audit des tables non pris en charge ](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* il n’est pas possible d’utiliser un alias DNS sur un serveur Azure SQL Database si *l’audit des tables* est activé sur une de ses bases de données.
    - L’audit des tables est déconseillé.
    - Nous vous recommandons de passer à [l’Audit des objets blob](sql-database-auditing.md).




## <a name="related-resources"></a>Ressources associées

- [Vue d’ensemble de la continuité d’activité avec Azure SQL Database](sql-database-business-continuity.md), couvrant notamment la récupération d’urgence.



## <a name="next-steps"></a>Étapes suivantes

- [PowerShell pour les alias DNS sur Azure SQL Database](dns-alias-powershell.md)

