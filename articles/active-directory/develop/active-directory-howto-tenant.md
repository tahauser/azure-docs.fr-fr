---
title: "Obtention d’un client Azure AD | Microsoft Docs"
description: "Obtention d’un client Azure Active Directory pour l'inscription et la génération d'applications."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b45e9479fa2e06f2c15ace9138e48ced94bacbda
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Obtention d’un client Azure Active Directory
Dans Azure Active Directory (Azure AD), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) est représentatif d'une organisation.  Il s’agit d’une instance dédiée du service Azure AD qu’une organisation reçoit et possède lorsqu’elle s'inscrit à un service cloud de Microsoft tel qu’Azure, Microsoft Intune ou Office 365.  Chaque client Azure AD est distinct et indépendant des autres clients Azure AD.  

Un client héberge les utilisateurs d'une entreprise et leurs informations, c’est-à-dire les mots de passe, les données de profil utilisateur, les autorisations, etc.  Il contient également des groupes, des applications et d’autres informations relatives à une organisation et à sa sécurité.

Pour permettre aux utilisateurs d'Azure AD de se connecter à votre application, vous devez enregistrer votre application dans un de vos clients.  La publication d'une application dans un client Azure AD est **absolument gratuite**.  En fait, la plupart des développeurs créent plusieurs clients et applications pour expérimenter, développer, organiser et tester.  Les organisations qui s’inscrivent et utilisent votre application peuvent éventuellement choisir d'acheter des licences s'ils souhaitent bénéficier des fonctionnalités de répertoire avancées.

Mais comment faire pour obtenir un client Azure AD ?  Le processus peut être un peu différent si :

* [Vous avez un abonnement Office 365 en cours](#use-an-existing-office-365-subscription)
* [Vous avez un abonnement Azure en cours associé à un compte Microsoft](#use-an-msa-azure-subscription)
* [Vous avez un abonnement Azure en cours associé à un compte professionnel](#use-an-organizational-azure-subscription)
* [Vous n’avez aucun des éléments ci-dessus et vous souhaitez commencer à partir de zéro](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utilisation d’un abonnement Office 365 en cours
Si vous avez un abonnement à Office 365 existant, vous disposez déjà d’un client Azure AD ! Vous pouvez vous connecter au [portail Azure](https://portal.azure.com) avec votre compte O365 et démarrer à l’aide d’Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Utilisation d’un abonnement Microsoft Azure
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte individuel Microsoft, vous avez déjà un client !  Lorsque vous vous connectez au [portail Azure](https://portal.azure.com), vous serez automatiquement connecté à votre client par défaut. Vous êtes libre d'utiliser ce client selon vos besoins, mais vous avez la possibilité de créer un compte d'administrateur professionnel.

Pour cela, procédez comme suit :  Autrement, vous pouvez créer un nouveau client et créer un administrateur dans ce client suivant un processus similaire.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte individuel
2. Accédez à la section « Azure Active Directory » du portail (située dans la barre de navigation de gauche sous **Plus de services**)
3. Vous devez être automatiquement connecté au « répertoire par défaut ». Si ce n’est pas le cas, vous pouvez changer de répertoire en cliquant sur le nom de votre compte dans le coin supérieur droit.
4. À partir de la section **Tâches rapides**, choisissez **Ajouter un utilisateur**.
5. Dans le formulaire Ajouter un utilisateur, fournissez les informations suivantes :

   * Nom : (choisissez une valeur appropriée)
   * Nom d'utilisateur : (choisissez un nom d'utilisateur pour cet administrateur)
   * Profil : (renseigner les valeurs appropriées pour le prénom, le nom, la fonction et le service)
   * Rôle : administrateur général
6. Lorsque vous avez rempli le formulaire Ajouter un utilisateur et que vous recevez le mot de passe temporaire pour le nouvel utilisateur d'administration, veillez à enregistrer ce mot de passe que vous utiliserez pour vous connecter avec ce nouvel utilisateur afin de modifier le mot de passe. Vous pouvez également envoyer le mot de passe directement à l'utilisateur, à l'aide d'un autre e-mail.
7. Cliquez sur **Créer** pour créer le nouvel utilisateur.
8. Pour modifier le mot de passe temporaire, connectez-vous à [https://login.microsoftonline.com](https://login.microsoftonline.com) avec ce nouveau compte d’utilisateur et modifiez le mot de passe lorsque vous y êtes invité.

## <a name="use-an-organizational-azure-subscription"></a>Utilisation d’un abonnement Azure professionnel
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte professionnel, vous avez déjà un client !  Dans le [portail Azure](https://portal.azure.com), vous devez trouver un client lorsque vous accédez à « Plus de services » et « Azure Active Directory ».  Vous êtes libre d'utiliser ce client selon vos besoins.

## <a name="start-from-scratch"></a>Commencer à partir de zéro
Si tous les éléments ci-dessus vous sont incompréhensibles, ne vous inquiétez pas.  Visitez simplement [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) pour vous inscrire à Azure avec une nouvelle organisation.  Une fois le processus terminé, vous aurez votre propre client Azure AD avec le nom de domaine que vous avez choisi lors de votre inscription.  Dans le [portail Azure](https://portal.azure.com), vous pouvez trouver votre client en accédant à « Azure Active Directory » dans la barre de navigation de gauche.

Dans le cadre du processus d'inscription à Azure, vous devrez fournir les informations d’une carte de crédit.  Vous pouvez procéder en toute confiance. Vous ne serez pas facturé pour la publication d'applications dans Azure AD ou la création de nouveaux clients.
