---
title: "Inscription d’application Azure Active Directory | Microsoft Docs"
description: "Cet article décrit comment utiliser le portail Azure pour inscrire une application avec Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 8f5d4ba82fcf3c963373b0e90b707a7d86fc0fea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Inscrire votre application avec un client Azure Active Directory

Vous pouvez utiliser le portail Azure pour inscrire votre application avec votre client Azure Active Directory (Azure AD). Cela crée un ID d’application pour l’application et lui permet de recevoir des jetons.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le volet de navigation de gauche, choisissez **Tous les services**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Ajouter**.
4. Suivez les invites et créez une application. Si vous souhaitez des exemples spécifiques pour les applications web ou natives, consultez les [Démarrages rapides](active-directory-developers-guide.md).
  * Pour les applications web, indiquez l’**URL de connexion**, c’est-à-dire l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter, par exemple `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pour les applications natives, indiquez un **URI de redirection**, qui sera utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application, par exemple, `http://MyFirstAADApp`
5. Une fois l’inscription terminée, Azure AD affecte un identificateur client unique à votre application, l’ID d’application.

## <a name="update-application-settings-from-the-azure-portal"></a>Mettre à jour les paramètres de l’application à partir du portail Azure

Vous pouvez facilement modifier les paramètres d’une application existante à l’aide du portail Azure. Par exemple, vous souhaitez peut-être configurer une URL de réponse, utilisée par Azure AD pour émettre des réponses de jeton. Vous pouvez également configurer des autorisations destinées à d’autres applications, par exemple pour permettre à votre application d’accéder à l’API Microsoft Graph. Vous pouvez effectuer toutes ces opérations via la page Paramètres de l’application.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le volet de navigation de gauche, choisissez **Tous les services**, cliquez sur **Inscriptions des applications**, puis choisissez votre application dans la liste.
4. Cliquez sur **Paramètres** pour ouvrir la page des paramètres de l’application.
  * La page **Propriétés** vous permet de modifier les informations générales de l’application. Cela inclut le nom de l’application, l’URL de connexion et l’URL de déconnexion.
  * La page **URL de réponse** vous permet d’ajouter une URL de réponse, utilisée par Azure AD pour émettre des réponses de jeton.
  * La page **Propriétaires** vous permet d’ajouter des propriétaires d’applications.
  * La page **Autorisations requises** vous permet de configurer des autorisations pour l’application. Par exemple, pour accéder à l’API Microsoft Graph, cliquez sur **Ajouter**, sélectionnez **Microsoft Graph** dans le sélecteur d’API, puis choisissez l’autorisation nécessaire, par exemple **Lire les données du répertoire**.
  * La page **Clés** vous permet d’ajouter des secrets d’application. Le secret s’affichant une seule fois immédiatement après sa création, assurez-vous de le copier pour pouvoir l’utiliser ultérieurement.

## <a name="use-the-inline-manifest-editor"></a>Utiliser l’éditeur de manifeste en ligne

Vous pouvez utiliser l’éditeur de manifeste en ligne pour modifier certaines propriétés d’application qui ne sont pas exposées directement dans le portail Azure. Par exemple, vous pouvez l’utiliser pour modifier l’URI ID d’application ou pour activer le flux implicite OAuth2.0 à la place du flux de code d’octroi d’autorisation par défaut.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le volet de navigation de gauche, choisissez **Tous les services**, cliquez sur **Inscriptions des applications**, puis choisissez votre application dans la liste.
4. Cliquez sur **Manifeste** dans la page de l’application pour ouvrir l’éditeur de manifeste en ligne.
5. Vous pouvez modifier directement le manifeste et l’enregistrer lorsque vous êtes prêt. Vous pouvez également télécharger le manifeste pour l’ouvrir dans votre éditeur favori et télécharger le manifeste mis à jour.

## <a name="next-steps"></a>Étapes suivantes

1. Pour des procédures pas à pas détaillées des applications exécutant l’authentification à l’aide d’Azure AD, consultez les [Démarrages rapides](active-directory-developers-guide.md).
2. Consultez notre liste complète d’exemples de code dans [GitHub](https://github.com/azure-samples).
