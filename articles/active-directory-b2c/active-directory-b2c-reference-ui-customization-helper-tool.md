---
title: 'Azure Active Directory B2C : outil d’assistance de personnalisation d’interface de page | Microsoft Docs'
description: Un outil d’assistance est utilisé pour illustrer la fonctionnalité de personnalisation de la Page Interface utilisateur dans Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: f898c626d52b1a4e7df72284190749f4481999ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C : un outil d’assistance utilisé pour illustrer la fonctionnalité de personnalisation de l’interface utilisateur de la page
Cet article complète le [principal article sur la personnalisation de l’interface utilisateur](active-directory-b2c-reference-ui-customization.md) dans Azure Active Directory (Azure AD) B2C. Les étapes suivantes décrivent l’utilisation de la fonctionnalité de personnalisation de l’interface utilisateur de la page à l’aide de l’exemple de contenu HTML et CSS que nous vous avons fourni.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtention d’un client Azure AD B2C
Avant de pouvoir personnaliser quoi que ce soit, vous devrez [obtenir un client Azure AD B2C](active-directory-b2c-get-started.md) , si ce n'est déjà fait.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Création d’une stratégie d’inscription ou de connexion
L’exemple de contenu fourni peut servir à personnaliser deux pages dans une [stratégie d’inscription ou de connexion](active-directory-b2c-reference-policies.md) : la [page de connexion unifiée](active-directory-b2c-reference-ui-customization.md) et la [page des attributs auto-déclarés](active-directory-b2c-reference-ui-customization.md). Lors de la [création de votre stratégie d’inscription ou de connexion](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), ajoutez le compte local (adresse de messagerie), Facebook, Google et Microsoft en tant que **fournisseurs d’identité**. Il s’agit des seuls fournisseurs d’identité acceptés par notre exemple de contenu HTML.  Vous pouvez également, si vous le souhaitez, ajouter un sous-ensemble de ces fournisseurs d’identité acceptés.

## <a name="register-an-application"></a>Inscription d'une application
Vous devrez [inscrire une application](active-directory-b2c-app-registration.md) dans votre client B2C qui permettra d'exécuter votre stratégie. Après avoir inscrit votre application, vous disposez de quelques options pour exécuter votre stratégie d’inscription :

