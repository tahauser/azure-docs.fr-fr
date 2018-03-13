---
title: "Problème de configuration de l’authentification unique fédérée pour une application non issue de la galerie | Microsoft Docs"
description: "Découvrez comment résoudre certains problèmes courants que vous pouvez rencontrer lors de la configuration de l’authentification unique fédérée sur votre application SAML personnalisée non répertoriée dans la galerie d’applications Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 77f4bf0fc38043afd7a6634bcf16a0f10f7ddfeb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problème de configuration de l’authentification unique fédérée pour une application non issue de la galerie

Si vous rencontrez un problème lors de la configuration d’une application, vérifiez que vous avez suivi toutes les étapes de l’article [Configuration de l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery).

## <a name="cant-add-another-instance-of-the-application"></a>Impossible d’ajouter une autre instance de l’application

Pour ajouter une deuxième instance d’une application, vous devez être en mesure de :

-   Configurer un identificateur unique pour la deuxième instance. Vous ne pouvez pas configurer le même identificateur que celui utilisé pour la première instance.

-   Configurer un certificat différent de celui utilisé pour la première instance.

Si l’application ne prend en charge aucune de ces configurations, vous ne pouvez pas configurer de seconde instance.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Où définir le format d’EntityID (identificateur d’utilisateur)

Vous ne pouvez pas sélectionner le format EntityID (identificateur d’utilisateur) qu’Azure AD envoie à l’application dans la réponse après l’authentification de l’utilisateur.

Azure AD sélectionne le format de l’attribut NameID (identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Comment obtenir le certificat ou les métadonnées de l’application à partir d’Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

Azure AD ne fournit pas d’URL permettant d’obtenir les métadonnées. Les métadonnées sont uniquement récupérables sous la forme d’un fichier XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vous ne savez pas comment personnaliser les revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez [Claims mapping in Azure Active Directory (public preview)](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mappage de revendications dans Azure Active Directory [préversion]) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
