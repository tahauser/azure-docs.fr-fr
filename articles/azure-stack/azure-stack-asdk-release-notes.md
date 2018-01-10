---
title: "Notes de publication du kit de développement Microsoft Azure Stack | Microsoft Docs"
description: "Améliorations, correctifs et problèmes connus pour le kit de développement Azure Stack."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: andredm
ms.openlocfilehash: 49d26defef56e6fc174cda57b24a126cd77227e4
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du kit de développement Azure Stack

*S’applique à : Kit de développement Azure Stack*

Ces notes de publication fournissent des informations sur les améliorations, les correctifs et les problèmes connus relatifs au kit de développement Azure Stack. Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201711221"></a>Build 20171122.1

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

- Consultez la section [Nouvelles fonctionnalités et correctifs](azure-stack-update-1711.md#new-features-and-fixes) des notes de mise à jour d’Azure Stack 1711 pour les systèmes intégrés Azure Stack.

    > [!IMPORTANT]
    > Certains des éléments répertoriés dans la section **Nouvelles fonctionnalités et correctifs** concernent uniquement les systèmes intégrés Azure Stack.

### <a name="known-issues"></a>Problèmes connus
 
#### <a name="deployment"></a>Déploiement
- Vous devez spécifier un serveur de temps par adresse IP au cours du déploiement.

#### <a name="infrastructure-management"></a>Gestion de l’infrastructure
- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.
- Le modèle et l’adresse IP du contrôleur BMC (Baseboard Management Controller) ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle. Ce comportement est attendu dans le kit de développement Azure Stack.

#### <a name="portal"></a>Portail
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
-  Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide. 

   Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.

- Le message d’avertissement **Activation requise** s’affichera pour vous inviter à enregistrer votre kit de développement Azure Stack. Il s’agit du comportement attendu.
- Si vous cliquez sur le lien **Composant** à partir de n'importe quelle alerte **Rôle d'infrastructure**, le panneau **Vue d'ensemble** qui en résulte essaie de se charger et échoue. De plus, le panneau **Vue d'ensemble** n'expire pas.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Lorsque vous tentez d’ajouter des éléments à la Place de marché d’Azure Stack à l’aide de l’option **Ajouter à partir d’Azure**, certains éléments disponibles en téléchargement peuvent ne pas être visibles.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création. 
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

#### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.
- Les opérateurs Azure Stack peuvent être dans l’impossibilité de déployer, supprimer ou modifier des réseaux virtuels ou des groupes de sécurité réseau. Ce problème se produit principalement lors des tentatives de mise à jour ultérieures du même package. Il est dû à un problème d’empaquetage avec une mise à jour, que nous étudions actuellement.
- L’équilibrage de charge interne gère mal les adresses MAC des machines virtuelles principales, ce qui arrête les instances Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
 
#### <a name="usage-and-billing"></a>Utilisation et facturation
- Les données d’utilisation des adresses IP publiques affichent la même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

#### <a name="identity"></a>Identité

Dans les environnements déployés des services de fédération Azure Active Directory (AD FS), le compte **azurestack\azurestackadmin** n’est plus le propriétaire de l’abonnement Fournisseur par défaut. Au lieu de vous connecter au **portail d’administration / point de terminaison adminmanagement** avec le compte **azurestack\azurestackadmin**, vous pouvez utiliser le compte **azurestack\cloudadmin**, afin de pouvoir gérer et utiliser l’abonnement Fournisseur par défaut.

> [!IMPORTANT]
> Même si le compte **azurestack\cloudadmin** est propriétaire de l’abonnement Fournisseur par défaut dans les environnements AD FS déployés, il ne dispose pas des autorisations nécessaires pour établir une connexion RDP avec l’hôte. Continuez à utiliser le compte **azurestack\azurestackadmin** ou le compte d’administrateur local pour vous connecter, accéder à l’hôte et le gérer en fonction des besoins.


## <a name="build-201710201"></a>Version 20171020.1

### <a name="improvements-and-fixes"></a>Améliorations et correctifs

Pour obtenir la liste des améliorations et des correctifs de la version 20171020.1, consultez la section [Améliorations et correctifs](azure-stack-update-1710.md#improvements-and-fixes) des notes de publication 1710 pour les systèmes intégrés Azure Stack. Certains des éléments répertoriés dans la section « Correctifs et améliorations de la qualité supplémentaires » sont pertinents uniquement pour les systèmes intégrés.

Les correctifs suivants ont également été appliqués :
- Résolution du problème dans lequel le fournisseur de ressources de calcul présentait un état inconnu.
- Résolution du problème dans lequel les quotas n’apparaissent pas dans le portail administrateur une fois que vous les avez créés et que vous essayez ensuite d’afficher les détails du plan.

### <a name="known-issues"></a>Problèmes connus

#### <a name="powershell"></a>PowerShell
- La version du module PowerShell AzureRM 1.2.11 est fournie avec une liste des modifications importantes. Pour des informations sur la mise à niveau à partir de la version 1.2.10, consultez le [guide de migration](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Déploiement
- Vous devez spécifier un serveur de temps par adresse IP au cours du déploiement.

#### <a name="infrastructure-management"></a>Gestion de l’infrastructure
- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.
- Le modèle et l’adresse IP du contrôleur BMC (Baseboard Management Controller) ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle. Ce comportement est attendu dans le kit de développement Azure Stack.

#### <a name="portal"></a>Portail
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
-  Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide. 

   Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.

- Le message d’avertissement **Activation requise** s’affichera pour vous inviter à enregistrer votre kit de développement Azure Stack. Il s’agit du comportement attendu.
- Dans les détails du message d’avertissement **Activation requise**, ne cliquez pas sur le lien vers le composant **AzureBridge**. Si vous faites cela, le panneau **Vue d’ensemble** ne se chargera pas et le délai d’expiration ne sera pas activé.
- Dans le portail administrateur, le message **Error fetching tenants** (Erreur lors de la récupération des locataires) peut s’afficher dans la zone **Notifications**. Vous pouvez ignorer sans problème ce message d’erreur.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Lorsque vous tentez d’ajouter des éléments à la Place de marché d’Azure Stack à l’aide de l’option **Ajouter à partir d’Azure**, certains éléments disponibles en téléchargement peuvent ne pas être visibles.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création. 
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

#### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
 
#### <a name="usage-and-billing"></a>Utilisation et facturation
- Les données d’utilisation des adresses IP publiques affichent la même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

## <a name="build-201709283"></a>Version 20170928.3

### <a name="known-issues"></a>Problèmes connus

#### <a name="powershell"></a>PowerShell
- La version du module PowerShell AzureRM 1.2.11 est fournie avec une liste des modifications importantes. Pour des informations sur la mise à niveau à partir de la version 1.2.10, consultez le [guide de migration](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Déploiement
- Vous devez spécifier un serveur de temps par adresse IP au cours du déploiement.

 #### <a name="infrastructure-management"></a>Gestion de l’infrastructure
- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.
- Le fournisseur de ressources de calcul présente un état inconnu.
- Le modèle et l’adresse IP du contrôleur BMC (Baseboard Management Controller) ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle. Ce comportement est attendu dans le kit de développement Azure Stack. 
   
#### <a name="portal"></a>Portail
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
- Le message d’avertissement **Activation requise** s’affichera pour vous inviter à enregistrer votre kit de développement Azure Stack. Il s’agit du comportement attendu.
- Dans les détails du message d’avertissement **Activation requise**, ne cliquez pas sur le lien vers le composant **AzureBridge**. Si vous faites cela, le panneau **Vue d’ensemble** ne se chargera pas et le délai d’expiration ne sera pas activé.
- Les quotas n’apparaissent pas dans le portail administrateur une fois que vous les avez créés et que vous essayez ensuite d’afficher les détails du plan. Pour résoudre ce problème, sous **Services et quotas**, cliquez sur **Ajouter**et ajoutez une nouvelle entrée.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
  
#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création.
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.

#### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est parfois appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.

#### <a name="sqlmysql"></a>SQL/MySQL
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
