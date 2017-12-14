---
title: "Configurer l’intégration de LinkedIn dans Azure AD | Microsoft Docs"
description: "Explique comment activer et désactiver l’intégration LinkedIn aux applications Microsoft dans Azure Active Directory."
services: active-directory
author: jeffgilb
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 15c161542d6826e6aeb5f708a0d9c3fa1f1885e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Activation de l’intégration LinkedIn dans Azure Active Directory
L’activation de l’intégration LinkedIn permet aux utilisateurs d’accéder aux données LinkedIn publiques et, s’ils le souhaitent, d’accéder à leur réseau LinkedIn personnel à partir d’applications Microsoft. Chaque utilisateur peut choisir de connecter son compte professionnel à son compte LinkedIn.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>Intégration LinkedIn du point de vue de vos utilisateurs finaux
Lorsque les utilisateurs de votre entreprise connectent leur compte LinkedIn à leur compte professionnel, ils peuvent plus facilement identifier les personnes avec lesquelles ils travaillent, qu’elles soient internes ou externes à l’entreprise. La connexion des deux comptes permet aux applications Microsoft de l’entreprise d’utiliser les contacts et les données de profil LinkedIn de l’utilisateur. Toutefois, celui-ci peut choisir de ne plus autoriser LinkedIn à partager ses données. L’intégration utilise également les informations de profil publiques. Les utilisateurs peuvent choisir de partager leur profil public et leurs informations de réseau avec les applications Microsoft en configurant les paramètres de confidentialité LinkedIn.

>[!NOTE]
> L’activation de l’intégration LinkedIn dans Azure AD permet aux applications et aux services d’accéder à certaines informations de vos utilisateurs finaux. Pour plus d’informations sur la confidentialité lors de l’activation de l’intégration LinkedIn dans Azure AD, lisez la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement/). 

## <a name="enable-linkedin-integration"></a>Activer l’intégration LinkedIn
Pour les entreprises, l’intégration de LinkedIn est activée par défaut dans Azure AD. Par conséquent, lorsque cette fonctionnalité est mise à la disposition de votre locataire, tous les utilisateurs de l’entreprise peuvent voir les profils et les fonctionnalités LinkedIn. L’activation de l’intégration LinkedIn permet aux utilisateurs de l’entreprise d’utiliser les fonctionnalités LinkedIn dans les services Microsoft, telles que l’affichage du profil lorsque vous recevez un e-mail dans Outlook. Quand vous désactivez cette fonctionnalité, vous empêchez l’accès aux fonctionnalités LinkedIn et vous bloquez les connexions de comptes d’utilisateur et le partage des données entre LinkedIn et votre entreprise via les services Microsoft.

> [!IMPORTANT]
> Cette fonctionnalité n’est pas disponible pour les locataires GoLocal, Sovereign et Government. En outre, les mises à jour de service Azure AD, telles que les fonctionnalités d’intégration LinkedIn, ne sont pas déployées sur tous les locataires Azure en même temps. Vous ne pouvez pas activer l’intégration LinkedIn dans Azure AD tant que cette fonctionnalité n’a pas été déployée sur votre locataire Azure.

1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**.
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Paramètres utilisateur**.
4. Sous **Intégration LinkedIn**, sélectionnez Oui ou Non pour activer ou désactiver l’intégration.
   ![Activation de l’intégration LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>En savoir plus 
* [Informations et fonctionnalités LinkedIn dans les applications Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centre d’aide LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez utiliser le lien suivant pour activer ou désactiver l’intégration de LinkedIn à Azure AD à partir du portail Azure :

[Configurer l’intégration de LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 