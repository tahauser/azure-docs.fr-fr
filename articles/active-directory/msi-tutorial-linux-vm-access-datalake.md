---
title: "Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store"
description: "Un didacticiel qui vous montre comment utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: fdf1c49c644a97ff2f66a8b217ee54896ed54131
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel montre comment utiliser une MSI pour une machine virtuelle Linux afin d’accéder à Azure Data Lake Store. Azure gère automatiquement les identités créées via la MSI. Vous pouvez utiliser la MSI pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer MSI sur une machine virtuelle Linux 
> * Accorder à votre machine virtuelle l’accès à Azure Data Lake Store
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour accéder à Azure Data Lake Store

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

   ![Panneau des concepts de base liés à la création d’une machine virtuelle](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dans la liste **Abonnement**, sélectionnez un abonnement pour la machine virtuelle.
5. Pour sélectionner un nouveau groupe de ressources dans lequel créer la machine virtuelle, sélectionnez **Groupe de ressources** > **Créer nouveau**. Lorsque vous avez terminé, sélectionnez **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Dans le volet des paramètres, conservez les valeurs par défaut, puis cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

La MSI d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. L’activation de la MSI installe l’extension de machine virtuelle de la MSI sur votre machine virtuelle et cela active la MSI pour Azure Resource Manager.  

1. Sélectionnez la **machine virtuelle** sur laquelle vous souhaitez activer la MSI.
2. Dans le volet gauche, sélectionnez **Configuration**.
3. **Managed service identity** s’affiche. Pour enregistrer et activer la MSI, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez **Non**.
   ![Sélection « Inscription auprès d’Azure Active Directory »](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Sélectionnez **Enregistrer**.
5. Si vous souhaitez vérifier les extensions présentes sur cette Machine virtuelle Linux, cliquez sur **Extensions**. Si la MSI est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

   ![Liste des extensions](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Accorder à votre machine virtuelle l’accès à Azure Data Lake Store

Maintenant, vous pouvez accorder à votre machine virtuelle l’accès à des fichiers et des dossiers dans Azure Data Lake Store. Pour cette étape, vous pouvez utiliser une instance Data Lake Store existante ou bien en créer une. Pour créer une nouvelle instance Data Lake Store via le portail Azure, suivez ce [Démarrage rapide de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Des procédures de démarrage rapide utilisant Azure CLI et Azure PowerShell sont également décrites dans la [Documentation Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

Dans Data Lake Store, créez un dossier et accordez une autorisation MSI pour lire, écrire et exécuter des fichiers dans ce dossier :

1. Dans le volet de gauche du portail Azure, sélectionnez **Data Lake Store**.
2. Sélectionnez l’instance Data Lake Store que vous souhaitez utiliser.
3. Cliquez sur **Explorateur de données** dans la barre de commandes.
4. Le dossier racine de l’instance Data Lake Store est sélectionné. Dans la barre de commandes, sélectionnez **Accès**.
5. Sélectionnez **Ajouter**.  Dans le champ **Sélectionner**, saisissez le nom de votre machine virtuelle, par exemple **DevTestVM**. Sélectionnez votre machine virtuelle à partir des résultats de recherche, puis cliquez sur **Sélectionner**.
6. Cliquez sur **Sélectionner les autorisations**.  Sélectionnez **Lire** et **Exécuter**, ajoutez à **Ce dossier** et ajoutez en tant qu’une **Autorisation d’accès uniquement**. Sélectionnez **OK**.  L’autorisation doit être ajoutée avec succès.
7. Fermez le panneau **Accès**.
8. Pour ce didacticiel, créons un nouveau dossier. Cliquez sur **Nouveau dossier** dans la barre de commandes et donnez-lui un nom, par exemple **TestFolder**.  Sélectionnez **OK**.
9. Sélectionnez le dossier que vous avez créé, puis sélectionnez **accès** sur la barre de commandes.
10. Comme à l’étape 5, sélectionnez **Ajouter**. Dans le champ **Sélectionner**, saisissez le nom de votre machine virtuelle. Sélectionnez votre machine virtuelle à partir des résultats de recherche, puis cliquez sur **Sélectionner**.
11. Comme à l’étape 6, cliquez sur **Sélectionner les autorisations**. Cliquez sur **Lire**, **Écrire** et **Exécuter**, ajoutez **Ce dossier**, et ajoutez en tant qu’**Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut**. Sélectionnez **OK**.  L’autorisation doit être ajoutée avec succès.

La MSI peut désormais effectuer toutes les opérations sur les fichiers du dossier que vous avez créé. Pour plus d’informations sur la gestion de l’accès à Data Lake Store, lisez cet article sur le [Contrôle d’accès dans Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Obtenir un jeton d’accès et appeler le système de fichiers Data Lake Store

Azure Data Lake Store prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide de la MSI. Pour s’authentifier sur le système de fichiers de Data Lake Store, vous envoyez un jeton d’accès émis par Azure AD pour votre point de terminaison de système de fichiers de Data Lake Store. Le jeton d’accès est un en-tête d’autorisation au format « Porteur \<ACCESS_TOKEN_VALUE\> ».  Pour en savoir plus sur la prise en charge de Data Lake Store pour Azure AD Authentication, lire [Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

Dans ce didacticiel, vous vous authentifiez sur l’API REST du système de fichiers de Data Lake Store à l’aide de cURL pour effectuer des requêtes REST.

> [!NOTE]
> Les kits de développement logiciel (SDK) clients du système de fichiers de Data Lake Store ne prennent pas encore en charge la MSI.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH p.ur les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail, accédez à votre machine virtuelle Linux. Dans **Vue d’ensemble**, sélectionnez **Connecter**.  
2. Connectez-vous à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre du terminal, à l’aide de cURL, envoyez une requête au point de terminaison de la MSI locale pour obtenir un jeton d’accès au système de fichiers de Data Lake Store. L’identificateur de ressource pour Data Lake Store est « https://datalake.azure.net/ ».  Il est important d’inclure la barre oblique finale dans l’identificateur de ressource.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Une réponse réussie retourne le jeton d’accès utilisé pour s’authentifier auprès de Data Lake Store :

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. À l’aide de cURL, faites une requête au point de terminaison REST du système de fichiers de votre instance Data Lake Store pour répertorier les dossiers dans le dossier racine. Il s’agit d’un moyen simple de vérifier que tout est correctement configuré. Copiez la valeur du jeton d’accès de l’étape précédente. Il est important que la chaîne « Porteur » de l’en-tête d’autorisation comporte un « P » majuscule. Vous pouvez rechercher le nom de votre instance Data Lake Store dans la section **Vue d’ensemble** du panneau **Data Lake Store** dans le portail Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Une réponse correcte se présente ainsi :

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Vous pouvez maintenant essayer de charger un fichier sur votre instance Data Lake Store. Commencez par créer un fichier à charger.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. À l’aide de cURL, faites une requête au point de terminaison REST du système de fichiers de votre instance Data Lake Store pour charger le fichier dans le dossier créé précédemment. Le chargement implique une redirection, que cURL suit automatiquement. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Une réponse correcte se présente ainsi :

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

À l’aide d’autres API de système de fichiers de Data Lake Store, vous pouvez ajouter à des fichiers, télécharger des fichiers et bien plus encore.

Félicitations ! Vous avez été authentifié auprès du système de fichiers de Data Lake Store à l’aide de la MSI d’une machine virtuelle Linux.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md).
- Data Lake Store utilise Azure Resource Manager pour les opérations de gestion.  Pour plus d’informations sur l’utilisation d’une MSI pour l’authentification auprès de Resource Manager, consultez l’article [Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder au Gestionnaire des ressources](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- En savoir plus sur l’[Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- En savoir plus sur les [Opérations de système de fichiers sur Azure Data Lake Store à l’aide d’une API REST](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) ou les [API de système de fichiers WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- En savoir plus sur le [Contrôle d’accès dans Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.