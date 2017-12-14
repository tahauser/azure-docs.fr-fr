---
title: "Prise en main d’Azure MFA dans le cloud | Microsoft Docs"
description: "Cette page dédiée à Microsoft Azure Multi-Factor Authentication décrit la prise en main d’Azure MFA dans le cloud."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
ms.openlocfilehash: 9d78bfc8c7872e3a248ee2deb19fa1bc40b8ee5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Prise en main avec Azure Multi-Factor Authentication dans le cloud
Cet article vous explique comment utiliser Azure Multi-Factor Authentication dans le cloud.

> [!NOTE]
> La documentation suivante fournit des informations relatives à l’activation des utilisateurs à l’aide du **portail Azure Classic**. Si vous recherchez des informations sur la configuration d’Azure Multi-Factor Authentication pour les utilisateurs O365, consultez l’article [Configurer l’authentification multifacteur pour les utilisateurs d’Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA dans le cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Configuration requise
[Souscrivez un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) : si vous n’avez pas encore d’abonnement Azure, vous devez en souscrire un. Si vous êtes nouveau et que vous utilisez Azure MFA, vous pouvez utiliser un abonnement d’évaluation.

## <a name="enable-azure-multi-factor-authentication"></a>Activation d’Azure Multi-Factor Authentication
Tant que vos utilisateurs disposent de licences comprenant Azure Multi-Factor Authentication, vous n’avez rien à faire pour activer Azure Multi-Factor Authentication (MFA). Vous pouvez commencer par demander une vérification en deux étapes pour les différents utilisateurs. Les licences suivantes prennent en charge Azure MFA :
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Si vous ne disposez pas de l’une de ces trois licences ou que vous n’avez pas suffisamment de licences pour tous vos utilisateurs, vous pourrez tout de même utiliser la fonctionnalité. Pour cela, il vous faudra effectuer une étape supplémentaire et [créer un fournisseur Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) dans votre répertoire.

## <a name="turn-on-two-step-verification-for-users"></a>Activer la vérification en deux étapes pour les utilisateurs

Utilisez l’une des procédures répertoriées dans [How to require two-step verification for a user or group](multi-factor-authentication-get-started-user-states.md) (Exiger la vérification en deux étapes pour un utilisateur ou groupe) pour commencer à utiliser Azure Multi-Factor Authentication. Vous pouvez choisir d’appliquer la vérification en deux étapes pour toutes les connexions, ou de créer des règles d’accès conditionnel, ce qui permet d’exiger la vérification en deux étapes lorsque vous le souhaitez.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré Azure Multi-Factor Authentication dans le cloud, vous pouvez configurer et installer votre déploiement. Pour plus d’informations, consultez [Configuration d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).

