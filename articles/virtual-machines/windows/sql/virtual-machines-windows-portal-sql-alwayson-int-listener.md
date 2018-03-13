---
title: "Créer un écouteur de groupe de disponibilité SQL Server dans des machines virtuelles Azure | Microsoft Docs"
description: "Instructions pas à pas pour la création d’un écouteur pour un groupe de disponibilité AlwaysOn pour SQL Server dans des machines virtuelles Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: 0399f9ef969098216e080140a67f81725b670115
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurer un équilibreur de charge pour un groupe de disponibilité AlwaysOn dans Azure
Cet article explique comment créer un équilibreur de charge pour un groupe de disponibilité SQL Server AlwaysOn dans des machines virtuelles Azure s’exécutant avec Azure Resource Manager. Un groupe de disponibilité nécessite un équilibreur de charge lorsque les instances SQL Server se trouvent sur des machines virtuelles Azure. Cet équilibrage de charge stocke l’adresse IP de l’écouteur de groupe de disponibilité. Si un groupe de disponibilité englobe plusieurs régions, chaque région a besoin d’un équilibreur de charge.

Pour mener à bien cette tâche, vous devez avoir déployé un groupe de disponibilité SQL Server sur des machines virtuelles Azure qui s’exécutent avec Resource Manager. Les deux machines virtuelles SQL Server doivent appartenir au même groupe à haute disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pour créer automatiquement le groupe de disponibilité dans Resource Manager. Ce modèle crée automatiquement un équilibreur de charge interne. 

Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Cet article nécessite que vos groupes de disponibilité soient déjà configurés.  

Rubriques connexes :

