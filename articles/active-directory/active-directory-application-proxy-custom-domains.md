---
title: "Domaines personnalisés dans le proxy d’application Azure AD | Microsoft Docs"
description: "Gérer les domaines personnalisés dans le proxy d’application Azure AD afin que l’URL de l’application soit identique quel que soit l’emplacement d’où vos utilisateurs y accèdent."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: b198c6aba4227e33eae4af49c019bc4012086a51
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Utilisation des domaines personnalisés dans le proxy d'application Azure AD

Lorsque vous publiez une application via le proxy d’application Azure Active Directory, vous créez une URL externe accessible à vos utilisateurs lorsqu’ils travaillent à distance. Cette URL obtient le domaine par défaut *yourtenant.msappproxy.net*. Par exemple, si vous avez publié une application nommée Dépenses et si votre locataire s’appelle Contoso, l’URL externe serait https://dépenses-contoso.msappproxy.net. Si vous souhaitez utiliser votre propre nom de domaine, configurez un domaine personnalisé pour votre application. 

Nous vous recommandons de définir des domaines personnalisés pour vos applications, le cas échéant. Voici quelques-uns des avantages des domaines personnalisés :

- Les utilisateurs accèdent à l’application avec la même URL, qu’ils travaillent à l’intérieur ou à l’extérieur de votre réseau.
- Si toutes vos applications ont les mêmes URL internes et externes, les liens qui pointent vers une autre application continuent à fonctionner même en dehors du réseau d’entreprise. 
- Vous contrôlez votre marque et créez les URL que vous souhaitez. 


## <a name="configure-a-custom-domain"></a>Configuration d’un domaine personnalisé

### <a name="prerequisites"></a>Prérequis

Avant de configurer un domaine personnalisé, assurez-vous d’avoir rempli les exigences suivantes : 
- La [vérification du domaine ajouté à Azure Active Directory](active-directory-domains-add-azure-portal.md).
- Un certificat personnalisé pour le domaine, sous la forme de fichier PFX. 
- Une application locale [publiée via le Proxy d’application](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Configurer votre domaine personnalisé

Lorsque vous avez rempli ces trois conditions, procédez comme suit pour configurer votre domaine personnalisé :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications** et sélectionnez l’application que vous souhaitez gérer.
3. Sélectionnez **Proxy d’application**. 
4. Dans le champ URL externe, utilisez la liste déroulante pour sélectionner votre domaine personnalisé. Si vous ne voyez pas votre domaine dans la liste, il n’a pas encore été vérifié. 
5. Sélectionnez **Enregistrer**.
5. Le champ **certificat** qui a été désactivé est maintenant activé. Sélectionnez ce champ. 

   ![Cliquez pour télécharger un certificat.](./media/active-directory-application-proxy-custom-domains/certificate.png)

   Si vous avez déjà téléchargé un certificat pour ce domaine, le champ Certificat affiche les informations de certificat. 

6. Télécharger le certificat PFX et entrez le mot de passe du certificat. 
7. Cliquez sur **Enregistrer** pour enregistrer vos modifications. 
8. Ajoutez un [enregistrement DNS](../dns/dns-operations-recordsets-portal.md) qui redirige la nouvelle URL externe vers le domaine msappproxy.net. 

>[!TIP] 
>Télécharger uniquement un certificat par un domaine personnalisé. Quand vous avez téléchargé un certificat, vous pouvez choisir le domaine personnalisé lorsque vous publiez une nouvelle application, sans effectuer une configuration supplémentaire à l’exception de l’enregistrement DNS. 

## <a name="manage-certificates"></a>Gérer des certificats

### <a name="certificate-format"></a>Format de certificat
Il n’existe aucune restriction sur les méthodes de signature de certificat. Chiffrement à courbe elliptique (ECC), autre nom de l’objet (SAN) et d’autres types de certificat courants sont tous pris en charge. 

Vous pouvez utiliser un certificat avec caractère générique tant que ce dernier correspond à l’URL externe de votre choix. 

### <a name="changing-the-domain"></a>Modification de domaine
Tous les domaines vérifiés s’affichent dans la liste déroulante de l’URL externe pour votre application. Pour modifier le domaine, mettez simplement ce champ à jour pour l’application. Si le domaine n’est pas dans la liste, [ajoutez-le en tant que domaine vérifié](active-directory-domains-add-azure-portal.md). Si vous sélectionnez un domaine dont le certificat n’est pas encore associé, suivez les étapes 5 à 7 pour ajouter le certificat. Ensuite, assurez-vous que vous mettez à jour l’enregistrement DNS pour la redirection à partir de la nouvelle URL externe. 

### <a name="certificate-management"></a>Gestion des certificats
Vous pouvez utiliser le même certificat pour plusieurs applications, sauf si celles-ci partagent un hôte externe. 

Lorsqu’un certificat expire, vous obtenez un avertissement vous demandant de télécharger un autre certificat via le portail. Si le certificat est révoqué, vos utilisateurs pourraient recevoir un avertissement de sécurité lors de l’accès à l’application. Nous n’effectuons pas de vérification pour déterminer si les certificats sont révoqués.  Pour mettre à jour le certificat pour une application donnée, accédez à l’application et suivez les étapes 5 à 7 pour la configuration des domaines personnalisés dans les applications publiées et servant à télécharger un nouveau certificat. Si l’ancien certificat n’est pas utilisé par d’autres applications, il est automatiquement supprimé. 

La gestion des certificats s’effectue actuellement via les pages d’applications individuelles. Vous devez donc gérer les certificats dans le contexte des applications pertinentes. 

## <a name="next-steps"></a>étapes suivantes
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md) pour vos applications publiées avec l’authentification Azure AD.
* [Activer l’accès conditionnel](application-proxy-enable-remote-access-sharepoint.md) à vos applications publiées.
* [Ajout de votre propre nom de domaine à Azure AD](active-directory-domains-add-azure-portal.md)


