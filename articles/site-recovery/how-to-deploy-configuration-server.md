---
title: " Déployer le serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment déployer un serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: anoopkv
ms.openlocfilehash: e257ede08ac46ad863b4883b10399058e6f59f1f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-a-configuration-server"></a>Déployer un serveur de configuration

Vous devez déployer un serveur de configuration local quand vous utilisez le service [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure. Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure,.et gère la réplication des données. Cet article vous guide tout au long des étapes nécessaires pour déployer le serveur de configuration.

## <a name="prerequisites"></a>configuration requise

Nous vous conseillons de déployer le serveur de configuration en tant que machine virtuelle VMware hautement disponible. Pour la réplication du serveur physique, le serveur de configuration peut être configuré sur une machine physique. Les conditions matérielles minimales requises sont indiquées dans le tableau suivant.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>planification de la capacité

Les conditions minimales de taille pour le serveur de configuration varient selon la fréquence potentielle de modification des données. Utilisez ce tableau comme guide.

| **UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- | --- |
| 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) |16 Go |300 Go |500 Go ou moins |Répliquez moins de 100 machines. |
| 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) |18 Go |600 Go |500 Go à 1 To |Répliquez entre 100 et 150 machines. |
| 16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) |32 Go |1 To |1 To à 2 To |Répliquez entre 150 et 200 machines. |


Si vous répliquez des machines virtuelles VMware, consultez la section consacrée aux [considérations sur la planification des capacités](/site-recovery-plan-capacity-vmware.md) et exécutez le [Planificateur de déploiement](site-recovery-deployment-planner.md) pour la réplication VMware.



## <a name="download-the-template"></a>Téléchargez le modèle

Site Recovery fournit un modèle téléchargeable pour configurer le serveur de configuration comme une machine virtuelle VMware hautement disponible. 

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, cliquez sur **+ Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
4. Téléchargez le modèle de Format OVF (Open Virtualization) pour le serveur de configuration.

  > [!TIP]
  La dernière version du modèle de serveur de configuration peut être téléchargée directement à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver)


## <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware


1. Ouvrez une session sur le serveur vCenter ou l’hôte vSphere ESXi de VMware, à l’aide du client vSphere de VMware.
2. Dans le menu **Fichiers**, sélectionnez **Déployer le modèle OVF** pour lancer l’assistant de déploiement du modèle OVF.  

     ![Modèle OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. Dans **Sélectionner une source**, spécifiez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, cliquez sur **Suivant**.
5. Dans **Sélectionner le nom et le dossier**, et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, et pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**.
4. Dans le reste des pages de l’assistant, acceptez les paramètres par défaut.
5. Dans **Prêt à finaliser** :
  - Pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.
  - Si vous souhaitez ajouter une interface réseau supplémentaire, désactivez **Mettre sous tension après le déploiement**, puis sélectionnez **Terminer**. Par défaut, le modèle de serveur de configuration est déployé avec une seule carte réseau, mais vous pouvez en ajouter d’autres après le déploiement.


## <a name="add-an-additional-adapter"></a>Ajouter une carte supplémentaire

Si vous souhaitez ajouter une carte réseau supplémentaire au serveur de configuration, faites-le avant d’inscrire le serveur dans le coffre. L’ajout de cartes supplémentaires n’est pas pris en charge après l’inscription.

1. Dans l’inventaire du client vSphere, faites un clic droit sur la machine virtuelle, puis sélectionnez **Modifier les paramètres**.
2. Dans **Matériel**, cliquez sur **Ajouter** > **Carte Ethernet**. Cliquez ensuite sur **Suivant**.
3. Sélectionnez un type de carte et un réseau. 
4. Pour connecter la carte réseau virtuelle lorsque la machine virtuelle est allumée, sélectionnez **Connecter à la mise sous tension**. Cliquez sur **Suivant** > **Terminer**, puis cliquez sur **OK**.
 

## <a name="register-the-configuration-server"></a>Inscrire le serveur de configuration 

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et spécifiez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.
4. L’outil de Configuration Azure Site Recovery se lance la première fois que vous vous connectez.
5. Spécifiez un nom utilisé pour inscrire le serveur de configuration avec Site Recovery. Cliquez ensuite sur **Suivant**.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, cliquez sur **Connecter** pour vous connecter à votre abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.
7. L’outil effectue des tâches de configuration, puis redémarre.
8. Connectez-vous de nouveau à la machine. L’assistant de gestion de serveur de configuration se lance automatiquement.

### <a name="configure-settings"></a>Configurer les paramètres

1. Dans l’assistant de gestion de serveur de configuration > **Configurer la connectivité**, sélectionnez la carte réseau qui recevra le trafic de réplication. Cliquez ensuite sur **Enregistrer**. Vous ne pouvez pas modifier ce paramètre une fois configuré.
2. Dans **Sélectionner le coffre Recovery Services**, sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre appropriés.
3. Dans **Installer un logiciel tiers**, acceptez le contrat de licence et cliquez sur **Télécharger et installer** pour installer MySQL Server.
4. Cliquez sur **Installer VMware PowerLCI**. Assurez-vous que toutes les fenêtres du navigateur sont fermées avant de continuer. Cliquez ensuite sur **Continuer**
5. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant de continuer.
6. Dans **Configurer le serveur vCenter/vSphere ESXi**, spécifiez le nom de domaine complet ou l’adresse IP du serveur vCenter, ou l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Spécifiez le port écouté par le serveur et un nom convivial à utiliser pour le serveur VMware dans le coffre.
7. Spécifiez les informations d’identification à utiliser par le serveur de configuration pour se connecter au serveur VMware. Site Recovery utilise ces informations d’identification pour détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Cliquez sur **Ajouter**, puis sur **Continuer**.
8. Dans **Configurer les informations d’identification de la machine virtuelle**, spécifiez le nom d’utilisateur et le mot de passe qui serviront à installer automatiquement le service Mobility sur les machines, une fois la réplication activée. Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer. Pour Linux, fournissez les détails du compte racine.
9. Cliquez sur **Finaliser la configuration** pour terminer l’inscription. 
10. Une fois l’inscription terminée, dans le portail Azure, vérifiez que le serveur de configuration et le serveur VMware sont répertoriés sous la page **Source** dans le coffre. Cliquez ensuite sur **OK** pour configurer les paramètres cibles.


## <a name="troubleshoot-deployment-issues"></a>Résoudre les problèmes de déploiement

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>étapes suivantes

Consultez les didacticiels pour en savoir plus sur la configuration de la récupération d’urgence de [machines virtuelles VMware](tutorial-vmware-to-azure.md) et de [serveurs physiques](tutorial-physical-to-azure.md) sur Azure.
