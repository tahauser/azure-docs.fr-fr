---
title: 'Azure Active Directory B2C : référence : personnaliser l’interface utilisateur d’un parcours utilisateur avec des stratégies personnalisées | Microsoft Docs'
description: Une rubrique sur les stratégies personnalisées Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 40245c25a7f80db27a25a0d34eb20f1057fc5e02
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personnaliser l’interface utilisateur d’un parcours utilisateur avec des stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Cet article est une description détaillée du fonctionnement de la personnalisation de l’interface utilisateur et de l’activation des stratégies personnalisées B2C à l’aide de l’infrastructure d’expérience d’identité


Une expérience utilisateur transparente est essentielle à toute solution B2C. Une expérience utilisateur transparente désigne une expérience, sur un appareil ou un navigateur, au cours de laquelle le parcours utilisateur au sein du service est impossible à distinguer de celui du service client utilisé.

## <a name="understand-the-cors-way-for-ui-customization"></a>Comprendre la méthode CORS appliquée à la personnalisation de l’interface utilisateur

Azure AD B2C vous permet de personnaliser l’apparence de l’expérience utilisateur sur les différentes pages qui sont traitées et affichées par Azure AD B2C par le biais de vos stratégies personnalisées.

Pour ce faire, Azure AD B2C exécute du code dans le navigateur de votre client et utilise l’approche moderne et standard [Partage des ressources cross-origin (CORS)](http://www.w3.org/TR/cors/) pour charger du contenu personnalisé à partir d’une URL spécifique que vous spécifiez dans une stratégie personnalisée pour qu’elle pointe vers vos modèles HTML5/CSS. CORS est un mécanisme qui permet à des ressources limitées (p. ex des polices) sur une page web d’être demandées à partir d’un autre domaine en dehors du domaine d’origine de la ressource.

Par rapport à l’ancienne méthode traditionnelle, où des pages de modèle sont détenues par la solution dans laquelle vous avez fourni des images et du texte limités et dans laquelle un contrôle restreint de la disposition et de l’apparence a été appliqué, ce qui compromettait la transparence de l’expérience, la méthode CORS prend en charge les modèles HTML5 et CSS, ce qui vous permet d’effectuer les opérations suivantes :

- Héberger le contenu et permettre à la solution d’injecter ses contrôles à l’aide d’un script côté client.
- Avoir un contrôle total sur chaque pixel de la disposition et de l’apparence.

Vous pouvez fournir autant de pages de contenu que vous le souhaitez en créant les fichiers HTML5/CSS nécessaires à chaque page.

> [!NOTE]
> Pour des raisons de sécurité, l’utilisation de JavaScript est actuellement bloquée pour la personnalisation. Pour débloquer JavaScript, vous devez utiliser un nom de domaine personnalisé pour votre client Azure AD B2C.

Dans chacun de vos modèles HTML5/CSS, vous fournissez un élément *d’ancrage*, qui correspond à l’élément `<div id=”api”>` requis dans le HTML ou la page de contenu, comme illustré ci-après. Azure AD B2C nécessite que toutes les pages de contenu aient cet élément div spécifique.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Le contenu lié à Azure AD B2C de la page est injecté dans cet élément div, tandis que le reste de la page doit être contrôlé par vos soins. Le code JavaScript d’Azure AD B2C extrait votre contenu et injecte du HTML dans cet élément div spécifique. Azure AD B2C injecte les contrôles ci-après selon les besoins : contrôle de sélecteur de compte, contrôles de connexion, contrôles multifacteur (actuellement basés sur le téléphone) et contrôles de collection d’attributs. Azure AD B2C vérifie que tous les contrôles sont accessibles et conformes à HTML5, que des styles peuvent leur être appliqués et qu’une version de contrôle ne fait pas l’objet d’une régression.

Le contenu fusionné s’affiche finalement sous forme d’un document dynamique pour votre client.

Pour vous assurer que tout fonctionne comme prévu, vous devez effectuer les opérations suivantes :

- Vérifier que votre contenu est accessible et conforme à HTML5
- Vérifier que CORS est activé sur votre serveur de contenu
- Traiter du contenu via HTTPS
- Utiliser des URL absolues comme https://yourdomain/content pour tous les liens et le contenu CSS

> [!TIP]
> Pour vérifier que CORS est activé sur le site sur lequel votre contenu est hébergé et pour tester vos requêtes CORS, vous pouvez utiliser le site http://test-cors.org/. Grâce à ce site, vous pouvez soit envoyer la demande CORS à un serveur distant (pour déterminer si CORS est pris en charge), soit envoyer la demande CORS à un serveur de test (pour découvrir certaines fonctionnalités de CORS).

> [!TIP]
> Le site http://enable-cors.org/ est également une ressource précieuse pour en savoir plus sur CORS.

Cette approche basée sur CORS permet aux utilisateurs finaux de bénéficier d’expériences cohérentes entre votre application et les pages traitées par Azure AD B2C.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Vous devez créer un compte de stockage pour pouvoir continuer avec la procédure suivante. Pour créer un compte Stockage Blob Azure, vous devez disposer d’un abonnement Azure. Vous pouvez vous inscrire gratuitement sur le [site web Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Ouvrez une session de navigation et accédez au [portail Azure](https://portal.azure.com).
2. Connectez-vous avec vos informations d’identification d’administration.
3. Cliquez sur **Créer une ressource** > **Stockage** > **Compte de stockage**.  Un volet **Créer un compte de stockage** s’affiche.
4. Dans **Nom**, fournissez un nom pour le compte de stockage, par exemple *contoso369b2c*. Par la suite, cette valeur est désignée sous le terme de *storageAccountName*.
5. Choisissez les sélections appropriées pour le niveau de tarification, le groupe de ressources et l’abonnement. Assurez-vous que l’option **Épingler au tableau d’accueil** est activée. Cliquez sur **Créer**.
6. Revenez au Tableau d’accueil et cliquez sur le compte de stockage que vous avez créé.
7. Dans la section **Services**, cliquez sur **Objets blob**. Un volet **Service BLOB** s’affiche.
8. Cliquez sur **+ conteneur**.
9. Dans **Nom**, fournissez un nom pour le conteneur, par exemple *b2c*. Par la suite, cette valeur est désignée sous le terme de *containerName*.
9. Sélectionnez **Objet blob** pour **Type d’accès**. Cliquez sur **Créer**.
10. Le conteneur que vous avez créé apparaît dans la liste sur le volet **Service BLOB**.
11. Fermez le volet **Objets blob**.
12. Dans le volet **Compte de stockage**, cliquez sur l’icône **Clé**. Un volet **Clés d’accès** s’affiche.  
13. Notez la valeur de **key1**. Par la suite, cette valeur est désignée sous le terme de *key1*.

## <a name="downloading-the-helper-tool"></a>Téléchargement de l’outil d’assistance

1.  Téléchargez l’outil d’assistance à partir de [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Enregistrer le fichier *B2C-AzureBlobStorage-Client-master.zip* sur votre ordinateur local.
3.  Extrayez le contenu du fichier B2C-AzureBlobStorage-Client-master.zip sur votre disque local, par exemple sous le dossier **UI-Customization-Pack**, ce qui crée un sous-dossier *B2C-AzureBlobStorage-Client-master*.
4.  Ouvrez ce dossier et extrayez le contenu du fichier archive *B2CAzureStorageClient.zip* qu’il contient.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Charger les exemples de fichiers UI-Customization-Pack

1.  À l’aide de l’Explorateur Windows, accédez au dossier *B2C-AzureBlobStorage-Client-master* situé sous le dossier *UI-Customization-Pack* créé dans la section précédente.
2.  Exécutez le fichier *B2CAzureStorageClient.exe*. Ce programme charge tous les fichiers dans le répertoire que vous spécifiez pour votre compte de stockage et active l’accès CORS pour ces fichiers.
3.  Lorsque vous y êtes invité, spécifiez : a.  Le nom de votre compte de stockage *storageAccountName*, par exemple *contoso369b2c*.
    b.  La clé d’accès primaire de votre stockage Blob Azure *key1*, par exemple *contoso369b2c*.
    c.  Le nom de votre conteneur de stockage d’objets blob *containerName*, par exemple *b2c*.
    d.  Le chemin d’accès aux exemples de fichiers *Starter-Pack*, par exemple *..\B2CTemplates\wingtiptoys*.

Si vous avez suivi les étapes précédentes, les fichiers HTML5 et CSS de *UI-Customization-Pack* pour la société fictive **wingtiptoys** pointent désormais vers votre compte de stockage.  Vous pouvez vérifier que le contenu a été chargé correctement en ouvrant le volet du conteneur associé dans le portail Azure. Vous pouvez également vérifier que le contenu a été chargé correctement en accédant à la page à partir d’un navigateur. Pour plus d’informations, consultez [Azure Active Directory B2C : un outil d’assistance utilisé pour illustrer la fonctionnalité de personnalisation de l’interface utilisateur de la page](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Vérifier que CORS est activé sur le compte de stockage

Le mécanisme CORS (Cross-Origin Resource Sharing) doit être activé sur votre point de terminaison pour qu’Azure AD B2C Premium puisse charger votre contenu, car ce dernier est hébergé sur un autre domaine que celui à partir duquel Azure AD B2C Premium traite la page.

Pour vérifier que CORS est activé sur le compte de stockage sur lequel vous hébergez votre contenu, procédez comme suit :

1. Ouvrez une session de navigation et accédez à la page *unified.html* à l’aide de l’URL complète de son emplacement dans votre compte de stockage, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Par exemple : https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Accédez à http://test-cors.org. Ce site vous permet de vérifier que CORS est activé pour la page que vous utilisez.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. Dans **URL distante**, entrez l’URL complète de votre contenu unified.html et cliquez sur **Envoyer une requête**.
4. Vérifiez que la sortie dans la section **Résultats** contient la mention *XHR status: 200* (État XHR : 200), ce qui indique que le mécanisme CORS est activé.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Le compte de stockage doit désormais héberger un conteneur d’objets blob nommé *b2c* dans notre exemple, qui contient les modèles wingtiptoys ci-après issus de *Starter-Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Le tableau ci-après décrit l’objectif des pages HTML5 précédentes.

| Modèle HTML5 | Description |
|----------------|-------------|
| *phonefactor.html* | Cette page peut être utilisée comme modèle pour une page d’authentification multi-facteur. |
| *resetpassword.html* | Cette page peut être utilisée comme modèle pour une page de mot de passe oublié. |
| *selfasserted.html* | Cette page peut être utilisée comme modèle pour une page d’inscription à un compte de réseau social, une page d’inscription à un compte local ou une page de connexion à un compte local. |
| *unified.html* | Cette page peut être utilisée comme modèle pour une page d’inscription ou de connexion unifiée. |
| *updateprofile.html* | Cette page peut être utilisée comme modèle pour une page de mise à jour de profil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Ajouter un lien à vos modèles HTML5/CSS pour votre parcours utilisateur

Vous pouvez ajouter un lien à vos modèles HTML5/CSS pour votre parcours utilisateur en modifiant directement une stratégie personnalisée.

Les modèles HTML5/CSS personnalisés à utiliser dans votre parcours utilisateur doivent être spécifiés dans une liste de définitions de contenu pouvant être utilisées dans ces parcours utilisateur. Pour ce faire, un élément XML *<ContentDefinitions>* facultatif doit être déclaré sous la section *<BuildingBlocks>* de votre fichier XML de stratégie personnalisée.

Le tableau ci-après décrit l’ensemble d’ID de définition de contenu reconnus par le moteur d’expérience d’identité Azure AD B2C et le type des pages qui leur sont associées.

| ID de définition du contenu | Description |
|-----------------------|-------------|
| *api.error* | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. |
| *api.idpselections* | **Page de sélection du fournisseur d’identité**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de la connexion. Il s’agit de fournisseurs d’identité d’entreprise, d’identité de réseaux sociaux tels que Facebook et Google+ ou de comptes locaux (basés sur une adresse e-mail ou un nom d’utilisateur). |
| *api.idpselections.signup* | **Sélection du fournisseur d’identité pour l’inscription**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de l’inscription. Il s’agit de fournisseurs d’identité d’entreprise, d’identité de réseaux sociaux tels que Facebook et Google+ ou de comptes locaux (basés sur une adresse e-mail ou un nom d’utilisateur). |
| *api.localaccountpasswordreset* | **Page de mot de passe oublié**. Cette page contient un formulaire que l’utilisateur doit remplir pour lancer une réinitialisation de mot de passe.  |
| *api.localaccountsignin* | **Page de connexion à un compte local**. Cette page contient un formulaire de connexion que l’utilisateur doit renseigner lors de la connexion à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| *api.localaccountsignup* | **Page d’inscription à un compte local**. Cette page contient un formulaire d’inscription que l’utilisateur doit renseigner lors de l’inscription pour un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles que la zone de saisie de texte, celle du mot de passe, le bouton radio, les cases du menu déroulant à sélection unique et la sélection de plusieurs cases à cocher. |
| *api.phonefactor* | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leur numéro de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| *api.selfasserted* | **Page d’inscription à un compte de réseau social**. Cette page contient un formulaire d’inscription que l’utilisateur doit remplir lors de l’inscription à l’aide d’un compte existant à partir d’un fournisseur d’identité de réseaux sociaux tel que Facebook ou Google+. Cette page est semblable à la page d’inscription à un compte local précédente, à l’exception des champs de saisie de mot de passe. |
| *api.selfasserted.profileupdate* | **Page de mise à jour de profil**. Cette page contient un formulaire dont l’utilisateur peut se servir pour mettre à jour son profil. Cette page est semblable à la page d’inscription à un compte local précédente, à l’exception des champs de saisie de mot de passe. |
| *api.signuporsignin* | **Page de connexion ou d’inscription unifiée**.  Cette page gère l’inscription et la connexion des utilisateurs, qui peuvent utiliser les fournisseurs d’identité d’entreprise, de réseaux sociaux, tels que Facebook ou Google+, ou de comptes locaux.

## <a name="next-steps"></a>Étapes suivantes
[Understanding the custom policies of the Azure AD B2C Custom Policy starter pack](active-directory-b2c-reference-custom-policies-understanding-contents.md) (Comprendre les stratégies personnalisées du pack de démarrage AD B2C Custom Policy)
