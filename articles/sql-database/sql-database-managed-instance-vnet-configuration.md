---
title: Configuration d’un réseau virtuel Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique décrit les options de configuration d’un réseau virtuel avec Azure SQL Database Managed Instance.
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1a839a9bb2355da9451816828f6f9f0e99f43f5e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (préversion) doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Ce déploiement permet les scénarios suivants : 
- Connexion directe à Managed Instance à partir d’un réseau local 
- Connexion de Managed Instance à un serveur lié ou un autre magasin de données local 
- Connexion de Managed Instance à des ressources Azure  

## <a name="plan"></a>Planification

Planifiez votre déploiement de Managed Instance dans un réseau virtuel en utilisant vos réponses aux questions suivantes : 
- Envisagez-vous de déployer une seule ou plusieurs options Managed Instance ? 

  Le nombre d’options Managed Instance détermine la taille minimale du sous-réseau à allouer à vos instances gérées. Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour Managed Instance](#create-a-new-virtual-network-for-managed-instances). 
- Avez-vous besoin de déployer votre option Managed Instance sur un réseau virtuel existant ou créez-vous un réseau ? 

   Si vous envisagez d’utiliser un réseau virtuel existant, vous devez modifier sa configuration pour prendre en compte Managed Instance. Pour plus d’informations, consultez [Modifier un réseau virtuel existant pour Managed Instance](#modify-an-existing-virtual-network-for-managed-instances). 

   Si vous envisagez de créer un réseau virtuel, consultez [Créer un réseau virtuel pour Managed Instance](#create-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Configuration requise

Pour la création d’une option Managed Instance, vous avez besoin d’un sous-réseau dédié conforme aux exigences suivantes dans le réseau virtuel :
- **Vide** : le sous-réseau ne doit contenir aucun autre service cloud associé et ne doit pas être un sous-réseau de passerelle. Vous ne pouvez pas créer d’option Managed Instance dans un sous-réseau qui contient des ressources autres qu’une instance managée ni ajouter ultérieurement d’autres ressources à l’intérieur du sous-réseau.
- **Pas de groupe de sécurité réseau** : aucun groupe de sécurité réseau ne doit être associé au sous-réseau.
- **Table de routage spécifique** : le sous-réseau doit avoir une table de routage utilisateur (UDR) avec un itinéraire Internet de tronçon suivant 0.0.0.0/0 comme seul itinéraire affecté. Pour plus d’informations, consultez [Créer la table de routage nécessaire et l’associer](#create-the-required-route-table-and-associate-it).
3. **DNS personnalisé éventuel** : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) à la liste. Pour plus d’informations, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
4. **Pas de point de terminaison de service** : le sous-réseau ne doit pas avoir de point de terminaison de service (stockage ou SQL) associé. Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
5. **Adresses IP suffisantes** : le sous-réseau doit avoir un minimum de 16 adresses IP. Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une nouvelle option Managed Instance si le sous-réseau de destination n’est pas compatible avec toutes les exigences précédentes. La réseau virtuel de destination et le sous-réseau doivent constamment respecter ces exigences (avant et après le déploiement), car toute violation peut entraîner l’entrée de l’instance dans un état défectueux et la rendre indisponible. Pour récupérer de cet état, vous devez créer une instance dans un réseau virtuel avec les stratégies réseau conformes, recréer des données de niveau d’instance et restaurer vos bases de données. Cela induit un temps d’arrêt important pour vos applications.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Déterminer la taille du sous-réseau pour les options Managed Instance

Quand vous créez une option Managed Instance, Azure alloue plusieurs machines virtuelles en fonction de la taille sélectionnée pendant le provisionnement. Étant donné que ces machines virtuelles sont associées à votre sous-réseau, elles nécessitent des adresses IP. Pour garantir la haute disponibilité pendant les opérations normales et la maintenance du service, Azure peut allouer des machines virtuelles supplémentaires. Par conséquent, le nombre d’adresses IP nécessaires dans un sous-réseau est supérieur au nombre d’options Managed Instance dans ce sous-réseau. 

Par défaut, une option Managed Instance a besoin d’un minimum de 16 adresses IP dans un sous-réseau et peut en utiliser jusqu’à 256. Ainsi, vous pouvez utiliser des masques de sous-réseau /28 à /24 lors de la définition de vos plages d’adresses IP de sous-réseau. 

Si vous envisagez de déployer plusieurs options Managed Instance à l’intérieur du sous-réseau et avez besoin d’optimiser la taille de ce dernier, utilisez ces paramètres pour former un calcul : 

- Azure utilise cinq adresses IP dans le sous-réseau pour ses besoins propres. 
- Chaque instance à usage général nécessite deux adresses. 

**Exemple** : vous prévoyez d’utiliser huit options Managed Instance. Cela signifie que vous avez besoin de 5 + 8 * 2 = 21 adresses IP. Comme les plages d’adresses IP sont définies par puissance de 2, vous avez besoin d’une plage de 32 (2 ^ 5) adresses IP. Ainsi, vous devez réserver le sous-réseau avec un masque de sous-réseau de /27. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Créer un réseau virtuel pour les options Managed Instance 

La création d’un réseau virtuel Azure est un prérequis à celle d’une option Managed Instance. Vous pouvez utiliser le portail Azure, [PowerShell](../virtual-network/quick-create-powershell.md) ou [Azure CLI](../virtual-network/quick-create-cli.md). La section suivante présente les étapes qui font appel au portail Azure. Les informations décrites ici s’appliquent à chacune de ces méthodes.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Recherchez **Réseau virtuel**, puis cliquez dessus. Vérifiez ensuite que **Resource Manager** est sélectionné en tant que mode de déploiement, puis cliquez sur **Créer**.

   ![créer un réseau virtuel](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Remplissez le formulaire de réseau virtuel avec les informations demandées, comme l’illustre la capture d’écran suivante :

   ![formulaire de création de réseau virtuel](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Cliquez sur **Créer**.

   L’espace d’adressage et le sous-réseau sont spécifiés en notation CIDR. 

   > [!IMPORTANT]
   > Les valeurs par défaut créent un sous-réseau qui accepte tout l’espace d’adressage du réseau virtuel. Si vous choisissez cette option, vous ne pouvez pas créer d’autres ressources à l’intérieur du réseau virtuel autres que d’une option Managed Instance. 

   L’approche recommandée est la suivante : 
   - Calculer la taille du sous-réseau en suivant la section [Déterminer la taille du sous-réseau pour Managed Instance](#determine-the-size-of-subnet-for-managed-instances)  
   - Évaluer les besoins pour le reste du réseau virtuel 
   - Renseigner les plages d’adresses du réseau virtuel et du sous-réseau en conséquence 

   Vérifiez que les points de terminaison de service sont **désactivés**. 

   ![formulaire de création de réseau virtuel](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Créer la table de routage nécessaire et l’associer

1. Connectez-vous au portail Azure.  
2. Recherchez **Table de routage**, puis cliquez dessus. Cliquez ensuite sur **Créer** depuis la page Table de routage.

   ![formulaire de création de table de routage](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Créez un itinéraire Internet de tronçon suivant 0.0.0.0/0, comme le montrent les captures d’écran suivantes :

   ![ajouter une table de routage](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![itinéraire](./media/sql-database-managed-instance-tutorial/route.png)

4. Associez cet itinéraire au sous-réseau de l’option Managed Instance, comme le montrent les captures d’écran suivantes :

    ![sous-réseau](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![définir une table de routage](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![définir une table de routage - enregistrer](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Une fois votre réseau virtuel créé, vous êtes prêt à créer votre option Managed Instance.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modifier un réseau virtuel existant pour des options Managed Instance 

Les questions et réponses figurant dans cette section vous montrent comment ajouter une option Managed Instance à un réseau virtuel existant. 

**Utilisez-vous un modèle de déploiement classique ou Resource Manager pour le réseau virtuel existant ?** 

Vous pouvez uniquement créer une option Managed Instance dans des réseaux virtuels Resource Manager. 

**Voulez-vous créer un sous-réseau pour une instance gérée SQL ou en utilisez un existant ?**

Si vous voulez en créer un : 

- Calculez la taille du sous-réseau en suivant les instructions de la section [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances).
- Suivez les étapes décrites dans [Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel](../virtual-network/virtual-network-manage-subnet.md). 
- Créez une table de routage qui contient une seule entrée, **0.0.0.0/0**, en tant qu’itinéraire Internet de tronçon suivant et associez-la au sous-réseau de l’option Managed Instance.  

Si vous voulez créer une option Managed Instance à l’intérieur d’un sous-réseau existant : 
- Vérifiez que si le sous-réseau est vide (il est impossible de créer une option Managed Instance dans un sous-réseau qui contient d’autres ressources, notamment le sous-réseau de passerelle). 
- Calculez la taille du sous-réseau en suivant les instructions de la section [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances) et vérifiez qu’il est correctement dimensionné. 
- Vérifiez que les points de terminaison de service ne sont pas activés sur le sous-réseau.
- Vérifiez qu’il n’y a aucun groupe de sécurité réseau associé au sous-réseau. 

**Avez-vous configuré un serveur DNS personnalisé ?** 

Dans l’affirmative, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md). 

- Créez la table de routage nécessaire et associez-la : consultez [Créer la table de routage nécessaire et l’associer](#create-the-required-route-table-and-associate-it).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour un tutoriel qui montre comment créer un réseau virtuel, créer une option Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Création d’une option Azure SQL Database Managed Instance](sql-database-managed-instance-tutorial-portal.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
