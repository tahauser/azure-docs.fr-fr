---
title: "Comment utiliser une identité du service administré (MSI) d’une machine virtuelle Windows pour accéder à Azure Data Lake Store"
description: "Un didacticiel qui vous montre comment utiliser une identité du service administré (MSI) d’une machine virtuelle Windows pour accéder à Azure Data Lake Store."
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
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 742b76e17b7ad00a70b0d18895c0b59ebe044d47
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Utiliser une identité du service administré (MSI) d’une machine virtuelle Windows pour accéder à Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce didacticiel montre comment utiliser une MSI pour une machine virtuelle Windows afin d’accéder à Azure Data Lake Store. Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer MSI sur une machine virtuelle Windows 
> * Accorder à votre machine virtuelle l’accès à Azure Data Lake Store
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle, et l’utiliser pour accéder à Azure Data Lake Store

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Création d'une machine virtuelle Windows dans un nouveau groupe de ressources

Pour ce didacticiel, nous allons créer une machine virtuelle Windows.  Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 
3. Saisissez les informations de la machine virtuelle. Le **Nom d’utilisateur** et le **Mot de passe** créés ici sont les informations d’identification nécessaires pour vous connecter à la machine virtuelle.
4. Choisissez un **Abonnement** approprié pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel créer votre machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Conservez les valeurs par défaut dans la page des paramètres et cliquez sur **OK**.

   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle 

Une MSI de machine virtuelle permet d’obtenir des jetons d’accès émanant d’Azure AD sans avoir à insérer d’informations d’identification dans votre code. À l’activation de la MSI, Azure crée une identité administrée pour votre machine virtuelle. En arrière plan, l’activation de la MSI effectue deux opérations : cela installe l’extension de machine virtuelle de la MSI sur votre machine virtuelle et cela active la MSI pour Azure Resource Manager.

1. Sélectionnez la **Machine virtuelle** sur laquelle vous souhaitez activer l’identité du service administré.  
2. Dans la barre de navigation gauche, cliquez sur **Configuration**. 
3. **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non. 
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.  
   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier et confirmer les extensions sur cette machine virtuelle, cliquez sur **Extensions**. Si MSI est activée, l’extension **ManagedIdentityExtensionforWindows** s’affiche dans la liste.

   ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

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

La MSI de votre machine virtuelle peut désormais effectuer toutes les opérations sur les fichiers du dossier que vous avez créé.  Pour plus d’informations sur la gestion de l’accès à Data Lake Store, lisez cet article sur le [Contrôle d’accès dans Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obtenir un jeton d’accès à l’aide de la MSI de machine virtuelle, et l’utiliser pour appeler le système de fichiers de Azure Data Lake Store

Azure Data Lake Store prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide de la MSI.  Pour s’authentifier sur le système de fichiers de Data Lake Store, vous envoyez un jeton d’accès émis par Azure AD pour votre point de terminaison de système de fichiers de Data Lake Store dans un en-tête d’autorisation au format « Porteur < ACCESS_TOKEN_VALUE > ».  Pour en savoir plus sur la prise en charge de Data Lake Store pour l’authentification à l’aide de Azure AD, lire [Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Les kits de développement logiciel clients du système de fichiers de Data Lake Store ne gèrent pas encore l’identité du service administré.  Ce didacticiel sera mis à jour lorsque cette prise en charge sera ajoutée aux kits de développements logiciel.

Dans ce didacticiel, vous vous authentifiez sur l’API REST du système de fichiers de Data Lake Store à l’aide de PowerShell pour effectuer des requêtes REST. Pour utiliser la MSI de machine virtuelle pour l’authentification, vous devez effectuer les requêtes à partir de la machine virtuelle.

1. Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows puis, dans **Vue d’ensemble**, cliquez sur **Connecter**.
2. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la machine virtuelle Windows. 
3. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez **PowerShell** dans la session à distance. 
4. À l’aide de l’applet de commande `Invoke-WebRequest` de Powershell, adressez une requête au point de terminaison de la MSI locale pour obtenir un jeton d’accès pour Azure Data Lake Store.  L’identificateur de ressource pour Data Lake Store est « https://datalake.azure.net/ ».  Data Lake effectue une correspondance exacte sur l’identificateur de ressource et la barre oblique est importante.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Convertissez la réponse d’objet JSON en objet PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrayez le jeton d’accès de la réponse.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. À l’aide « Invoke-WebRequest » de PowerShell, faites une requête au point de terminaison REST de votre Data Lake Store pour répertorier les dossiers dans le dossier racine.  Il s’agit d’un moyen simple de vérifier que tout est correctement configuré.  Il est important de que la chaîne « Porteur » dans l’en-tête d’autorisation ait un « P » majuscule.  Vous pouvez rechercher le nom de votre Data Lake Store dans la section **Vue d’ensemble** du panneau Data Lake Store dans le portail Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Une réponse correcte se présente ainsi :

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Vous pouvez maintenant essayer de charger un fichier sur votre Data Lake Store.  Commencez par créer un fichier à charger.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. À l’aide de `Invoke-WebRequest` de PowerShell, faites une requête au point de terminaison REST de votre Data Lake Store pour charger le fichier dans le dossier créé précédemment.  Cette requête prend deux étapes.  Dans la première étape, vous faites une requête et vous obtenez une redirection vers l’emplacement où le fichier doit être téléchargé.  Dans la deuxième étape, vous chargez réellement le fichier.  N’oubliez pas définir le nom du dossier et du fichier de façon appropriée si vous avez utilisé des valeurs différentes de celles du didacticiel. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Si vous examinez la valeur de `$HdfsRedirectResponse`, elle doit ressembler à la réponse suivante :

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Terminez le chargement en envoyant une requête au point de terminaison de redirection :

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Une réponse correcte se présente ainsi :

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

À l’aide d’autres API de système de fichiers de Data Lake Store, vous pouvez ajouter à des fichiers, télécharger des fichiers et bien plus encore.

Félicitations !  Vous avez été authentifié auprès du système de fichiers de Data Lake Store à l’aide d’une identité du service administré d’une machine virtuelle.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Data Lake Store utilise Azure Resource Manager pour la gestion des opérations.  Pour plus d’informations sur l’utilisation d’une MSI de machine virtuelle pour l’authentification auprès du Gestionnaire des ressources, lisez [Utiliser une identité du service administré (MSI) d’une machine virtuelle Linux pour accéder au Gestionnaire des ressources](msi-tutorial-linux-vm-access-arm.md).
- En savoir plus sur l’[Authentification auprès de Data Lake Store à l’aide de Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- En savoir plus sur les [Opérations de système de fichiers sur Azure Data Lake Store à l’aide d’une API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) ou des [API de système de fichiers WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- En savoir plus sur le [Contrôle d’accès dans Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.