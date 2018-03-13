---
title: "Comment configurer l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD | Microsoft Docs"
description: "Comment configurer rapidement un approvisionnement et une annulation d’approvisionnement complets des comptes d’utilisateur pour des applications déjà répertoriées dans la galerie d’applications Azure AD"
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
ms.openlocfilehash: da7050edae282c90c4f92c588ce8fe501ccba50d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Comment configurer l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD

*L’approvisionnement de comptes d’utilisateur* consiste à créer, mettre à jour et/ou désactiver des enregistrements de compte d’utilisateur dans le magasin local de profils utilisateur d’une application. La plupart des applications cloud et SaaS stockent le rôle et les autorisations des utilisateurs dans leur propre magasin local de profils utilisateur. La présence de ces enregistrements d’utilisateur dans leur magasin local est *requise* pour l’authentification unique et l’accès aux opérations.

Dans le Portail Azure, l’onglet **Approvisionnement** du volet de navigation de gauche affiche les modes d’approvisionnement pris en charge pour l’application d’entreprise concernée. Deux valeurs sont possibles :

## <a name="configuring-an-application-for-manual-provisioning"></a>Configuration d’une application pour l’approvisionnement manuel

*Manuel* : dans le cadre d’un approvisionnement manuel, les comptes d’utilisateur doivent être créés manuellement à l’aide des méthodes fournies par cette application. Cela peut impliquer une connexion à un portail d’administration pour cette application et l’ajout d’utilisateurs à l’aide d’une interface utilisateur web. Il peut également s’agir d’une feuille de calcul comportant les détails des comptes d’utilisateur, qui doit être chargée à l’aide d’un mécanisme fourni par l’application. Consultez la documentation fournie par l’application ou contactez le développeur de l’application pour déterminer les mécanismes disponibles.

Si le mode manuel est le seul proposé par une application donnée, cela signifie qu’aucun connecteur d’approvisionnement Azure AD automatique n’a encore été créé pour l’application. Cela peut également signifier que l’application ne prend pas en charge l’API de gestion des utilisateurs requise sur laquelle doit s’appuyer la création d’un connecteur d’approvisionnement automatisé.

Si vous avez besoin d’assistance en matière d’approvisionnement automatique pour une application donnée, vous pouvez remplir une demande sur la page [Azure Active Directory Application Requests (Demandes d’applications Azure Active Directory)](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configuration d’une application pour l’approvisionnement automatique

*Automatique* : dans le cadre d’un approvisionnement automatique, un connecteur d’approvisionnement Azure AD a été développé pour cette application. Pour plus d’informations sur le service d’approvisionnement Azure AD et sur son fonctionnement, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Pour plus d’informations sur l’approvisionnement d’utilisateurs et de groupes spécifiques sur une application, consultez [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Les étapes nécessaires pour activer et configurer l’approvisionnement automatique varient selon l’application.

>[!NOTE]
>Vous devez toujours commencer par rechercher le didacticiel spécifique à la configuration de l’approvisionnement pour votre application. Suivez ensuite ces étapes afin de configurer l’application et Azure AD pour créer la connexion d’approvisionnement. 
>
>

Vous trouverez les didacticiels spécifiques aux applications à la page [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Lors de la configuration de l’approvisionnement, il est important de vérifier et configurer les mappages d’attributs et les workflows qui définissent les propriétés de l’utilisateur (ou du groupe) passant d’Azure AD à l’application. Cela inclut la définition d’une « propriété correspondante » réservée à l’utilisation et à la mise en correspondance des utilisateurs/groupes entre les deux systèmes. Pour plus d’informations sur ce processus important.

## <a name="next-steps"></a>Étapes suivantes
[Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

