---
title: "Points de terminaison de service de réseau virtuel et règles dans Azure SQL Database | Microsoft Docs"
description: "Marquez un sous-réseau en tant que point de terminaison de service de réseau virtuel, puis le point de terminaison en tant que règle de réseau virtuel dans l’ACL de votre serveur Azure SQL Database. Le serveur SQL Database accepte alors les communications provenant de toutes les machines virtuelles et d’autres nœuds sur le sous-réseau."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 02/20/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: c1bb1698723af60544b89f4b3168c44a32d31afd
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Utiliser des points de terminaison de service de réseau virtuel et des règles pour Azure SQL Database

Les *règles de réseau virtuel* sont une fonctionnalité de sécurité de pare-feu. Elles permettent de contrôler si votre serveur Azure SQL Database doit accepter ou non les communications provenant de sous-réseaux spécifiques dans des réseaux virtuels. Cet article explique pourquoi la fonctionnalité de règles de réseau virtuel est parfois la meilleure solution pour autoriser en toute sécurité des communications sur votre serveur Azure SQL Database.

Pour créer une règle de réseau virtuel, il doit d’abord exister un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] pour la règle à référencer.


#### <a name="how-to-create-a-virtual-network-rule"></a>Création d’une règle de réseau virtuel

Si vous créez uniquement une règle de réseau virtuel, vous pouvez passer directement à la procédure et à l’explication donnée [plus loin dans cet article](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie et description

**Réseau virtuel :** vous pouvez avoir des réseaux virtuels associés à votre abonnement Azure.

**Sous-réseau :** un réseau virtuel contient des **sous-réseaux**. Toutes les machines virtuelles Azure que vous avez sont assignées à des sous-réseaux. Un sous-réseau peut contenir plusieurs machines virtuelles ou d’autres nœuds de calcul. Les nœuds de calcul qui se trouvent en dehors de votre réseau virtuel ne peuvent pas accéder à ce dernier, sauf si vous configurez votre sécurité pour leur en donner l’accès.

**Point de terminaison de service de réseau virtuel :** un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Dans cet article, nous nous intéressons au nom de type de **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database.

**Règle de réseau virtuel :** une règle de réseau virtuel pour le serveur SQL Database est un sous-réseau répertorié dans la liste de contrôle d’accès (ACL) du serveur SQL Database. Pour figurer dans l’ACL pour le serveur SQL Database, le sous-réseau doit contenir le nom de type **Microsoft.Sql**.

Une règle de réseau virtuel donne l’instruction au serveur SQL Database d’accepter les communications provenant de tout nœud se trouvant sur le sous-réseau.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Avantages d’une règle de réseau virtuel

Les machines virtuelles sur vos sous-réseaux ne peuvent pas communiquer avec votre serveur SQL Database sans une intervention de votre part. La création d’une règle de réseau virtuel permet d’établir la communication. Le choix de l’approche des règles de réseau virtuel peut être justifié par contraste et comparaison avec les options de sécurité concurrentes offertes par le pare-feu.

#### <a name="a-allow-access-to-azure-services"></a>R. Autoriser l’accès aux services Azure

Le volet du pare-feu dispose d’un bouton **ACTIVÉ/DÉSACTIVÉ** étiqueté **Autoriser l'accès aux services Azure**. Le paramètre **ACTIVÉ** autorise les communications provenant de toutes les adresses IP Azure et de tous les sous-réseaux Azure. Ces adresses IP ou sous-réseaux Azure ne vous appartiennent peut-être pas. Ce paramètre **ACTIVÉ** est sans doute plus ouvert que vous souhaitez que le soit votre serveur SQL Database. La fonctionnalité de règles de réseau virtuel offre un contrôle granulaire beaucoup plus précis.

#### <a name="b-ip-rules"></a>B. Règles IP

Le pare-feu du serveur SQL Database permet de spécifier des plages d’adresses IP à partir desquelles les communications sont acceptées sur le serveur SQL Database. Cette approche est indiquée pour les adresses IP stables qui se trouvent en dehors du réseau privé Azure. Mais un grand nombre de nœuds à l’intérieur du réseau privé Azure sont configurés avec des adresses IP *dynamiques*. Les adresses IP dynamiques peuvent changer, par exemple quand la machine virtuelle est redémarrée. Spécifier une adresse IP dynamique dans une règle de pare-feu au sein d’un environnement de production serait inimaginable.

Vous pouvez récupérer l’option IP en obtenant une adresse IP *statique* pour votre machine virtuelle. Pour plus d’informations, consultez [Configurer des adresses IP privées pour une machine virtuelle à l’aide du portail Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

L’approche des IP statiques peut toutefois devenir difficile à gérer, et elle est coûteuse quand elle est appliquée à grande échelle. Les règles de réseau virtuel sont plus faciles à établir et à gérer.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. SQL Database n’est plus disponible sur un sous-réseau

Si votre serveur Azure SQL Database était un nœud sur un sous-réseau de votre réseau virtuel, tous les nœuds situés dans le réseau virtuel pourraient communiquer avec le serveur SQL Database. Dans ce cas, vos machines virtuelles pourraient communiquer avec le serveur SQL Database sans avoir à utiliser de règles de réseau virtuel ni de règles IP.

Mais depuis septembre 2017, le service Azure SQL Database ne figure plus parmi les services pouvant être assignés à un sous-réseau.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Informations sur les règles de réseau virtuel

Cette section fournit des informations sur les règles de réseau virtuel.

#### <a name="only-one-geographic-region"></a>Une seule région géographique

Chaque point de terminaison de service de réseau virtuel s’applique à une seule région Azure. Le point de terminaison ne permet pas à d’autres régions d’accepter les communications provenant du sous-réseau.

Une règle de réseau virtuel est limitée à la région à laquelle s’applique son point de terminaison sous-jacent.

#### <a name="server-level-not-database-level"></a>Niveau serveur, et non niveau base de données

Chaque règle de réseau virtuel s’applique à tout le serveur Azure SQL Database, et pas seulement à une base de données particulière sur le serveur. En d’autres termes, la règle de réseau virtuel s’applique au niveau du serveur, et non au niveau de la base de données.

- En revanche, les règles IP peuvent être appliquées à tout niveau.

#### <a name="security-administration-roles"></a>Rôles d’administration de la sécurité

Il existe une séparation des rôles de sécurité dans l’administration des points de terminaison de service de réseau virtuel. Chacun des rôles suivants doit réaliser une action :

- **Administrateur de réseau :**&nbsp; Activez le point de terminaison.
- **Administrateur de base de données :**&nbsp; Mettez à jour la liste de contrôle d’accès (ACL) pour ajouter un sous-réseau donné sur le serveur SQL Database.

*Alternative RBAC :*

Les rôles d’administrateur de réseau et d’administrateur de base de données disposent de plus de fonctionnalités que nécessaires pour gérer les règles de réseau virtuel. Seule une partie de ces fonctionnalités est réellement nécessaire.

Vous avez la possibilité d’utiliser le [contrôle d’accès en fonction du rôle (RBAC)][rbac-what-is-813s] dans Azure pour créer un rôle personnalisé unique disposant uniquement des fonctionnalités nécessaires. Le rôle personnalisé peut être utilisé au lieu d’impliquer l’administrateur de réseau ou l’administrateur de base de données. Votre surface d’exposition de sécurité est inférieure si vous assignez un rôle personnalisé à un utilisateur au lieu de lui assigner les deux principaux rôles d’administrateur.

> [!NOTE]
> Il peut arriver que l’instance Azure SQL Database et le sous-réseau de réseau virtuel se trouvent dans des abonnements différents. Dans ce cas, vous devez vérifier les configurations suivantes :
> - Les deux abonnements doivent se trouver dans le même locataire Azure Active Directory.
> - L’utilisateur dispose des autorisations requises pour lancer des opérations, telles que l’activation des points de terminaison de service et l’ajout d’un sous-réseau de réseau virtuel sur le serveur donné.

## <a name="limitations"></a>Limites

Pour Azure SQL Database, la fonctionnalité de règles de réseau virtuel présente les limitations suivantes :

- Une application web peut être mappée à une adresse IP privée dans un sous-réseau/réseau virtuel. Même si les points de terminaison de service sont activés à partir du réseau virtuel/sous-réseau donné, les connexions entre l’application web et le serveur présenteront une source IP publique Azure, et non une source de sous-réseau/réseau virtuel. Pour activer la connectivité à partir d’une application web vers un serveur disposant de règles de pare-feu de réseau virtuel, vous devez **autoriser tous les services Azure** sur le serveur.

- Dans le pare-feu pour votre base de données SQL Database, chaque règle de réseau virtuel fait référence à un sous-réseau. Tous ces sous-réseaux référencés doivent être hébergés dans la même région géographique qui héberge la base de données SQL Database.

- Chaque serveur Azure SQL Database peut avoir jusqu’à 128 entrées ACL pour un réseau virtuel donné.

- Les règles de réseau virtuel s’appliquent uniquement à des réseaux virtuels Azure Resource Manager, et non à des réseaux avec un [modèle de déploiement Classic][arm-deployment-model-568f].

- Activer les points de terminaison de réseau virtuel pour Azure SQL Database active également les points de terminaison pour les services MySQL et PostGres Azure. Toutefois, avec les points de terminaison activés, les tentatives de connexion à partir des points de terminaison pour vos instances de MySQL ou Postgres échoueront.
    - La raison sous-jacente est que MySQL et PostGres ne prennent pas actuellement en charge les ACL.

- Sur le pare-feu, les plages d’adresses IP s’appliquent aux éléments de mise en réseau suivants, contrairement aux règles de réseau virtuel :
    - [Réseau privé virtuel (VPN) site à site (S2S)][vpn-gateway-indexmd-608y]
    - En local via [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Considérations en cas d’utilisation des points de terminaison de service
Lorsque vous utilisez des points de terminaison de service pour Azure SQL Database, passez en revue les considérations suivantes :

- **Une sortie vers les adresses IP publiques Azure SQL Database est requise** : des groupes de sécurité réseau (NSG) doivent être ouverts aux adresses IP Azure SQL Database pour autoriser la connectivité. Vous pouvez pour ce faire utiliser des [balises de service](../virtual-network/security-overview.md#service-tags) NSG pour Azure SQL Database.
- **Azure SQL Database pour PostgreSQL et MySQL ne sont pas pris en charge** : les points de terminaison de service ne sont pas pris en charge pour Azure Database pour PostgreSQL ou MySQL. L’activation des points de terminaison de service pour la base de données SQL interrompra la connectivité à ces services. Nous avons une atténuation pour cela ; veuillez contacter *dmalik@microsoft.com*.

#### <a name="expressroute"></a>ExpressRoute

Si votre réseau est connecté au réseau Azure via l’utilisation [d’ExpressRoute][expressroute-indexmd-744v], chaque circuit est configuré avec deux adresses IP publiques sur Microsoft Edge. Les deux adresses IP sont utilisées pour se connecter aux services Microsoft, comme le stockage Azure, à l’aide de l’homologation publique Azure.

Pour permettre la communication de votre circuit avec Azure SQL Database, vous devez créer des règles de réseau IP pour les adresses IP publiques de vos circuits. Pour rechercher les adresses IP publiques de votre circuit ExpressRoute, ouvrez un ticket de support avec ExpressRoute dans le portail Azure.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Impact de la suppression de la règle « Autoriser tous les services Azure »

De nombreux utilisateurs souhaitent supprimer la règle **Autoriser tous les services Azure** de leurs serveurs SQL Azure et la remplacer par une règle de pare-feu de réseau virtuel.
Mais la suppression de cette règle affecte les fonctionnalités Azure SQLDB suivantes :

#### <a name="import-export-service"></a>Service d’importation/exportation
Le service d’importation/exportation Azure SQLDB s’exécute sur des machines virtuelles dans Azure. Ces machines virtuelles, qui ne se trouvent pas dans votre réseau virtuel, obtiennent une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser tous les services Azure**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.
Vous pouvez contourner le problème. Exécutez le service d’importation/exportation BACPAC directement dans votre code à l’aide de l’API DACFx. Assurez-vous que le déploiement s’effectue dans une machine virtuelle qui se trouve dans le sous-réseau de réseau virtuel pour lequel vous avez défini la règle de pare-feu.

#### <a name="sql-database-query-editor"></a>Éditeur de requêtes SQL Database
L’éditeur de requêtes Azure SQL Database est déployé sur des machines virtuelles dans Azure. Ces machines virtuelles ne se trouvent pas dans votre réseau virtuel. Elles obtiennent donc une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser tous les services Azure**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.

#### <a name="table-auditing"></a>Audit de table
Il existe actuellement deux façons d’activer l’audit sur votre instance SQL Database. L’audit de table échoue une fois que vous avez activé des points de terminaison de service sur votre instance Azure SQL Server. Pour contourner le problème, vous pouvez utiliser un audit d’objets blob.

#### <a name="impact-on-data-sync"></a>Impact sur la synchronisation de données
SQLDB Azure dispose de la fonctionnalité de synchronisation de données qui se connecte à vos bases de données à l’aide d’adresses IP Azure. Lorsque vous utilisez des points de terminaison de service, il est probable que vous comptiez désactiver l’accès **Autoriser tous les services Azure** à votre serveur logique. Cela arrêtera la fonctionnalité de synchronisation de données.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impact de l’utilisation des points de terminaison de service de réseau virtuel avec le stockage Azure

Le stockage Azure a implémenté la même fonctionnalité qui vous permet de limiter la connectivité à votre compte de stockage.
Si vous choisissez d’utiliser cette fonctionnalité avec un compte de stockage utilisé par une instance SQL Azure Server, vous risquez de rencontrer les problèmes. Vous trouverez ci-dessous une liste détaillée des fonctionnalités Azure SQLDB affectées par ce problème.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase est couramment utilisé pour charger des données dans Azure SQLDW à partir de comptes de stockage. Si le compte de stockage à partir duquel vous chargez des données limite l’accès à seulement un ensemble de sous-réseaux de réseau virtuel, la connectivité entre PolyBase et le compte sera interrompue. Il existe une atténuation des risques pour cet objet ; veuillez contacter *dmalik@microsoft.com* pour plus d’informations.

#### <a name="azure-sqldb-blob-auditing"></a>Audit d’objets blob Azure SQLDB
L’audit d’objets blob transfère des journaux d’audit à votre propre compte de stockage. Si ce compte de stockage utilise la fonctionnalité de points de terminaison de service VENT, la connectivité entre Azure SQLDB et le compte de stockage sera interrompue.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Ajout d’une règle de pare-feu de réseau virtuel à votre serveur sans activer les points de terminaison de service Vnet

Bien avant que cette fonctionnalité ne soit améliorée, vous deviez activer les points de terminaison de service Vnet avant de pouvoir implémenter une règle de réseau virtuel dynamique dans le pare-feu. Les points de terminaison associaient un sous-réseau/réseau virtuel donné à une base de données Azure SQL Database. Depuis janvier 2018, vous pouvez éviter cette opération en définissant l’indicateur **IgnoreMissingServiceEndpoint**.

La simple définition d’une règle de pare-feu ne permet pas de sécuriser le serveur. Vous devez également activer les points de terminaison de service Vnet pour que la sécurité entre en vigueur. Quand vous activez les points de terminaison de service, votre sous-réseau/réseau virtuel est arrêté pendant qu’il passe de l’état désactivé à l’état activé. Cela est particulièrement vrai dans le contexte de grands réseaux virtuels. Vous pouvez utiliser l’indicateur **IgnoreMissingServiceEndpoint** pour réduire ou éliminer le temps d’arrêt pendant la transition.

Vous pouvez définir l’indicateur **IgnoreMissingServiceEndpoint** à l’aide de PowerShell. Pour plus d’informations, consultez [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel et une règle pour Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Erreurs 40914 et 40615

L’erreur de connexion 40914 est liée aux *règles de réseau virtuel*, comme spécifié dans le volet Pare-feu du portail Azure. L’erreur 40615 est similaire, à ceci près qu’elle est liée aux *règles des adresses IP* sur le pare-feu.

#### <a name="error-40914"></a>Erreur 40914

*Texte du message :* Impossible d’ouvrir le serveur « *[nom-serveur]* » demandé par la connexion. Le client n’est pas autorisé à accéder au serveur.

*Description de l’erreur :* le client se trouve dans un sous-réseau qui a des points de terminaison de serveur de réseau virtuel. Mais le serveur Azure SQL Database n’a pas de règle de réseau virtuel qui accorde au sous-réseau le droit de communiquer avec la base de données SQL Database.

*Résolution de l’erreur :* dans le volet Pare-feu du portail Azure, utilisez le contrôle des règles de réseau virtuel pour [ajouter une règle de réseau virtuel](#anchor-how-to-by-using-firewall-portal-59j) au sous-réseau.

#### <a name="error-40615"></a>Erreur 40615

*Texte du message :* Impossible d’ouvrir le serveur ’{0}’ demandé par la connexion. Le client avec l'adresse IP '{1}' n'est pas autorisé à accéder au serveur.

*Description de l’erreur :* le client tente de se connecter à partir d’une adresse IP qui n’est pas autorisée à se connecter au serveur Azure SQL Database. Le pare-feu du serveur ne dispose d’aucune règle d’adresse IP qui autorise un client à communiquer à partir de l’adresse IP donnée vers la base de données SQL Database.

*Résolution de l’erreur :*  entrez l’adresse IP du client en tant que règle d’adresse IP. Effectuez cette opération via le volet Pare-feu du portail Azure.


Une liste de plusieurs messages d’erreur de base de données SQL Database est documentée [ici][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Le portail peut créer une règle de réseau virtuel

Cette section montre comment vous pouvez utiliser le [portail Azure][http-azure-portal-link-ref-477t] pour créer une *règle de réseau virtuel* sur votre serveur Azure SQL Database. La règle donne l’instruction à votre serveur SQL Database d’accepter les communications provenant d’un sous-réseau spécifique qui a été marqué comme étant un *point de terminaison de service de réseau virtuel*.

#### <a name="powershell-alternative"></a>Alternative PowerShell

Un script PowerShell peut également créer des règles de réseau virtuel avec l’applet de commande essentielle **New-AzureRmSqlServerVirtualNetworkRule**. Si cette option vous intéresse, consultez [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel et une règle pour Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="prerequisites"></a>Prérequis

Vous devez déjà disposer d’un sous-réseau étiqueté avec le *nom de type* de point de terminaison de service de réseau virtuel particulier approprié pour Azure SQL Database.

- Le nom de type de point de terminaison approprié est **Microsoft.Sql**.
- Si votre sous-réseau n’est pas étiqueté avec le nom de type, consultez [Vérifier que votre sous-réseau est un point de terminaison][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Étapes dans le portail Azure

1. Connectez-vous au [portail Azure][http-azure-portal-link-ref-477t].

2. Accédez ensuite à **Serveurs SQL** &gt; **Pare-feu / Réseaux virtuels**.

3. Définissez le contrôle **Autoriser l’accès aux services Azure** sur DÉSACTIVÉ.

    > [!IMPORTANT]
    > Si vous maintenez le contrôle ACTIVÉ, votre serveur Azure SQL Database acceptera les communications à partir de n’importe quel sous-réseau. En termes de sécurité, le fait de laisser le contrôle ACTIVÉ peut avoir pour effet de multiplier excessivement les accès. La fonctionnalité de points de terminaison de service de réseau virtuel Microsoft Azure, associée à la fonctionnalité de règles de réseau virtuel de SQL Database, peuvent ensemble réduire votre surface d’exposition de sécurité.

4. Cliquez sur le contrôle **+ Ajouter existant** dans la section **Réseaux virtuels**.

    ![Cliquez sur Ajouter existant (point de terminaison de sous-réseau, en tant que règle SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. Dans le nouveau volet **Créer/mettre à jour**, renseignez les contrôles avec les noms de vos ressources Azure.

    > [!TIP]
    > Vous devez inclure le **préfixe d’adresse** correct pour votre sous-réseau. Vous pouvez trouver la valeur correspondante dans le portail.
    > Accédez à **Toutes les ressources** &gt; **Tous les types** &gt; **Réseaux virtuels**. Le filtre affiche vos réseaux virtuels. Cliquez sur votre réseau virtuel, puis sur **Sous-réseaux**. La colonne **PLAGE D’ADRESSES** contient le préfixe d’adresse nécessaire.

    ![Renseignez les champs de la nouvelle règle.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Cliquez sur le bouton **OK** en bas du volet.

7.  Le volet du pare-feu affiche la règle de réseau virtuel obtenue.

    ![Le volet du pare-feu affiche la nouvelle règle.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Les états suivants s’appliquent aux règles :
> - **Prêt :** indique que l’opération que vous avez lancée a abouti.
> - **Échec :** indique que l’opération que vous avez lancée a échoué.
> - **Supprimé :** s’applique uniquement à l’opération de suppression et indique que la règle a été supprimée et qu’elle ne s’applique plus.
> - **En cours :** indique que l’opération est en cours d’exécution. L’ancienne règle s’applique lorsque l’opération est à cet état.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Articles connexes

- [Points de terminaison de service de réseau virtuel Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database][sql-db-firewall-rules-config-715d]

La fonctionnalité de règle de réseau virtuel pour Azure SQL Database est disponible depuis fin septembre 2017.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel, puis une règle de réseau virtuel pour Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]


<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
