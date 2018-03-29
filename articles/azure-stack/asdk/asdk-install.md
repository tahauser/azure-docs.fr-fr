---
title: Installer le Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Explique comment installer le Kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 7b8fe61731a9412c61152bc58e55deebb611d011
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installer le Kit de développement Azure Stack (ASDK)
Après la [préparation de l’ordinateur hôte ASDK](asdk-prepare-host.md), le kit ASDK peut être déployé dans l’image CloudBuilder.vhdx en suivant les étapes de cet article.

## <a name="install-the-asdk"></a>Installer l’ASDK
Les étapes de cet article vous montrent comment déployer le kit ASDK à l’aide d’une interface graphique utilisateur (GUI) que vous pouvez obtenir en téléchargeant et en exécutant le script PowerShell **asdk-installer.ps1**.

> [!NOTE]
> L’interface utilisateur du programme d’installation du Kit de développement Azure Stack est un script open source basé sur WCF et PowerShell.


1. Une fois que l’ordinateur hôte a correctement démarré dans l’image CloudBuilder.vhdx, connectez-vous avec les informations d’identification d’administrateur que vous avez spécifiées lorsque vous avez [préparé l’ordinateur hôte du Kit de développement](asdk-prepare-host.md) pour l’installation du ASDK. Il doit s’agir des mêmes informations d’identification d’administrateur local que celles de l’hôte du Kit de développement.
2. Ouvrez une console PowerShell avec élévation de privilèges et exécutez le script **&lt;lettre du lecteur>\AzureStack_Installer\asdk-installer.ps1** (qui peut maintenant être sur un lecteur autre que C:\ dans l’image Cloudbuilder.vhdx). Cliquez sur **Installer**.

    ![](media/asdk-install/1.PNG) 

3. Dans la zone déroulante **Type** du fournisseur d’identité, sélectionnez **Azure Cloud** ou **AD FS**. Sous **Mot de passe de l’administrateur local**, dans la zone **Mot de passe**, tapez le mot de passe de l’administrateur local (qui doit correspondre au mot de passe de l’administrateur local actuellement configuré), puis cliquez sur **Suivant**.
    - **Cloud Azure** : configure Azure Active Directory (Azure AD) comme fournisseur d’identité. Pour utiliser cette option, vous avez besoin d’une connexion Internet, du nom complet d’un locataire d’annuaire Azure AD au format *nomdomaine*.onmicrosoft.com ou d’un nom de domaine personnalisé vérifié Azure AD et des informations d’identification d’administrateur général de l’annuaire spécifié. 
    - **AD FS** : Le service d’annuaire de marquage par défaut est utilisé comme fournisseur d’identité. Le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre de la configuration.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Pour un résultat optimal, même si vous voulez utiliser un environnement Azure Stack déconnecté et AD FS comme fournisseur d’identité, il est préférable d’installer le kit ASDK en étant connecté à Internet. De cette façon, la version d’évaluation de Windows Server 2016 incluse avec l’installation du kit de développement peut être activée au moment du déploiement.
4. Sélectionnez une carte réseau à utiliser pour le Kit de développement, puis cliquez sur **Suivant**.

    ![](media/asdk-install/3.PNG)

5. Sélectionnez une configuration réseau DHCP ou statique pour la machine virtuelle BGPNAT01.
    > [!TIP]
    > La machine virtuelle BGPNAT01 est le routeur de périphérie qui fournit des fonctionnalités NAT et VPN pour Azure Stack.

    - **DHCP** (par défaut) : la machine virtuelle obtient la configuration réseau IP auprès du serveur DHCP.
    - **Statique** : utilisez cette option seulement si DHCP ne peut pas affecter une adresse IP valide pour l’accès Internet d’Azure Stack. **Une adresse IP statique doit être spécifiée avec la longueur du masque de sous-réseau au format CIDR (par exemple, 10.0.0.5/24)**.
    - Tapez une **adresse IP de serveur de temps** valide. Ce champ obligatoire définit le serveur de temps qui doit être utilisé par le Kit de développement. Ce paramètre doit être fourni sous la forme d’une adresse IP de serveur temps valide. Les noms de serveur ne sont pas pris en charge.

      > [!TIP]
      > Pour rechercher l’adresse IP d’un serveur de temps, visitez [pool.ntp.org](http:\\pool.ntp.org) ou effectuez un test ping time.windows.com. 

    - **Si vous le souhaitez**, vous pouvez aussi définir les valeurs suivantes :
        - **ID de VLAN** : définit l’ID du réseau local virtuel. Utilisez cette option seulement si l’hôte et AzS-BGPNAT01 doivent configurer l’ID du réseau local virtuel pour accéder au réseau physique (et à Internet). 
        - **Redirecteur DNS** : un serveur DNS est créé dans le cadre du déploiement d’Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.

    ![](media/asdk-install/4.PNG)

6. Dans la page **Vérification des propriétés de la carte d’interface réseau**, vous voyez une barre de progression. Une fois la vérification terminée, cliquez sur **Suivant**.

    ![](media/asdk-install/5.PNG)

9. Dans la page **Résumé**, cliquez sur **Déployer** pour démarrer l’installation du kit ASDK sur l’ordinateur hôte du kit de développement.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Ici, vous pouvez également copier les commandes de configuration PowerShell qui seront utilisées pour installer le kit de développement. C’est utile si vous avez besoin de [redéployer le kit ASDK sur l’ordinateur hôte à l’aide de PowerShell](asdk-deploy-powershell.md).

10. Si vous effectuez un déploiement Azure AD, vous serez invité à entrer les informations d’identification du compte administrateur général quelques minutes après le début de l’installation.

    ![](media/asdk-install/7.PNG)

11. Le processus de déploiement peut prendre quelques heures, au cours desquelles l’ordinateur hôte ne redémarre automatiquement qu’une seule fois. Si vous voulez surveiller la progression du déploiement, connectez-vous en tant que azurestack\AzureStackAdmin après le redémarrage de l’hôte du kit de développement. Quand le déploiement est terminé, la console PowerShell affiche le message suivant : **TERMINÉ : Action « Déploiement »**. 
    > [!IMPORTANT]
    > Si vous vous connectez en tant qu’administrateur local une fois que la machine est jointe au domaine, vous ne voyez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack\AzureStackAdmin pour vérifier qu’il est en cours d’exécution.

    ![](media/asdk-install/8.PNG)

Félicitations, vous avez installé le kit ASDK !

Si le déploiement échoue pour la même raison, vous pouvez effectuer un [redéploiement](asdk-redeploy.md) depuis le début ou utiliser les commandes PowerShell suivantes, dans la même fenêtre PowerShell avec des privilèges élevés, pour redémarrer le déploiement à partir de la dernière étape réussie :

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Étapes suivantes
[Configuration post-déploiement](asdk-post-deploy.md)