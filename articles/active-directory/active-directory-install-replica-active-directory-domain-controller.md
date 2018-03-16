---
title: "Installer des contrôleurs de domaine de réplication pour un domaine Active Directory local sur des machines virtuelles Azure | Microsoft Docs"
description: "Guide pratique pour installer des contrôleurs de domaine de réplication pour un domaine Active Directory local sur des machines virtuelles Azure dans un réseau virtuel Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: f4e64fbc6c2fda026297b69bd54471d49b6785a1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installation d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure
Cet article explique comment installer des contrôleurs de domaine supplémentaires en vue de les utiliser comme contrôleurs de domaine de réplication pour un domaine Active Directory local sur des machines virtuelles Azure dans un réseau virtuel Azure. Vous pouvez également [installer une forêt Windows Server Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md). Pour savoir comment installer AD DS (Active Directory Domain Services) sur un réseau virtuel Azure, consultez [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Schéma du scénario
Dans ce scénario, des utilisateurs externes doivent accéder à des applications qui s'exécutent sur des serveurs appartenant à un domaine. Les machines virtuelles qui exécutent les serveurs d'applications et les contrôleurs de domaine de réplication sont installées sur un réseau virtuel Azure. Le réseau virtuel peut être connecté au réseau local par [ExpressRoute](../expressroute/expressroute-locations-providers.md), ou vous pouvez utiliser une connexion [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md), comme illustré ci-après : 

![Schéma d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure][1]

Les serveurs d’applications et les contrôleurs de domaine sont déployés dans des services cloud distincts afin de distribuer la charge de traitement et dans des groupes à haute disponibilité pour améliorer la tolérance de panne.
Les contrôleurs de domaine répliquent entre eux et avec les contrôleurs de domaine locaux à l'aide de la réplication Active Directory. Aucun outil de synchronisation n'est nécessaire.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Créer un site Active Directory local pour le réseau virtuel Azure
Vous pouvez créer un site dans Active Directory qui représente la région du réseau correspondant au réseau virtuel. Ce site peut aider à optimiser l’authentification, la réplication et d’autres opérations de localisation du contrôleur de domaine. Les étapes suivantes expliquent comment créer un site. Pour plus d’informations, consultez [Ajout d’un nouveau site](https://technet.microsoft.com/library/cc781496.aspx).

1. Ouvrez Sites et services Active Directory : **Gestionnaire de serveur** > **Outils** > **Sites et services Active Directory**.
2. Créez un site représentant la région où vous avez créé un réseau virtuel Azure : cliquez sur **Sites** > **Action** > **Nouveau site** > tapez le nom du nouveau site, par exemple Azure US West > sélectionnez un lien de site > **OK**.
3. Créez un sous-réseau et associez-le au nouveau site : double-cliquez sur **Sites** > cliquez avec le bouton droit sur **Sous-réseaux** > **Nouveau sous-réseau** > tapez la plage d’adresses IP du réseau virtuel (par exemple, 10.1.0.0/16 dans le schéma du scénario) > sélectionnez le nouveau site Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Création d'un réseau virtuel Azure
Pour créer un réseau virtuel Azure et configurer un réseau privé virtuel de site à site, suivez les étapes indiquées dans l’article [Créer une connexion de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

Vous pouvez également configurer la passerelle du réseau virtuel pour créer une connexion VPN de site à site sécurisée. Créez la connexion VPN de site à site entre le nouveau réseau virtuel et un périphérique VPN local. Pour connaître les instructions, consultez [Configurer une passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>création des machines virtuelles Azure pour les rôles de contrôleur de domaine
Pour créer des machines virtuelles afin d’héberger le rôle de contrôleur de domaine, répétez les étapes indiquées dans [Créer une machine virtuelle Windows avec le portail Azure](../virtual-machines/windows/quick-create-portal.md) en fonction des besoins. Déployez au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de panne. Si le réseau virtuel Azure possède au moins deux contrôleurs de domaine qui sont configurés de la même façon, vous pouvez placer les machines virtuelles qui exécutent ces contrôleurs de domaine dans un groupe à haute disponibilité pour une meilleure tolérance de panne.

Pour créer les machines virtuelles à l’aide de Windows PowerShell au lieu du portail Azure, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Réservez une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer, [installez Azure PowerShell](/powershell/azure/overview) , puis exécutez l’applet de commande Set-AzureStaticVNetIP. Par exemple : 

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Pour plus d’informations sur la configuration d’une adresse IP, consultez [Configuration d’une adresse IP interne statique pour une machine virtuelle](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installation des services AD DS sur des machines virtuelles Azure
Connectez-vous à une machine virtuelle et vérifiez que vous disposez d'une connectivité sur la connexion VPN de site à site ou la connexion ExpressRoute aux ressources sur votre réseau local. Ensuite, installez les services AD DS sur les machines virtuelles Azure. Vous pouvez appliquer la même procédure que celle utilisée pour installer un contrôleur de domaine supplémentaire sur votre réseau local (interface utilisateur, Windows PowerShell ou un fichier de réponses). Lors de l'installation des services AD DS, veillez à spécifier le nouveau volume comme emplacement de la base de données Active Directory, des journaux et de SYSVOL. Si vous avez besoin d’un rappel concernant l’installation des services AD DS, consultez [Installer les services de domaine Active Directory (Niveau 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [Installer un contrôleur de domaine de réplication Windows Server 2012 dans un domaine existant (Niveau 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>reconfiguration du serveur DNS pour le réseau virtuel
1. Pour obtenir la liste des noms de réseau virtuel, dans le [portail Azure](https://portal.azure.com), recherchez *Réseaux virtuels*, puis sélectionnez **Réseaux virtuels** pour afficher la liste. 
2. Ouvrez le réseau virtuel que vous souhaitez gérer, puis [reconfigurez les adresses IP du serveur DNS pour votre réseau virtuel](../virtual-network/manage-virtual-network.md#change-dns-servers) afin d’utiliser les adresses IP statiques attribuées aux contrôleurs de domaines de réplication au lieu des adresses IP pour les serveurs DNS locaux.
3. Pour que toutes les machines virtuelles des contrôleurs de domaine de réplication sur le réseau virtuel soient configurées de manière à utiliser des serveurs DNS sur le réseau virtuel, effectuez les opérations suivantes :
  1. Sélectionnez **Machines virtuelles**.
  2. Sélectionnez les machines virtuelles, puis **Redémarrer**. 
  3. Attendez que la machine virtuelle soit de nouveau **en cours d’exécution**, puis connectez-vous-y.

## <a name="create-vms-for-application-servers"></a>création de machines virtuelles pour les serveurs d'applications

Pour créer des machines virtuelles afin d’héberger le rôle de serveur d’applications, répétez les étapes indiquées dans [Créer une machine virtuelle Windows avec le portail Azure](../virtual-machines/windows/quick-create-portal.md) en fonction des besoins. Pour créer les machines virtuelles à l’aide de Microsoft PowerShell au lieu du portail Azure, consultez [Utilisation d’Azure PowerShell pour créer et configurer des machines virtuelles basées sur Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Le tableau suivant contient les paramètres suggérés.

| Paramètre | Valeurs |
| --- | --- |
|  **Choix d’une image** | Windows Server 2012 R2 Datacenter |
|  **Configuration de la machine virtuelle** |<p>Nom de machine virtuelle : Tapez un nom en une seule partie (par exemple, AppServer1).</p><p>Nouveau nom d'utilisateur : tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte intégré appelé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : saisissez un mot de passe</p> |
|  **Configuration de la machine virtuelle** |<p>Service cloud : sélectionnez **Créer un nouveau service de cloud computing** pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Nom DNS du service cloud : indiquez un nom global unique</p><p>Région/Groupe d’affinités/Réseau virtuel : indiquez le nom du réseau virtuel (par exemple WestUSVNet).</p><p>Compte de stockage : Choisissez **Utiliser un compte de stockage généré automatiquement** pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage lorsque de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Groupe à haute disponibilité : Choisissez **Créer un groupe à haute disponibilité**.</p><p>Nom du groupe à haute disponibilité : tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p> |
|  **Configuration de la machine virtuelle** |<p>Sélectionnez <b>Installer l'agent de la machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p> |
  
Une fois que chaque machine virtuelle supplémentaire est approvisionnée, connectez-vous et joignez-la au domaine. 
1. Dans **Gestionnaire de serveur** &gt; **Serveur local** &gt; **WORKGROUP** &gt; **Modifier…**, sélectionnez **Domaine**.
2. Entrez le nom de votre domaine local. 
3. Fournissez les informations d’identification d’un utilisateur de domaine.
4. Redémarrez la machine virtuelle.

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez [Bien démarrer avec les applets de commande Azure](/powershell/azure/overview) et le [Guide de référence des applets de commande Azure](/powershell/azure/get-started-azureps).
* [Instructions pour le déploiement de Windows Server Active Directory sur Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Comment télécharger des contrôleurs de domaine Hyper-V locaux existants vers Azure à l’aide de PowerShell Azure](http://support.microsoft.com/kb/2904015)
* [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Iaas des professionnels de l’informatique Microsoft Azure : principes de base des machines virtuelles (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Iaas des professionnels de l’informatique Microsoft Azure :(05) Création de réseaux virtuels pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Applets de commande de gestion Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
