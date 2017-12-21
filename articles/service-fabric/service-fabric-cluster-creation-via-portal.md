---
title: "Créer un cluster Service Fabric dans le portail Azure | Microsoft Docs"
description: "Cet article décrit comment configurer un cluster Service Fabric sécurisé dans Azure à l’aide du portail Azure et d’Azure Key Vault."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: be880efdcf1276252c76f27c2f2fd99edd606caa
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Création d’un cluster Service Fabric dans Azure à partir du portail Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portail Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ce guide vous mène pas à pas à travers les étapes de configuration d’un cluster Service Fabric sécurisé dans Azure à l’aide du portail Azure. Il vous permet de vous familiariser avec les procédures suivantes :

* configuration d’Azure Key Vault de manière à gérer des clés pour la sécurité des clusters ;
* création d’un cluster sécurisé dans le portail Azure ;
* authentification d’administrateurs à l’aide de certificat.

> [!NOTE]
> Pour accéder à des options de sécurité plus avancées, telles que l’authentification des utilisateurs avec Azure Active Directory et la configuration de certificats pour la sécurité des applications, [créez votre cluster à l’aide d’Azure Resource Manager][create-cluster-arm].
> 
> 

Un cluster sécurisé est un cluster qui empêche tout accès non autorisé à des opérations de gestion, notamment le déploiement, la mise à niveau et la suppression d’applications, de services et des données qu’ils contiennent. Un cluster non sécurisé est un cluster auquel toute personne peut se connecter à tout moment pour effectuer des opérations de gestion. Bien qu’il soit possible de créer un cluster non sécurisé, il est **vivement recommandé de créer un cluster sécurisé**. Un cluster non sécurisé **ne peut pas être sécurisé ultérieurement** , ce qui implique la création d’un nouveau cluster.