* Générez l’une des applications de démarrage rapide Azure AD B2C répertoriées dans la section « Prise en main » dans [Inscription et connexion de consommateurs à vos applications](active-directory-b2c-overview.md#get-started).
* Utilisez l’application [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) préconfigurée. Si vous choisissez d’utiliser l’application Playground, vous devez inscrire une application dans votre client B2C en utilisant **l’URI de redirection** `https://aadb2cplayground.azurewebsites.net/`.
* Utilisez le bouton **Exécuter maintenant** sur votre stratégie dans le [portail Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personnalisation de votre stratégie
Pour personnaliser l’apparence de votre stratégie, vous devez d’abord créer les fichiers HTML et CSS en suivant les conventions spécifiques d’Azure AD B2C. Vous pouvez ensuite charger votre contenu statique vers un emplacement accessible au public afin qu’Azure AD B2C puisse y accéder. Il peut s’agir de votre propre serveur web dédié, d’Azure Blob Storage, du réseau de distribution de contenu (CDN) Azure ou de n’importe quel autre fournisseur d’hébergement de ressources statiques. Les seules conditions requises exigent que votre contenu soit disponible via le protocole HTTPS et accessible à l’aide de CORS. Après avoir publié votre contenu statique sur le web, vous pouvez modifier votre stratégie pour la pointer vers cet emplacement et présenter ce contenu à vos clients. Le [principal article sur la personnalisation de l'interface utilisateur](active-directory-b2c-reference-ui-customization.md) décrit en détail la fonctionnalité de personnalisation Azure AD B2C.

Dans le cadre de ce didacticiel, nous avons déjà créé quelques exemples de contenu et les avons hébergés sur Azure Blob Storage. L’exemple de contenu est une personnalisation très basique dans le thème de notre entreprise fictive, « Wingtip Toys ». Pour le tester dans votre propre stratégie, procédez comme suit :

1. Connectez-vous à votre client dans le [portail Azure](https://portal.azure.com/) et accédez au panneau de fonctionnalités B2C.
2. Cliquez sur **Stratégies d’inscription ou de connexion**, puis sur votre stratégie (par exemple, « b2c\_1\_sign\_up\_sign\_in »).
3. Cliquez sur **Personnalisation de l’interface utilisateur de la page**, puis sur **Page d’inscription ou de connexion unifiée**.
4. Placez le commutateur **Utiliser la page personnalisée** sur **Oui**. Dans le champ **URI de la page personnalisée**, entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Cliquez sur **OK**.
5. Cliquez sur **page d’inscription à un compte Local**. Placez le commutateur **Utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée**, entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Suivez la même procédure pour la **page d’inscription à un compte social**.
   Cliquez deux fois sur **OK** pour fermer les panneaux de personnalisation de l'interface utilisateur.
7. Cliquez sur **Enregistrer**.

Vous pouvez maintenant tester votre stratégie personnalisée. Vous pouvez utiliser votre propre application ou l’application Azure AD B2C Playground si vous le souhaitez, mais vous avez aussi la possibilité de cliquer sur la commande **Exécuter maintenant** dans le panneau de la stratégie. Sélectionnez votre application dans la zone de liste déroulante ainsi que l’URI de redirection approprié. Cliquez sur le bouton **Exécuter maintenant** . Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience utilisateur consistant à inscrire votre application avec le nouveau contenu en place !

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Chargement de l'exemple de contenu sur Azure Blob Storage
Si vous souhaitez utiliser Azure Blob Storage pour héberger le contenu de votre page, vous pouvez créer votre propre compte de stockage et utiliser notre outil d'assistance B2C pour charger vos fichiers.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.
1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** > **Données et stockage** > **Compte de stockage**. Vous avez besoin d’un abonnement Azure pour créer un compte Azure Blob Storage. Vous pouvez vous inscrire gratuitement sur le [site web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Saisissez un **nom** pour le compte de stockage (par exemple, « contoso ») et sélectionnez les options appropriées pour **Niveau tarifaire**, **Groupe de ressources** et **Abonnement**. Assurez-vous que l’option **Épingler au tableau d’accueil** est activée. Cliquez sur **Créer**.
4. Revenez au tableau d’accueil et cliquez sur le compte de stockage que vous venez de créer.
5. Dans la section **Résumé**, cliquez sur **Conteneurs**, puis sur **+ Ajouter**.
6. Fournissez un **nom** pour le conteneur (par exemple, « b2c ») et sélectionnez **Blob** comme **type d’accès**. Cliquez sur **OK**.
7. Le conteneur que vous avez créé apparaît dans la liste sur le panneau **Objets Blob** . Notez l’URL du conteneur, qui doit ressembler par exemple à `https://contoso.blob.core.windows.net/b2c`. Fermez le panneau **Objets Blob** .
8. Dans le panneau de compte de stockage, cliquez sur **Clés** et notez les valeurs des champs **Nom de compte de stockage** et **Clé d’accès primaire**.

> [!NOTE]
> **Clé d’accès primaire** est une information de sécurité importante.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Téléchargement de l’outil d’assistance et des fichiers exemples
Vous pouvez télécharger [l’outil d’assistance Azure Blob Storage et les exemples de fichiers sous forme d’un fichier .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou effectuer un clonage à partir de GitHub :

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ce référentiel contient un répertoire `sample_templates\wingtip` qui regroupe un exemple de contenu HTML et CSS, et des images. Pour que ces modèles puissent faire référence à votre propre compte Azure Blob Storage, vous devez modifier les fichiers HTML. Ouvrez `unified.html` et `selfasserted.html`, puis remplacez toutes les instances de `https://localhost` par l’URL de votre propre conteneur que vous avez notée dans les étapes précédentes. Vous devez utiliser le chemin absolu des fichiers HTML car, dans ce cas, le code HTML sera fourni par Azure AD, sous le domaine `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Chargement des fichiers exemples
Dans le même référentiel, décompressez `B2CAzureStorageClient.zip` et exécutez le fichier `B2CAzureStorageClient.exe` qu’il contient. Ce programme charge simplement tous les fichiers dans le répertoire que vous spécifiez pour votre compte de stockage et active l’accès CORS pour ces fichiers. Si vous avez suivi les étapes ci-dessus, les fichiers HTML et CSS pointent désormais vers votre compte de stockage. Notez que le nom de votre compte de stockage est la partie qui précède `blob.core.windows.net`, par exemple `contoso`. Vous pouvez vérifier que le contenu a été téléchargé correctement en essayant d'accéder à `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` sur un navigateur. Utilisez également [http://test-cors.org/](http://test-cors.org/) pour vérifier que le contenu est maintenant compatible CORS. (Recherchez « XHR status: 200 » dans le résultat.)

### <a name="customize-your-policy-again"></a>Nouvelle personnalisation de votre stratégie
Maintenant que vous avez chargé l'exemple de contenu sur votre propre compte de stockage, vous devez modifier votre stratégie d'inscription pour le référencer. Répétez les étapes à partir de la section [« Personnalisation de votre stratégie »](#customize-your-policy) ci-dessus, en utilisant cette fois l'URL de votre propre compte de stockage. Par exemple, l'emplacement de votre fichier `unified.html` serait `<url-of-your-container>/wingtip/unified.html`.

Vous pouvez à présent utiliser le bouton **Exécuter maintenant** ou votre propre application pour réexécuter votre stratégie. Le résultat devrait être quasiment identique puisque vous avez utilisé le même exemple de contenu HTML et CSS dans les deux cas. Cependant, vos stratégies référencent maintenant votre propre instance d’Azure Blob Storage, et vous êtes libre de modifier et de recharger les fichiers. Pour plus d’informations sur la personnalisation du contenu HTML et CSS, reportez-vous à [l’article sur la personnalisation de l’interface utilisateur principale](active-directory-b2c-reference-ui-customization.md).