* [Configurer des groupes de disponibilité AlwaysOn dans une machine virtuelle Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Dans ce document, vous créez et vous configurez un équilibreur de charge dans le portail Azure. Une fois le processus terminé, vous configurez le cluster afin qu’il utilise l’adresse IP de l’équilibreur de charge pour l’écouteur du groupe de disponibilité.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Créer et configurer l’équilibrage de charge dans le portail Azure
Dans cette partie de la tâche, procédez comme suit :

1. Dans le portail Azure, créez l’équilibreur de charge et configurez l’adresse IP.
2. Configurez le pool principal.
3. Créez la sonde. 
4. Configurez les règles d’équilibrage de charge.

> [!NOTE]
> Si les instances de SQL Server se trouvent dans plusieurs groupes et régions, effectuez chaque étape à deux reprises, une fois dans chaque groupe de ressources.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Étape 1 : Créer l’équilibreur de charge et configurer l’adresse IP
Créez d’abord l’équilibreur de charge. 

1. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles SQL Server. 

2. Dans le groupe de ressources, cliquez sur **Ajouter**.

3. Recherchez **équilibreur de charge** puis, dans les résultats de la recherche, sélectionnez **Équilibreur de charge**, publié par **Microsoft**.

4. Dans le panneau **Équilibrage de charge**, cliquez sur **Créer**.

5. Dans la boîte de dialogue **Créer l’équilibreur de charge**, configurez l’équilibreur de charge comme suit :

   | Paramètre | Valeur |
   | --- | --- |
   | **Name** |Nom de l’équilibrage de charge. Par exemple, **sqlLB**. |
   | **Type** |**Interne** : la plupart des implémentations utilisent un équilibreur de charge interne, ce qui permet aux applications du même réseau virtuel de se connecter au groupe de disponibilité.  </br> **Externe** : permet aux applications de se connecter au groupe de disponibilité via une connexion Internet publique. |
   | **Réseau virtuel** |Sélectionnez le réseau virtuel contenant les instances de SQL Server. |
   | **Sous-réseau** |Sélectionnez le sous-réseau contenant les instances de SQL Server. |
   | **Affectation d’adresses IP** |**Statique** |
   | **Adresse IP privée** |Spécifiez une adresse IP disponible à partir du sous-réseau. Vous allez l’utiliser pour créer un écouteur sur le cluster. Dans un script PowerShell plus loin dans cet article, vous utilisez cette adresse pour la variable `$ILBIP`. |
   | **Abonnement** |Si vous avez plusieurs abonnements, ce champ doit normalement apparaître. Sélectionnez l’abonnement que vous voulez associer à cette ressource. Normalement, il s’agit du même abonnement que pour toutes les ressources du groupe de disponibilité. |
   | **Groupe de ressources** |Sélectionnez le groupe de ressources contenant les instances de SQL Server. |
   | **Lieu** |Sélectionnez l’emplacement Azure contenant les instances de SQL Server. |

6. Cliquez sur **Créer**. 

Azure crée l’équilibreur de charge. Cet équilibrage de charge appartient à un réseau, un sous-réseau, un groupe de ressources et un emplacement spécifiques. Une fois qu’Azure a terminé la tâche, vérifiez les paramètres de l’équilibreur de charge dans Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Étape 2 : Configurer le pool principal
Azure appelle *pool principal* ce pool d’adresses principal. En l’occurrence, le pool principal est constitué des adresses des deux instances de SQL Server dans votre groupe de disponibilité. 

1. Dans votre groupe de ressources, cliquez sur l’équilibreur de charge que vous avez créé. 

2. Dans **Paramètres**, cliquez sur **Pools principaux**.

3. Dans **Pools principaux**, cliquez sur **Ajouter** pour créer un pool d’adresses principal. 

4. Dans **Ajouter un pool principal** sous **Nom**, entrez un nom pour le pool principal.

5. Sous **Machines virtuelles**, cliquez sur **Ajouter une machine virtuelle**. 

6. Sous **Choisir des machines virtuelles**, cliquez sur **Choisir un groupe à haute disponibilité** puis spécifiez le groupe à haute disponibilité auquel les machines virtuelles SQL Server appartiennent.

7. Une fois que vous avez choisi le groupe à haute disponibilité, cliquez sur **Choisir les machines virtuelles**, sélectionnez les deux machines virtuelles qui hébergent des instances de SQL Server dans le groupe de disponibilité puis cliquez sur **Sélectionner**. 

8. Cliquez sur **OK** pour fermer les panneaux **Choisir les machines virtuelles** et **Ajouter un pool principal**. 

Azure met à jour les paramètres du pool d’adresses principal. Votre groupe à haute disponibilité a maintenant un pool de deux instances de SQL Server.

### <a name="step-3-create-a-probe"></a>Étape 3 : Créer une sonde
La sonde définit la façon dont Azure identifie l’instance de SQL Server qui détient l’écouteur du groupe de disponibilité. Azure sonde le service avec l’adresse IP sur un port que vous définissez lors de la création de la sonde.

1. Dans le panneau **Paramètres** de l’équilibreur de charge, cliquez sur **Sondes d’intégrité**. 

2. Dans le panneau **Sondes d’intégrité**, cliquez sur **Ajouter**.

3. Configurez la sonde dans le panneau **Ajouter une sonde** . Utilisez les valeurs suivantes pour configurer la sonde :

   | Paramètre | Valeur |
   | --- | --- |
   | **Name** |Nom de la sonde. Par exemple, **SQLAlwaysOnEndPointProbe**. |
   | **Protocole** |**TCP** |
   | **Port** |Vous pouvez utiliser n’importe quel port disponible. Par exemple, *59999*. |
   | **Intervalle** |*5* |
   | **Seuil de défaillance sur le plan de l’intégrité** |*2* |

4.  Cliquez sur **OK**. 

> [!NOTE]
> Vérifiez que le port que vous spécifiez est ouvert sur le pare-feu des deux instances de SQL Server. Les deux instances nécessitent une règle de trafic entrant pour le port TCP que vous utilisez. Pour plus d’informations, consultez [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

Azure crée la sonde puis l’utilise pour identifier l’instance de SQL Server qui a l’écouteur pour le groupe de disponibilité.

### <a name="step-4-set-the-load-balancing-rules"></a>Étape 4 : Définir les règles d’équilibrage de charge
Les règles d’équilibrage de charge déterminent comment l’équilibreur de charge route le trafic vers les instances de SQL Server. Pour cet équilibreur de charge, vous activez le retour direct du serveur, car une seule des deux instances de SQL Server a la ressource d’écouteur de groupe de disponibilité à un moment donné.

1. Dans le panneau **Paramètres** de l’équilibrage de charge, cliquez sur **Règles d’équilibrage de charge**. 

2. Dans le panneau **Règles d’équilibrage de charge**, cliquez sur **Ajouter**.

3. Dans le panneau **Ajouter une règle d’équilibrage de charge**, configurez la règle d’équilibrage de charge. Utilisez les paramètres suivants : 

   | Paramètre | Valeur |
   | --- | --- |
   | **Name** |Nom de la règle d’équilibrage de charge. Par exemple, **SQLAlwaysOnEndPointListener**. |
   | **Protocole** |**TCP** |
   | **Port** |*1433* |
   | **Port principal** |*1433*. Cette valeur est ignorée, car cette règle utilise **Adresse IP flottante (retour direct du serveur)**. |
   | **Sonde** |Utilisez le nom de la sonde que vous avez créée pour cet équilibrage de charge. |
   | **Persistance de session** |**Aucun** |
   | **Délai d’inactivité (minutes).** |*4* |
   | **Adresse IP flottante (retour serveur direct)** |**Activé** |

   > [!NOTE]
   > Il peut être nécessaire de faire défiler le panneau vers le bas pour voir tous les paramètres.
   > 

4. Cliquez sur **OK**. 
5. Azure configure la règle d’équilibrage de charge. L’équilibreur de charge est maintenant configuré pour router le trafic vers l’instance de SQL Server qui héberge l’écouteur pour le groupe de disponibilité. 

À ce stade, le groupe de ressources a un équilibreur de charge qui connecte les deux ordinateurs SQL Server. L’équilibreur de charge contient également une adresse IP pour l’écouteur du groupe de disponibilité AlwaysOn SQL Server, de sorte que l’un ou l’autre des ordinateurs puisse répondre aux demandes pour les groupes de disponibilité.

> [!NOTE]
> Si vos instances de SQL Server se trouvent dans deux régions distinctes, répétez les étapes dans l’autre région. Chaque région nécessite un équilibrage de charge. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour qu’il utilise l’adresse IP de l’équilibrage de charge
L’étape suivante consiste à configurer l’écouteur sur le cluster et à le mettre en ligne. Effectuez les actions suivantes : 

1. Créez l’écouteur du groupe de disponibilité sur le cluster de basculement. 

2. Mettez l’écouteur en ligne.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Étape 5 : Créer l’écouteur du groupe de disponibilité sur le cluster de basculement
Dans cette étape, vous créez manuellement l’écouteur du groupe de disponibilité dans le Gestionnaire du cluster de basculement et dans SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Vérifiez la configuration de l’écouteur

Si les ressources et les dépendances du cluster sont correctement configurées, vous devez être en mesure de voir l’écouteur dans SQL Server Management Studio. Procédez comme suit pour définir le port de l’écouteur :

1. Démarrez SQL Server Management Studio, puis connectez-vous au réplica principal.

2. Accédez à **Haute disponibilité AlwaysOn** > **Groupes de disponibilité** > **Écouteurs de groupe de disponibilité**.  
    Vous devez maintenant voir le nom de l'écouteur que vous avez créé dans le Gestionnaire du cluster de basculement. 

3. Cliquez avec le bouton droit sur le nom de l’écouteur puis cliquez sur **Propriétés**.

4. Dans la zone **Port**, spécifiez le numéro de port pour l’écouteur du groupe de disponibilité à l’aide du paramètre $EndpointPort utilisé précédemment (valeur par défaut : 1433) puis cliquez sur **OK**.

Vous disposez maintenant d’un groupe de disponibilité dans des machines virtuelles Azure s’exécutant en mode Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion à l’écouteur
Testez la connexion en procédant comme suit :

1. Envoyez une requête RDP à une instance de SQL Server qui se trouve dans le même réseau virtuel, mais qui ne détient pas le réplica. Ce serveur peut être une autre instance de SQL Server dans le cluster.

2. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** au réplica principal par le biais de l’écouteur avec une authentification Windows :
   
        sqlcmd -S <listenerName> -E

La connexion SQLCMD se connecte automatiquement à l’instance de SQL Server qui héberge le réplica principal. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Créer une adresse IP pour un groupe de disponibilité supplémentaire

Chaque groupe de disponibilité utilise un écouteur distinct. Chaque écouteur possède sa propre adresse IP. Le même équilibreur de charge doit être utilisé pour conserver l’adresse IP des écouteurs supplémentaires. Après avoir créé un groupe de disponibilité, ajoutez l’adresse IP à l’équilibreur de charge, puis configurez l’écouteur.

Pour ajouter une adresse IP à un équilibreur de charge avec le portail Azure, procédez comme suit :

1. Dans le portail Azure, ouvrez le groupe de ressources qui contient l’équilibreur de charge puis cliquez sur celui-ci. 

2. Sous **PARAMÈTRES**, cliquez sur **Pool d’adresses IP frontales** puis cliquez sur **Ajouter**. 

3. Sous **Ajouter une adresse IP frontale**, attribuez un nom au serveur frontal. 

4. Vérifiez que le **réseau virtuel** et le **sous-réseau** sont identiques aux instances SQL Server.

5. Définissez l’adresse IP de l’écouteur. 
   
   >[!TIP]
   >Vous pouvez la définir en tant qu’adresse IP statique et taper une adresse qui n’est actuellement pas utilisée dans le sous-réseau. Vous pouvez aussi la définir en tant qu’adresse IP dynamique et enregistrer le nouveau pool d’adresses IP frontales. Dans ce dernier cas, le portail Azure affecte automatiquement une adresse IP disponible au pool. Vous pouvez alors rouvrir le pool d’adresses IP frontales et changer l’affectation en statique. 

6. Enregistrez l’adresse IP de l’écouteur. 

7. Ajoutez une sonde d’intégrité en utilisant les paramètres suivants :

   |Paramètre |Valeur
   |:-----|:----
   |**Name** |Nom destiné à identifier la sonde.
   |**Protocole** |TCP
   |**Port** |Un port TCP non utilisé doit être disponible sur toutes les machines virtuelles. Il ne peut pas être utilisé à d’autres fins. Deux écouteurs distincts ne peuvent pas utiliser un même port de sonde. 
   |**Intervalle** |Laps de temps entre les différentes tentatives de la sonde. Utilisez la valeur par défaut (5).
   |**Seuil de défaillance sur le plan de l’intégrité** |Nombre de seuils consécutifs qui doivent échouer avant qu’une machine virtuelle soit considérée comme défectueuse.

8. Cliquez sur **OK** pour enregistrer la sonde. 

9. Créez une règle d’équilibrage de charge. Cliquez sur **Règles d’équilibrage de charge** puis sur **Ajouter**.

10. Configurez la nouvelle règle d’équilibrage de charge en utilisant les paramètres suivants :

   |Paramètre |Valeur
   |:-----|:----
   |**Name** |Nom destiné à identifier la règle d’équilibrage de charge. 
   |**Adresse IP du serveur frontal** |Sélectionnez l’adresse IP que vous avez créée. 
   |**Protocole** |TCP
   |**Port** |Utilisez le port utilisé par les instances SQL Server. Une instance par défaut utilise le port 1433, à moins que vous l’ayez changé. 
   |**Port principal** |Utilisez la même valeur que **Port**.
   |**Pool back-end** |Pool qui contient les machines virtuelles dotées des instances SQL Server. 
   |**Sonde d’intégrité** |Choisissez la sonde que vous avez créée.
   |**Persistance de session** |Aucun
   |**Délai d’inactivité (minutes).** |Valeur par défaut (4)
   |**Adresse IP flottante (retour direct du serveur)** | activé

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurer le groupe de disponibilité pour qu’il utilise la nouvelle adresse IP

Pour terminer la configuration du cluster, répétez les étapes que vous avez suivies pour créer le premier groupe de disponibilité. Autrement dit, configurez le [cluster pour qu’il utilise la nouvelle adresse IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Après avoir ajouté une adresse IP pour l’écouteur, configurez le groupe de disponibilité supplémentaire comme suit : 

1. Vérifiez que le port de la sonde pour la nouvelle adresse IP est ouvert sur les deux machines virtuelles SQL Server. 

2. [Dans Cluster Manager, ajoutez le point d’accès client](#addcap).

3. [Configurez la ressource IP du groupe de disponibilité](#congroup).

   >[!IMPORTANT]
   >Au moment de créer l’adresse IP, utilisez l’adresse IP que vous avez ajoutée à l’équilibreur de charge.  

4. [Rendez la ressource de groupe de disponibilité de SQL Server dépendant du point d’accès client](#dependencyGroup).

5. [Créez une dépendance entre la ressource du point d’accès client et l’adresse IP](#listname).
 
6. [Définissez les paramètres de cluster dans PowerShell](#setparam).

Une fois que vous avez configuré le groupe de disponibilité pour qu’il utilise la nouvelle adresse IP, configurez la connexion à l’écouteur. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Ajouter la règle d’équilibrage de charge pour le groupe de disponibilité distribué

Si un groupe de disponibilité participe à un groupe de disponibilité distribué, l’équilibrage de charge a besoin d’une règle supplémentaire. Cette règle stocke le port utilisé par l’écouteur de groupe de disponibilité distribué.

>[!IMPORTANT]
>Cette étape s’applique uniquement si le groupe de disponibilité fait partie d’un [groupe de disponibilité distribué](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Sur chaque serveur qui est inclus dans le groupe de disponibilité distribué, créez une règle de trafic entrant sur le port TCP de l’écouteur de groupe de disponibilité distribué. La documentation utilise 5022 dans de nombreux exemples. 

1. Dans le portail Azure, cliquez sur l’équilibreur de charge, puis sur **Équilibreurs de charge**, et enfin sur **+Ajouter**. 

1. Créez la nouvelle règle d’équilibrage de charge avec les paramètres suivants :

   |Paramètre |Valeur
   |:-----|:----
   |**Name** |Ajoutez un nom pour identifier la règle d’équilibrage de charge pour le groupe de disponibilité distribué. 
   |**Frontend IP address (Adresse IP frontale)** |Utilisez la même adresse IP de serveur frontal que le groupe de disponibilité.
   |**Protocole** |TCP
   |**Port** |5022 - le port pour [l’écouteur de point de terminaison de groupe de disponibilité distribué](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Peut être n’importe quel port disponible.  
   |**Port principal** | 5022 - Utilisez la même valeur que **Port**.
   |**Pool back-end** |Pool qui contient les machines virtuelles dotées des instances SQL Server. 
   |**Sonde d’intégrité** |Choisissez la sonde que vous avez créée.
   |**Persistance de session** |Aucun
   |**Délai d’inactivité (minutes).** |Valeur par défaut (4)
   |**Adresse IP flottante (retour direct du serveur)** | activé

Répétez ces étapes pour l’équilibrage de charge sur les autres groupes de disponibilité qui participent aux groupes de disponibilité distribués.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un groupe de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure dans des régions différentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