Les concepts appliqués sont les mêmes pour la création de clusters sécurisés, qu’il s’agisse de clusters Linux ou Windows. Pour en savoir plus et pour accéder à des scripts d’assistance dédiés à la création de clusters Linux sécurisés, consultez [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Les paramètres obtenus par le script d’assistance peuvent être saisis directement dans le portail, comme indiqué dans la section [Création d’un cluster dans le portail Azure](#create-cluster-portal).

## <a name="configure-key-vault"></a>Configurer Key Vault 
### <a name="log-in-to-azure"></a>Connexion à Azure
Ce guide utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.

Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Sélectionnez votre abonnement :

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="set-up-key-vault"></a>Configuration d’Azure Key Vault
Cette partie du guide vous présente la création d’un coffre de clés pour un cluster Service Fabric dans Azure et pour des applications Service Fabric. Pour obtenir un guide complet sur Key Vault, consultez le [Guide pratique de Key Vault][key-vault-get-started].

Service Fabric utilise des certificats X.509 pour sécuriser un cluster. Azure Key Vault permet de gérer des certificats pour des clusters Service Fabric dans Azure. Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster.

Le diagramme suivant illustre la relation entre Key Vault, un cluster Service Fabric et le fournisseur de ressources Azure qui utilise des certificats stockés dans Key Vault lorsqu’il crée un cluster :

![Installation de certificats][cluster-security-cert-installation]

#### <a name="create-a-resource-group"></a>Création d’un groupe de ressources
La première étape consiste à créer un nouveau groupe de ressources spécifiquement pour le coffre de clés. Il est recommandé de placer le coffre de clés dans son propre groupe de ressources pour vous permettre de supprimer des groupes de ressources de calcul et de stockage (par exemple, le groupe de ressources qui contient votre cluster Service Fabric) sans perdre vos clés et vos secrets. Le groupe de ressources qui contient votre coffre de clés doit se trouver dans la même région que le cluster qui l’utilise.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

#### <a name="create-key-vault"></a>Création d'un coffre de clés
Créez un coffre de clés dans le nouveau groupe de ressources. Le coffre de clés **doit être activé pour le déploiement** afin d’autoriser le fournisseur de ressources Service Fabric à obtenir des certificats à partir de ce coffre et à les installer sur les nœuds de cluster :

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Si vous avez un coffre de clés existant, vous pouvez l’activer pour le déploiement à l’aide de l’une des méthodes suivantes :

##### <a name="azure-powershell"></a>Azure PowerShell

```powershell
PS C:\Users\vturecek> Set-AzureRmKeyVaultAccessPolicy -VaultName 'myvault' -EnabledForDeployment
```

##### <a name="azure-cli"></a>Interface de ligne de commande Azure :

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


### <a name="add-certificates-to-key-vault"></a>Ajout de certificats à Key Vault
Les certificats sont utilisés dans Service Fabric à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications. Pour plus d’informations sur l’utilisation de certificats dans Service Fabric, consultez la page [Scénarios de sécurité d’un cluster Service Fabric][service-fabric-cluster-security].

#### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et de serveur (obligatoire)
Ce certificat est nécessaire pour sécuriser un cluster et empêcher un accès non autorisé à ce dernier. Il assure la sécurité du cluster de différentes manières :

* **Authentification du cluster :** authentifie la communication nœud à nœud pour la fédération du cluster. Seuls les nœuds qui peuvent prouver leur identité avec ce certificat peuvent être ajoutés au cluster.
* **Authentification du serveur :** authentifie les points de terminaison de gestion du cluster sur un client de gestion, afin que le client de gestion sache qu’il communique avec le véritable cluster. Ce certificat fournit également SSL pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.

Pour cela, le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
* Le nom d'objet du certificat doit correspondre au domaine servant à accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture de SSL pour les points de terminaison de gestion HTTPS du cluster et Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d'une autorité de certification pour le domaine `.cloudapp.azure.com` . Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d'une autorité de certification, le nom d'objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour votre cluster.

#### <a name="client-authentication-certificates"></a>Authentification de certificat client
Les certificats client supplémentaires authentifient les administrateurs pour les tâches de gestion de cluster. Service Fabric dispose de deux niveaux d’accès : **admin** et **read-only user**. Au minimum, un seul certificat pour un accès administrateur doit être utilisé. Pour un accès de niveau utilisateur supplémentaire, un certificat distinct doit être fourni. Pour en savoir plus sur les rôles d’accès, consultez la page [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric][service-fabric-cluster-security-roles].

Vous n’avez pas besoin de charger les certificats d’authentification client dans le Key Vault pour pouvoir travailler avec Service Fabric. Il suffit de fournir ces certificats aux utilisateurs qui sont autorisés à effectuer la gestion des clusters. 

> [!NOTE]
> Azure Active Directory est la méthode recommandée pour authentifier les clients pour des opérations de gestion de cluster. Pour utiliser Azure Active Directory, vous devez [créer un cluster à l’aide d’Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificats d’application (facultatif)
Un nombre quelconque de certificats supplémentaires peut être installé sur un cluster pour sécuriser une application. Avant de créer votre cluster, examinez les scénarios de sécurité d’application qui nécessitent l’installation d’un certificat sur les nœuds, notamment :

* Le chiffrement et déchiffrement de valeurs de configuration d’applications.
* Le chiffrement des données sur les nœuds lors de la réplication. 

Les certificats d’application ne peuvent pas être configurés lors de la création d’un cluster par le biais du portail Azure. Pour configurer les certificats d’application au moment de la configuration du cluster, vous devez [créer un cluster à l’aide d’Azure Resource Manager][create-cluster-arm]. Vous pouvez également ajouter des certificats d’application au cluster après sa création.

#### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Mise en forme de certificats pour une utilisation par un fournisseur de ressources Azure
Des fichiers de clé privée (.pfx) peuvent être ajoutés et utilisés directement par le biais de Key Vault. Toutefois, le fournisseur de ressources Azure nécessite que les clés soient stockées dans un format JSON spécial qui inclut le fichier .pfx en tant que chaîne encodée en base 64 et le mot de passe de clé privée. Pour répondre à ces exigences, les clés doivent être placées dans une chaîne JSON, puis stockées en tant que *secrets* dans Key Vault.

Pour faciliter ce processus, un module PowerShell est [disponible sur GitHub][service-fabric-rp-helpers]. Procédez comme suit pour utiliser le module :

1. Téléchargez le contenu complet du référentiel dans un répertoire local. 
2. Importez le module dans la fenêtre PowerShell :

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

La commande `Invoke-AddCertToKeyVault` dans ce module PowerShell met automatiquement en forme une clé privée de certificat dans une chaîne JSON et la charge vers Key Vault. Il permet d’ajouter le certificat de cluster et tous les certificats d’application supplémentaires à Key Vault. Répétez simplement cette étape pour tous les certificats supplémentaires à installer sur votre cluster.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Il s’agit de toutes les conditions préalables de Key Vault pour la configuration d’un modèle Service Fabric Cluster Resource Manager qui installe des certificats pour l’authentification de nœud, la sécurité des points de terminaison d’administration et les fonctionnalités de sécurité d’application supplémentaires qui utilisent des certificats X.509. À ce stade, vous disposez à présent la configuration suivante dans Azure :

* Groupe de ressources Key Vault
  * Key Vault
    * Certificat d’authentification de serveur de clusters

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Création d’un cluster dans le portail Azure
### <a name="search-for-the-service-fabric-cluster-resource"></a>Rechercher la ressource de cluster Service Fabric
![Rechercher le modèle de cluster Service Fabric sur le portail Azure.][SearchforServiceFabricClusterTemplate]

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur **Nouveau** pour ajouter un nouveau modèle de ressources. Recherchez le modèle de cluster Service Fabric dans **Marketplace** sous **Tout**.
3. Sélectionnez **Cluster Service Fabric** dans la liste.
4. Accédez au panneau **Cluster Service Fabric**, puis cliquez sur **Créer**.
5. Le panneau **Créer un cluster Service Fabric** inclut les quatre étapes suivantes.

#### <a name="1-basics"></a>1. Concepts de base
![Capture d’écran de la création d’un groupe de ressources.][CreateRG]

Dans le volet De base, vous devez fournir les informations de base de votre cluster.

1. Entrez le nom de votre cluster.
2. Choisissez un **Nom d’utilisateur** et un **Mot de passe** pour le bureau à distance pour les machines virtuelles.
3. Veillez à sélectionner **l’Abonnement** sur lequel vous souhaitez que votre cluster soit déployé, surtout si vous avez plusieurs abonnements.
4. Créez un **groupe de ressources**. Il est préférable de lui attribuer le même nom que le cluster, car cela vous permet de le retrouver plus facilement, surtout lorsque vous essayez d’apporter des modifications à votre déploiement ou de supprimer votre cluster.
   
   > [!NOTE]
   > Bien que vous puissiez décider d’utiliser un groupe de ressources existant, nous vous conseillons de créer un groupe de ressources. Cela facilite la suppression des clusters dont vous n’avez pas besoin.
   > 
   > 
5. Sélectionnez la **région** dans laquelle vous souhaitez créer le cluster. Vous devez utiliser la région de votre coffre de clés.

#### <a name="2-cluster-configuration"></a>2. Configuration de clusters
![Création d’un type de nœud][CreateNodeType]

Configurez vos nœuds de cluster. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Votre cluster peut avoir plusieurs types de nœud, mais le type de nœud principal (le premier que vous définissez sur le portail) doit avoir au moins cinq machines virtuelles, car il s’agit du type de nœud dans lequel les services du système Service Fabric sont placés. Ne configurez pas la zone **Propriétés de sélection élective** , car une propriété de sélection élective par défaut de « NodeTypeName » est ajoutée automatiquement.

> [!NOTE]
> Un scénario répandu si vous avez plusieurs types de nœuds est une application qui contient un service front-end et un service back-end. Vous souhaitez placer le service front-end sur des machines virtuelles plus petites (tailles de machine virtuelle de type D2) avec leurs ports ouverts à Internet, mais vous souhaitez placer le service back-end sur des machines virtuelles de plus grandes tailles (dont les tailles sont de type D4, D6, D15, etc.) dont les ports ne sont pas accessibles sur Internet.
> 
> 

1. Choisissez un nom pour votre type de nœud (1 à 12 caractères contenant uniquement des lettres et des chiffres).
2. La **taille** minimale des machines virtuelles pour le type de nœud principal dépend de la couche de **durabilité** que vous choisissez pour le cluster. La valeur par défaut du niveau de durabilité est Bronze. Pour en savoir plus sur la durabilité, consultez la page [Comment choisir la fiabilité et la durabilité du cluster Service Fabric][service-fabric-cluster-capacity].
3. Sélectionnez la taille de machine virtuelle et le niveau tarifaire. Les machines virtuelles de série D ont des lecteurs de disques SSD et sont vivement recommandées pour les applications avec état. N’utilisez pas les références de machine virtuelle incluant des cœurs partiels ou présentant une capacité de disque disponible inférieure à 7 Go. 
4. Le **nombre** minimum de machines virtuelles du type de nœud principal dépend du niveau de **fiabilité** que vous choisissez. La valeur par défaut du niveau de fiabilité est Silver. Pour en savoir plus sur la fiabilité, consultez la page [Comment choisir la fiabilité et la durabilité du cluster Service Fabric][service-fabric-cluster-capacity].
5. Choisissez le nombre de machines virtuelles du type de nœud. Vous pouvez augmenter ou réduire ultérieurement le nombre de machines virtuelles d’un type de nœud. Cependant, pour le type de nœud principal, le nombre de machines virtuelles minimum dépend du niveau de fiabilité que vous avez choisi. Les autres types de nœud peuvent avoir au moins 1 machine virtuelle.
6. Configurez des points de terminaison personnalisés. Ce champ vous permet d’entrer une liste séparée par des virgules des ports que vous souhaitez exposer par le biais de l’Azure Load Balancer à l’Internet public pour vos applications. Par exemple, si vous envisagez de déployer une application web dans votre cluster, saisissez « 80 » pour autoriser le trafic sur le port 80 dans votre cluster. Pour en savoir plus sur les points de terminaison, consultez la page [Communiquer avec des applications][service-fabric-connect-and-communicate-with-services].
7. Configurez les **diagnostics**du cluster. Par défaut, les diagnostics sont activés sur votre cluster afin de faciliter la résolution des problèmes. Si vous souhaitez désactiver les diagnostics, définissez **l’État** sur **Désactivé**. Nous vous recommandons de **ne pas** désactiver les diagnostics.
8. Sélectionnez le mode de mise à niveau Service Fabric que vous souhaitez associer à votre cluster. Sélectionnez **Automatique**si vous souhaitez que le système récupère automatiquement la dernière version disponible et essaye de mettre à niveau votre cluster vers cette version. Définissez le mode sur **Manuel**si vous souhaitez choisir une version prise en charge.

> [!NOTE]
> Nous prenons uniquement en charge les clusters qui exécutent des versions prises en charge de Service Fabric. Si vous sélectionnez le mode **Manuel** , vous êtes responsable de la mise à niveau de votre cluster vers une version prise en charge. Pour en savoir plus sur le mode de mise à niveau de Service Fabric, consultez le document [Mettre à niveau un cluster Service Fabric][service-fabric-cluster-upgrade].
> 
> 

#### <a name="3-security"></a>3. Sécurité
![Capture d’écran des configurations de sécurité sur le portail Azure.][SecurityConfigs]

L’étape finale consiste à fournir des informations de certificat pour sécuriser le cluster à l’aide de Key Vault et les informations de certificat créées précédemment.

* Remplissez les champs de certificat principaux par la sortie obtenue du chargement du **certificat de cluster** dans Key Vault à l’aide de la commande PowerShell `Invoke-AddCertToKeyVault`.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Activez la case **Configurer les paramètres avancés** pour saisir les certificats clients pour le **Client d’administration** et le **Client en lecture seule**. Dans ces champs, saisissez l’empreinte de votre certificat de client d’administration et l’empreinte de votre certificat de client en lecture seule, le cas échéant. Lorsque les administrateurs tentent de se connecter au cluster, ils se voient attribuer l’accès uniquement s’ils disposent d’un certificat avec une empreinte qui correspond aux valeurs entrées ici.  

#### <a name="4-summary"></a>4. Résumé

Pour achever la création du cluster, cliquez sur **Résumé** de manière à afficher les configurations que vous avez fournies, ou téléchargez le modèle Azure Resource Manager utilisé pour déployer votre cluster. Lorsque vous avez fourni les paramètres obligatoires, le bouton **OK** s’affiche en vert. Vous pouvez cliquer dessus pour démarrer le processus de création du cluster.

Vous pouvez voir la progression de la création dans les notifications. (Cliquez sur l’icône représentant une cloche près de la barre d’état dans l’angle supérieur droit de votre écran.) Si vous avez cliqué sur **Épingler au tableau d’accueil** pendant la création du cluster, **Déploiement du cluster Service Fabric** apparaît épinglé au **tableau d’accueil**.

### <a name="view-your-cluster-status"></a>Afficher l’état de votre cluster
![Capture d’écran des détails du cluster dans le tableau de bord.][ClusterDashboard]

Une fois votre cluster créé, vous pouvez l’inspecter dans le portail :

1. Accédez à **Parcourir**, puis cliquez sur **Clusters Service Fabric**.
2. Recherchez votre cluster et cliquez dessus.
3. Vous pouvez maintenant voir les détails de votre cluster dans le tableau de bord, notamment le point de terminaison du cluster et un lien vers Service Fabric Explorer.

La section **Surveillance des nœuds** du panneau du tableau de bord du cluster indique le nombre de machines virtuelles intègres et de machines virtuelles non intègres. Pour plus d’informations sur l’intégrité du cluster, consultez [Présentation du contrôle d’intégrité de Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l’état. Cette situation est appelée « conservation du quorum ». Ainsi, il est généralement déconseillé d’arrêter tous les ordinateurs du cluster, sauf si vous avez d’abord effectué une [sauvegarde complète de votre état][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connexion distante à une instance de jeu de mise à l’échelle de machine virtuelle ou à un nœud de cluster
Chacune des valeurs NodeTypes que vous spécifiez dans votre cluster entraîne la configuration d’un groupe de machines virtuelles identiques. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé à l’aide de certificats pour l’authentification de la gestion. Ensuite, [connectez-vous à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [gérer les secrets d’application](service-fabric-application-secret-management.md).  Découvrez également les [options de support de Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
