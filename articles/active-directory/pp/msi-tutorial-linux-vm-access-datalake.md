---
title: Comment utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store
description: Un didacticiel qui vous montre comment utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e2a2b885dd0dc5b240aef234ef1ff139d788de3c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce didacticiel montre comment utiliser une MSI pour une machine virtuelle Linux afin d’accéder à Azure Data Lake Store. Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer MSI sur une machine virtuelle Linux 
> * Accorder à votre machine virtuelle l’accès à Azure Data Lake Store
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour accéder à Azure Data Lake Store

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière plan, l’activation de la MSI effectue deux opérations : cela installe l’extension de machine virtuelle de la MSI sur votre machine virtuelle et cela active la MSI pour Azure Resource Manager.  

1. Sélectionnez la **Machine virtuelle** sur laquelle vous souhaitez activer l’identité du service administré.
2. Dans la barre de navigation gauche, cliquez sur **Configuration**.
3. **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier les extensions présentes sur cette **Machine virtuelle Linux**, cliquez sur **Extensions**. Si l’identité MSI est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Accorder à votre machine virtuelle l’accès à Azure Data Lake Store

Maintenant, vous pouvez accorder à votre machine virtuelle l’accès à des fichiers et des dossiers dans un Azure Data Lake Store.  Pour cette étape, vous pouvez utiliser un Data Lake Store existant ou bien en créer un.  Pour créer un nouveau Data Lake Store via le portail Azure, suivez ce [Démarrage rapide de Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Des procédures de démarrage rapide utilisant Azure CLI et Azure PowerShell sont également décrites dans la [Documentation Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

Dans votre Data Lake Store, créez un dossier et accordez à votre machine virtuelle une autorisation MSI pour lire, écrire et exécuter des fichiers dans ce dossier :

1. Dans le portail Azure, cliquez sur **Data Lake Store** dans le volet de navigation gauche.
2. Cliquez sur le Data Lake Store que vous souhaitez utiliser pour ce didacticiel.
3. Cliquez sur **Explorateur de données** dans la barre de commandes.
4. Le dossier racine du Data Lake Store est sélectionné.  Cliquez sur **Accéder** dans la barre de commandes.
5. Cliquez sur **Add**.  Dans le champ **Sélectionner**, saisissez le nom de votre machine virtuelle, par exemple **DevTestVM**.  Sélectionnez votre machine virtuelle à partir des résultats de recherche, puis cliquez sur **Sélectionner**.
6. Cliquez sur **Sélectionner les autorisations**.  Sélectionnez **Lire** et **Exécuter**, ajoutez à **Ce dossier** et ajoutez en tant qu’une **Autorisation d’accès uniquement**.  Cliquez sur **OK**.  L’autorisation doit être ajoutée avec succès.
7. Fermez le panneau **Accès**.
8. Pour ce didacticiel, créons un nouveau dossier.  Cliquez sur **Nouveau dossier** dans la barre de commandes et donnez-lui un nom, par exemple **TestFolder**.  Cliquez sur **OK**.
9. Cliquez sur le dossier créé, puis cliquez sur **Accès** dans la barre de commandes.
10. Semblable à l’étape 5, cliquez sur **Ajouter**, saisissez le nom de votre machine virtuelle dans le champ **Sélectionner**, sélectionnez-la puis cliquez sur **Sélectionner**.
11. Semblable à l’étape 6, cliquez sur **Sélectionner les autorisations**, sélectionnez **Lire**, **Écrire**, et **Exécuter**, ajoutez à **Ce dossier** et ajoutez en tant qu’**une entrée d’autorisation accès et une entrée d’autorisation par défaut**.  Cliquez sur **OK**.  L’autorisation doit être ajoutée avec succès.

La MSI de votre machine virtuelle peut désormais effectuer toutes les opérations sur les fichiers du dossier que vous avez créé.  Pour plus d’informations sur la gestion de l’accès à Data Lake Store, lisez cet article sur le [Contrôle d’accès dans Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obtenir un jeton d’accès à l’aide de la MSI de machine virtuelle, et l’utiliser pour appeler le système de fichiers de Azure Data Lake Store

Azure Data Lake Store prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide de la MSI.  Pour s’authentifier sur le système de fichiers de Data Lake Store, vous envoyez un jeton d’accès émis par Azure AD pour votre point de terminaison de système de fichiers de Data Lake Store dans un en-tête d’autorisation au format « Porteur < \<ACCESS_TOKEN_VALUE\> > ».  Pour en savoir plus sur la prise en charge de Data Lake Store pour l’authentification à l’aide de Azure AD, lire [Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

Dans ce didacticiel, vous vous authentifiez sur l’API REST du système de fichiers de Data Lake Store à l’aide de CURL pour effectuer des requêtes REST.

> [!NOTE]
> Les kits de développement logiciel clients du système de fichiers de Data Lake Store ne gèrent pas encore l’identité du service administré.  Ce didacticiel sera mis à jour lorsque cette prise en charge sera ajoutée aux kits de développements logiciel.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail, accédez à votre machine virtuelle Linux et dans **Vue d’ensemble**, cliquez sur **Connexion**.  
2. **Connectez-vous** à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre du terminal, à l’aide de CURL, envoyez une requête au point de terminaison de l’identité du service administré locale pour obtenir un jeton d’accès au système de fichiers de Data Lake Store.  L’identificateur de ressources pour Data Lake Store est « https://datalake.azure.net/ ».  Il est important d’inclure la barre oblique dans l’identificateur de ressource.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Une réponse réussie retourne le jeton d’accès utilisé pour s’authentifier auprès de Data Lake Store :

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. À l’aide de CURL, faites une requête au point de terminaison REST du système de fichiers de votre Data Lake Store pour répertorier les dossiers dans le dossier racine.  Il s’agit d’un moyen simple de vérifier que tout est correctement configuré.  Copiez la valeur du jeton d’accès de l’étape précédente.  Il est important de que la chaîne « Porteur » dans l’en-tête d’autorisation ait un « P » majuscule.  Vous pouvez rechercher le nom de votre Data Lake Store dans la section **Vue d’ensemble** du panneau Data Lake Store dans le portail Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Une réponse correcte se présente ainsi :

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Vous pouvez maintenant essayer de charger un fichier sur votre Data Lake Store.  Commencez par créer un fichier à charger.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. À l’aide de CURL, faites une requête au point de terminaison REST du système de fichiers de votre Data Lake Store pour charger le fichier dans le dossier créé précédemment.  Le chargement implique une redirection, et CURL la suit automatiquement. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Une réponse correcte se présente ainsi :

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

Félicitations !  Vous avez été authentifié auprès du système de fichiers de Data Lake Store à l’aide d’une identité du service administré d’une machine virtuelle.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Data Lake Store utilise Azure Resource Manager pour la gestion des opérations.  Pour plus d’informations sur l’utilisation d’une MSI de machine virtuelle pour l’authentification auprès du Gestionnaire des ressources, lisez [Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder au Gestionnaire des ressources](msi-tutorial-linux-vm-access-arm.md).
- En savoir plus sur l’[Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- En savoir plus sur les [Opérations de système de fichiers sur Azure Data Lake Store à l’aide d’une API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) ou des [API de système de fichiers WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- En savoir plus sur le [Contrôle d’accès dans Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.