---
title: "Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD | Microsoft Docs"
description: "Comment résoudre les problèmes courants rencontrés lors de la configuration l’approvisionnement des utilisateurs pour une application déjà répertoriée dans la galerie d’applications Azure AD"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: asmalser
ms.openlocfilehash: 6617345c8923b1fc8081b01ddfe8b4bedf10b6ea
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie Azure Active Directory | Microsoft Docs 

Lorsque vous utilisez le portail Azure pour configurer [l’approvisionnement automatique des utilisateurs](active-directory-saas-app-provisioning.md) pour une application d’entreprise, vous pouvez rencontrer une situation où :

* Les **informations d’identification d’administrateur** saisies pour l’application sont valides et le bouton **Tester la connexion** fonctionne. Toutefois, les informations d’identification ne peuvent pas être enregistrées, et le portail Azure renvoie un message d’erreur générique.

Si l’authentification unique basée sur SAML est également configurée pour la même application, la cause la plus probable de l’erreur est que la limite de stockage par application interne d’Azure AD pour les certificats et les informations d’identification a été dépassée.

Actuellement, Azure AD a une capacité de stockage maximale d’un kilo-octet pour tous les certificats, les jetons secrets, les informations d’identification et les données de configuration connexes associées à une seule instance d’une application (également appelée enregistrement du principal de service dans Azure AD).

Lorsque l’authentification unique basée sur SAML est configurée, le certificat utilisé pour signer les jetons SAML est stocké ici et consomme généralement plus de 50 % de l’espace.

Les jetons secrets, les URI, les adresses e-mail de notification, les noms d’utilisateur et les mots de passe saisis pendant la configuration de l’approvisionnement d’utilisateurs peuvent être à l’origine d’un dépassement de la limite de stockage.

## <a name="how-to-work-around-this-issue"></a>Solution de contournement du problème 

Pour contourner ce problème, vous avez deux possibilités :

1. **Utiliser deux instances d’application, l’un pour l’authentification unique et l’autre pour l’approvisionnement d’utilisateurs** : par exemple, avec l’application de galerie [LinkedIn Elevate](active-directory-saas-linkedinelevate-tutorial.md), vous pouvez ajouter LinkedIn Elevate à partir de la galerie et la configurer pour l’authentification unique. Pour l’approvisionnement, ajoutez une autre instance de LinkedIn Elevate à partir de la galerie d’applications Azure AD et nommez-la « LinkedIn Elevate (approvisionnement) ». Pour cette seconde instance, configurez [l’approvisionnement](active-directory-saas-linkedinelevate-provisioning-tutorial.md), mais pas l’authentification unique. Lorsque vous utilisez cette solution de contournement, les mêmes utilisateurs et groupes doivent être [affectés](active-directory-coreapps-assign-user-azure-portal.md) aux deux applications. 

2. **Réduire la quantité de données de configuration stockées** : toutes les données saisies dans la section [informations d’identification d’administrateur](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) de l’onglet Approvisionnement sont stockées au même endroit que le certificat SAML. S’il n’est pas possible de réduire la longueur de toutes ces données, certains champs de configuration facultatifs tels que **E-mail de notification** peuvent être supprimés.

## <a name="next-steps"></a>Étapes suivantes
[Configurer l’approvisionnement et l’annulation de l’approvisionnement pour les applications SaaS](active-directory-saas-app-provisioning.md)
