---
title: Installation d’une forêt Active Directory sur un réseau virtuel Azure | Microsoft Docs
description: Didacticiel qui explique comment créer une forêt Active Directory sur une machine virtuelle dans un réseau virtuel Azure.
services: active-directory, virtual-network
keywords: 'machine virtuelle active directory, installer une forêt active directory, vidéos azure active directory  '
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
tags: ''
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: joflore
ms.openlocfilehash: 0b98b475f49847589398ca743fc3ac3b8bcbb381
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure
Cet article explique comment créer un environnement Windows Server Active Directory sur une machine virtuelle sur un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Dans ce cas, le réseau virtuel Azure n'est pas connecté à un réseau local.

Les rubriques suivantes peuvent également vous intéresser :

* Pour une vidéo qui illustre ces étapes, consultez [Installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Vous pouvez éventuellement [configurer un VPN virtuel de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , puis installer une nouvelle forêt ou étendre une forêt locale à un réseau virtuel Azure. Dans ce cas, consultez la page [Installation d'un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure](active-directory-install-replica-active-directory-domain-controller.md).
* Pour obtenir des recommandations sur l'installation des services de domaine Active Directory (AD DS) sur un réseau virtuel Azure, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Schéma du scénario
Dans ce scénario, des utilisateurs externes doivent accéder à des applications qui s'exécutent sur des serveurs appartenant à un domaine. Les machines virtuelles qui exécutent les serveurs d’applications et les machines virtuelles qui exécutent des contrôleurs de domaine sont installées dans leur propre service cloud dans un réseau virtuel Azure. Elles sont également incluses dans un groupe à haute disponibilité pour une meilleure tolérance de panne.

![Forêt Active Directory sur une machine virtuelle dans Azure Virtual Network][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Quelles sont les différences par rapport à une installation locale ?
Les différences entre l'installation d'un contrôleur de domaine dans Azure ou localement sont minimes. Le tableau suivant répertorie les principales différences.

| Pour configurer... | Local | réseau virtuel Azure |
| --- | --- | --- |
| **Adresse IP pour le contrôleur de domaine** |Attribuez une adresse IP statique aux propriétés d'adaptateur réseau |Exécutez la cmdlet Set-AzureStaticVNetIP pour attribuer une adresse IP statique |
| **Programme de résolution du client DNS** |Définissez l'adresse des serveurs DNS préféré et auxiliaire dans les propriétés d'adaptateur réseau des membres du domaine |Définissez l'adresse du serveur DNS dans les propriétés du réseau virtuel |
| **Stockage de base de données Active Directory** |(Facultatif) Définissez l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\ |Vous devez définir l'emplacement de stockage par défaut sur un autre lecteur que le lecteur C:\ |

## <a name="create-an-azure-virtual-network"></a>Création d'un réseau virtuel Azure
1. Connectez-vous au portail Azure.
2. Créez un réseau virtuel. Cliquez sur **Réseaux** > **Create a virtual network**. Utilisez les valeurs du tableau suivant pour compléter l'Assistant.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Détails du réseau virtuel** |<p>Nom : entrez le nom du réseau virtuel</p><p>Région : sélectionnez la région la plus proche</p> |
   |  **DNS et VPN** |<p>N'indiquez pas de serveur DNS</p><p>Ne sélectionnez pas d'option VPN</p> |
   |  **Espaces d’adressage du réseau virtuel** |<p>Nom du sous-réseau : entrez le nom du sous-réseau</p><p>Adresse IP de départ : <b>10.0.0.0</b></p><p>CIDR : <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Création de machines virtuelles exécutant le contrôleur de domaine et les rôles serveur DNS
Répétez les étapes suivantes pour créer des machines virtuelles pour héberger le rôle de contrôleur de domaine en fonction des besoins. Vous devez déployer au moins deux contrôleurs de domaine virtuels pour fournir la redondance et la tolérance de panne. Si le réseau virtuel Azure inclut au moins deux contrôleurs de domaine configurés de manière similaire (ce sont tous deux des catalogues globaux, ils exécutent un serveur DNS et aucun d'eux ne contient de rôle FSMO, etc.), placez alors les machines virtuelles qui exécutent ces catalogues globaux dans un groupe à haute disponibilité afin d'améliorer la tolérance aux pannes.

Pour créer les machines virtuelles à l’aide de Windows PowerShell au lieu de l’interface utilisateur, consultez l’exemple [Créer une machine virtuelle avec PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md).

1. Dans le portail Azure, sélectionnez **Nouveau** > **Compute**, puis sélectionnez une machine virtuelle. Utilisez les valeurs suivantes pour terminer l'Assistant. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Choix d’une image** |Windows Server 2012 R2 Datacenter |
   |  **Configuration de la machine virtuelle** |<p>Nom de machine virtuelle : Tapez un nom en une seule partie (par exemple, AzureDC1).</p><p>Nouveau nom d'utilisateur : tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte intégré appelé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : saisissez un mot de passe</p> |
   |  **Configuration de la machine virtuelle** |<p>Service cloud : sélectionnez <b>Créer un nouveau service de cloud computing</b> pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Nom DNS du service cloud : indiquez un nom global unique</p><p>Région/Groupe d’affinités/Réseau virtuel : indiquez le nom du réseau virtuel (par exemple WestUSVNet).</p><p>Choisissez <b>Utiliser un compte de stockage généré automatiquement pour la première machine virtuelle</b>, puis sélectionnez ce même nom de compte de stockage lors de la création de machines virtuelles supplémentaires qui hébergeront le rôle de contrôleur de domaine.</p><p>Groupe à haute disponibilité : Choisissez <b>Créer un groupe à haute disponibilité</b>.</p><p>Nom du groupe à haute disponibilité : tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p> |
   |  **Configuration de la machine virtuelle** |<p>Sélectionnez <b>Installer l'agent de la machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p> |
2. Attachez un disque à chaque machine virtuelle qui exécutera le rôle de serveur de contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez l'option **Préférences de cache d'hôte** définie sur **Aucun**. Pour ces étapes, consultez [Association d'un disque de données à une machine virtuelle Windows](../virtual-machines/windows/attach-managed-disk-portal.md)
3. Après votre première connexion à la machine virtuelle, ouvrez **Gestionnaire de serveur** > **Services de fichiers et de stockage** pour créer un volume sur ce disque à l'aide de NTFS.
4. Réservez une adresse IP statique pour les machines virtuelles qui exécuteront le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez Microsoft Web Platform Installer, [installez Azure PowerShell](/powershell/azure/overview) , puis exécutez l’applet de commande Set-AzureStaticVNetIP. Par exemple : 

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Pour plus d’informations sur la configuration d’une adresse IP, consultez [Configuration d’une adresse IP interne statique pour une machine virtuelle](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>installation de Windows Server Active Directory
Utilisez la même procédure que vous utilisez en local pour [installer AD DS](https://technet.microsoft.com/library/jj574166.aspx) (c’est-à-dire que vous pouvez utiliser l'interface utilisateur, un fichier de réponses ou Windows PowerShell). Vous devez fournir des informations d'identification d'administrateur pour installer une nouvelle forêt. Pour spécifier l'emplacement de la base de données Active Directory, des fichiers journaux et de SYSVOL, modifiez l'emplacement de stockage par défaut afin qu'il soit défini non sur le lecteur de système d'exploitation, mais sur l'autre disque de données que vous avez attaché à la machine virtuelle.

Lorsque l'installation du contrôleur de domaine est terminée, connectez-vous à celui-ci après vous être reconnecté à la machine virtuelle. N'oubliez pas de spécifier les informations d'identification du domaine.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Réinitialisation du serveur DNS pour le réseau virtuel Azure
1. Réinitialisez la configuration du redirecteur DNS sur le nouveau serveur contrôleur de domaine/DNS.
   1. Dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS**.
   2. Dans **Gestionnaire DNS**, cliquez avec le bouton droit sur le nom du serveur DNS, puis cliquez sur **Propriétés**.
   3. Sous l'onglet **Redirecteurs**, cliquez sur l'adresse IP du redirecteur, puis cliquez sur **Modifier**.  Sélectionnez l'adresse IP, puis cliquez sur **Supprimer**.
   4. Cliquez sur **OK** pour fermer l'éditeur, puis à nouveau sur **OK** pour fermer les propriétés du serveur DNS.
2. Mettez à jour les paramètres du serveur DNS pour le réseau virtuel.
   1. Cliquez sur **Réseaux virtuels** > double-cliquez sur le réseau virtuel que vous avez créé > **Configurer** > **Serveurs DNS**, tapez le nom et l’adresse IP de l’une des machines virtuelles qui exécutent le rôle du serveur de contrôleur de domaine/DNS, puis cliquez sur **Enregistrer**.
   2. Sélectionnez la machine virtuelle et cliquez sur **Redémarrer** pour que la machine virtuelle configure les paramètres de résolution DNS avec l'adresse IP du nouveau serveur DNS.

## <a name="create-vms-for-domain-members"></a>Création de machines virtuelles pour les membres du domaine
1. Répétez les étapes suivantes pour créer des machines virtuelles exécutables en tant que serveurs d’applications. Acceptez la valeur par défaut d'un paramètre, sauf si une autre valeur est suggérée ou requise.

   | Sur cette page de l'Assistant... | Spécifiez les valeurs suivantes |
   | --- | --- |
   |  **Choix d’une image** |Windows Server 2012 R2 Datacenter |
   |  **Configuration de la machine virtuelle** |<p>Nom de machine virtuelle : Tapez un nom en une seule partie (par exemple, AppServer1).</p><p>Nouveau nom d'utilisateur : tapez le nom d'un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur la machine virtuelle. Vous aurez besoin de ce nom pour vous connecter à la machine virtuelle pour la première fois. Le compte intégré appelé Administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmation : saisissez un mot de passe</p> |
   |  **Configuration de la machine virtuelle** |<p>Service cloud : sélectionnez **Créer un nouveau service de cloud computing** pour la première machine virtuelle et sélectionnez ce même nom de service cloud lors de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Nom DNS du service cloud : indiquez un nom global unique</p><p>Région/Groupe d’affinités/Réseau virtuel : indiquez le nom du réseau virtuel (par exemple WestUSVNet).</p><p>Compte de stockage : Choisissez **Utiliser un compte de stockage généré automatiquement** pour la première machine virtuelle, puis sélectionnez ce même nom de compte de stockage lorsque de la création de machines virtuelles supplémentaires qui hébergeront l'application.</p><p>Groupe à haute disponibilité : Choisissez **Créer un groupe à haute disponibilité**.</p><p>Nom du groupe à haute disponibilité : tapez un nom pour le groupe à haute disponibilité quand vous créez la première machine virtuelle, puis sélectionnez ce même nom lors de la création de machines virtuelles supplémentaires.</p> |
   |  **Configuration de la machine virtuelle** |<p>Sélectionnez <b>Installer l'agent de la machine virtuelle</b> et toutes les extensions dont vous avez besoin.</p> |
2. Une fois que chaque machine virtuelle supplémentaire est approvisionnée, connectez-vous et joignez-la au domaine. Dans le **Gestionnaire de serveur**, cliquez sur **Serveur Local** > **GROUPE DE TRAVAIL** > **MODIFIER...** puis sélectionnez **Domaine** et tapez le nom de votre domaine local. Spécifiez les informations d'identification d'un utilisateur de domaine, puis redémarrez la machine virtuelle pour terminer la jonction au domaine.

Pour créer les machines virtuelles à l’aide de Windows PowerShell au lieu de l'interface utilisateur, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pour plus d'informations sur l'utilisation de Windows PowerShell, consultez [Prise en main des cmdlets Azure](/powershell/azure/overview) et le [Guide de référence des cmdlets Azure](/powershell/azure/get-started-azureps).

## <a name="see-also"></a>Voir aussi
* [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Instructions pour le déploiement de Windows Server Active Directory sur Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Configuration d’un réseau VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Installation d’un réplica de contrôleur de domaine Active Directory dans un réseau virtuel Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Iaas des professionnels de l’informatique Microsoft Azure : principes de base des machines virtuelles (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Iaas des professionnels de l’informatique Microsoft Azure :(05) Création de réseaux virtuels pour la connectivité entre différents locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Présentation du réseau virtuel.](../virtual-network/virtual-networks-overview.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Guide de référence des cmdlets Azure](/powershell/azure/get-started-azureps)
* [Définition de l'adresse IP statique d'une machine virtuelle Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Attribution d'une adresse IP statique à une machine virtuelle Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Installation d'une nouvelle forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
* [Présentation de la virtualisation des services de domaine Active Directory (AD DS) (niveau 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
