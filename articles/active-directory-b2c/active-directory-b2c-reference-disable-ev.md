---
title: "Désactiver la vérification par e-mail lors de l’inscription du consommateur - Azure Active Directory B2C"
description: "Une rubrique qui montre comment désactiver la vérification par e-mail lors de l’inscription du consommateur à Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
ms.custom: seohack1
ms.openlocfilehash: f8e7e5dd8e5e5da6ff4c1e82215da019e8e101c9
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C : désactiver la vérification par e-mail lors de l’inscription du consommateur
Lorsqu'il est activé, Azure Active Directory (Azure AD) B2C permet à un consommateur de s’inscrire à des applications en fournissant une adresse e-mail et en créant un compte local. Azure Active Directory B2C assure la validité des adresses e-mail en demandant aux consommateurs de les vérifier pendant le processus d’inscription. Cela empêche un processus malveillant automatisé de générer des faux comptes pour les applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et demander aux consommateurs de vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les consommateurs qui ont vérifié leur e-mail de ceux qui ne l'ont pas encore fait.

Par défaut, la vérification est activée pour les stratégies d’inscription. Pour la désactiver, procédez comme suit :

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Cliquez sur les **stratégies d’inscription** ou les **stratégies d’inscription ou de connexion** selon la configuration de l'inscription.
3. Ouvrez votre inscription (par exemple, « B2C_1_SiUp ») en cliquant dessus. Cliquez sur **Modifier** dans la partie supérieure du panneau.
4. Cliquez sur la fonctionnalité de **personnalisation de l’interface utilisateur de page**.
5. Cliquez sur **page d’inscription à un compte Local**.
6. Cliquez sur **Adresse e-mail** dans la colonne **Nom** colonne sous la section des **attributs d’abonnement**.
7. Définissez l’option **Exiger la vérification** sur **non**.
8. Cliquez sur **OK** en bas jusqu'à atteindre le panneau **Modifier une stratégie**.
9. Cliquez sur **Enregistrer** dans la partie supérieure du panneau. Vous avez terminé !

> [!NOTE]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par défaut, nous vous recommandons d’ajouter votre propre système de vérification.
> 
> 

Nous sommes ouverts aux commentaires et suggestions ! Si vous avez des difficultés avec cette rubrique, ou si vous avez des conseils pour améliorer ce contenu, faites-nous part de vos commentaires en bas de la page. En cas de demandes liées aux fonctionnalités, utilisez [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
