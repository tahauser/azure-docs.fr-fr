---
title: "Connecter l’Explorateur Stockage à un abonnement Azure Stack"
description: "Découvrir comment connecter l’Explorateur Stockage à un abonnement Azure Stack"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: xiaofmao
ms.openlocfilehash: bad4b6b0d829fb68dc25e84406a453071a36476a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Connecter l’Explorateur Stockage à un abonnement Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

L’Explorateur Stockage Azure (préversion) est une application autonome qui vous permet d’utiliser facilement les données de stockage Azure Stack sur Windows, macOS et Linux. Il existe plusieurs outils qui permettent de déplacer des données vers et à partir du stockage Azure Stack. Pour plus d’informations, consultez [Outils de transfert de données pour le stockage Azure Stack](azure-stack-storage-transfer.md).

Dans cet article, vous découvrez comment vous connecter à vos comptes de stockage Azure Stack à l’aide de l’Explorateur Stockage. 

Si vous n’avez pas installé l’Explorateur Stockage, [téléchargez](http://www.storageexplorer.com/)-le et installez-le.

Une fois que vous êtes connecté à votre abonnement Azure Stack, vous pouvez utiliser les [articles sur l’Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) pour utiliser vos données Azure Stack. 

## <a name="prepare-an-azure-stack-subscription"></a>Préparer un abonnement Azure Stack

Vous avez besoin d’un accès au bureau de la machine hôte d’Azure Stack ou d’une connexion VPN pour que l’Explorateur Stockage puisse accéder à l’abonnement Azure Stack. Pour savoir comment configurer une connexion VPN à Azure Stack, consultez [Connect to Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Se connecter à Azure Stack).

Pour le Kit de développement Azure Stack, vous devez exporter le certificat racine d’autorité Azure Stack.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Pour exporter et importer le certificat Azure Stack

1. Ouvrez `mmc.exe` sur une machine hôte Azure Stack ou sur une machine locale avec une connexion VPN à Azure Stack. 

2. Dans **Fichier**, sélectionnez **Ajouter/Supprimer un composant logiciel enfichable**, ajoutez **Certificats** pour gérer **Mon compte utilisateur**.



3. Sous **Racine de la console\Certificats (ordinateur local)\Autorités de certification racines de confiance\Certificats**, recherchez **AzureStackSelfSignedRootCert**.

    ![Charger le certificat racine Azure Stack via mmc.exe][25]

4. Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches** > **Exporter**, puis suivez les instructions pour exporter le certificat avec **X.509 encodé en base 64 (.cer)**.  

    Le certificat exporté sera utilisé à l’étape suivante.
5. Démarrez l’Explorateur Stockage (préversion) et, si vous voyez la boîte de dialogue **Connexion au stockage Azure**, cliquez sur Annuler.

6. Dans le menu **Modifier**, pointez sur **Certificats SSL**, puis cliquez sur **Importer les certificats**. Utilisez la boîte de dialogue du sélecteur de fichier pour rechercher et ouvrir le certificat que vous avez exporté à l’étape précédente.

    Vous êtes invité à redémarrer l’Explorateur de stockage suite à l’importation.

    ![Importer le certificat dans l’explorateur de stockage (version préliminaire)][27]

Vous êtes maintenant prêts à connecter l’Explorateur Stockage à un abonnement Azure Stack.

### <a name="to-connect-an-azure-stack-subscription"></a>Connexion à un abonnement Azure Stack


1. Après le redémarrage de l’Explorateur de stockage (version préliminaire), sélectionnez le menu **Modifier** et vérifiez que l’option **Target Azure Stack** (Cibler Azure Stack) est sélectionnée. Si l’option n’est pas sélectionnée, sélectionnez-la et redémarrez l’Explorateur de stockage pour appliquer la modification. Cette configuration est requise pour la compatibilité avec votre environnement Azure Stack.

    ![S’assurer que l’option Target Azure Stack (Cibler Azure Stack) est sélectionnée][28]

7. Dans le volet gauche, sélectionnez **Gérer les comptes**.  
    Tous les comptes Microsoft auxquels vous êtes connecté sont affichés.

8. Pour vous connecter au compte Azure Stack, sélectionnez **Ajouter un compte**.

    ![Ajouter un compte Azure Stack][29]

9. Dans la boîte de dialogue **Connexion au stockage Azure**, sous **Environnement Azure**, sélectionnez **Utiliser l’environnement Azure Stack**, puis cliquez sur **Suivant**.

10. Pour vous connecter au compte Azure Stack associé à au moins un abonnement Azure Stack actif, remplissez la boîte de dialogue **Se connecter à un environnement Azure Stack**.  

    Les détails de chaque champ figurent ci-dessous :

    * **Nom de l’environnement** : le champ peut être personnalisé par l’utilisateur.
    * **ARM resource endpoint** (Point de terminaison de ressource ARM) : les exemples de points de terminaison de ressource ARM :

        * Pour un opérateur cloud :<br> https://adminmanagement.local.azurestack.external   
        * Pour un locataire :<br> https://management.local.azurestack.external
 
    * **ID de locataire** : Facultatif. La valeur est fournie uniquement lorsque le répertoire doit être spécifié.

12. Une fois que vous êtes connecté avec un compte Azure Stack, le volet gauche est renseigné avec les abonnements Azure Stack associés à ce compte. Sélectionnez les abonnements Azure Stack que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure Stack répertoriés.)

    ![Sélectionner les abonnements Azure Stack après avoir renseigné la boîte de dialogue Custom Cloud Environment (Environnement cloud personnalisé)][30]  
    Le volet de gauche affiche les comptes de stockage associés aux abonnements Azure Stack sélectionnés.

    ![Liste des comptes de stockage, y compris les comptes d’abonnement Azure Stack][31]

## <a name="next-steps"></a>Étapes suivantes
* [Bien démarrer avec l’Explorateur Stockage (préversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Stockage Azure Stack : Différences et considérations](azure-stack-acs-differences.md)


* Pour en savoir plus sur le stockage Azure, consultez [Présentation du stockage Microsoft Azure](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
