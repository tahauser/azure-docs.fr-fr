---
title: "Gestion des noms de domaine personnalisés dans Azure Active Directory | Microsoft Docs"
description: "Concepts de gestion et procédures pour gérer un nom de domaine dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 64c1be4358305a736ac1dd8a1b7194c80100d256
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gestion des noms de domaine personnalisés dans Azure Active Directory
Un nom de domaine est une partie importante de l’identificateur de nombreuses ressources de répertoire : il fait partie du nom ou de l’adresse électronique d’un utilisateur, de l’adresse d’un groupe et parfois de l’URI ID d’application pour une application. Une ressource dans Azure Active Directory (Azure AD) peut inclure un nom de domaine déjà vérifié comme appartenant à l’annuaire qui contient la ressource. Seul un administrateur général peut effectuer des tâches de gestion de domaine dans Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Définir le nom de domaine principal pour votre répertoire Azure AD
Lors de la création du répertoire, le nom de domaine initial, par exemple « contoso.onmicrosoft.com », est également le nom de domaine principal. Le domaine principal est le nom de domaine par défaut pour un nouvel utilisateur lorsque vous créez un nouvel utilisateur. Le fait de définir un nom de domaine principal simplifie le processus permettant à un administrateur de créer des utilisateurs dans le portail. Pour modifier le nom de domaine principal, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Azure Active Directory**.
3. Sélectionnez **Noms de domaine personnalisés**.
     
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. Sélectionnez le nom du domaine que vous souhaitez choisir comme domaine principal.
5. Sélectionnez la commande **Définir comme principal**. Confirmez votre choix lorsque vous y êtes invité.
   
   ![Créer un nom de domaine principal](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

Vous pouvez modifier le nom de domaine principal de votre répertoire en n’importe quel domaine personnalisé vérifié qui n’est pas fédéré. La modification du domaine principal de votre répertoire ne changera pas les noms des utilisateurs existants.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Ajouter des noms de domaine personnalisés à votre locataire Azure AD
Vous pouvez ajouter jusqu’à 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec l’annuaire Active Directory local, vous pouvez ajouter jusqu’à 450 noms de domaine dans chaque répertoire. Pour plus d’informations, consultez la section [Noms de domaines fédérés et gérés](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Ajouter des sous-domaines d’un domaine personnalisé
Si vous souhaitez ajouter un nom de domaine de troisième niveau, tel que « europe.contoso.com » à votre répertoire, vous devez tout d’abord ajouter et vérifier le domaine de second niveau, tel que contoso.com. Le sous-domaine est automatiquement vérifié par Azure AD. Pour voir que le sous-domaine que vous venez d’ajouter a été vérifié, actualisez la page dans le navigateur qui répertorie les domaines.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Que faire en cas de modification du bureau d’enregistrement DNS pour votre nom de domaine personnalisé ?
Si vous modifiez le bureau d’enregistrement DNS de votre nom de domaine personnalisé, vous pouvez continuer à utiliser votre nom de domaine personnalisé sans interruption et sans tâches de configuration supplémentaires dans Azure AD. Si vous utilisez votre nom de domaine personnalisé avec Office 365, Intune ou autres services s’appuyant sur des noms de domaine personnalisés dans Azure AD, reportez-vous à la documentation de ces services.

## <a name="delete-a-custom-domain-name"></a>Supprimer un nom de domaine personnalisé
Vous pouvez supprimer un nom de domaine personnalisé de votre répertoire Azure AD si votre organisation n’utilise plus ce nom de domaine ou si vous souhaitez l’utiliser avec un autre répertoire Azure AD.

Pour supprimer un nom de domaine personnalisé, vous devez d’abord vous assurer qu’aucune des ressources de votre répertoire ne s’appuie sur le nom de domaine. Vous ne pouvez pas supprimer un nom de domaine de votre répertoire si :

* L’utilisateur dispose d’un nom d’utilisateur, d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Le groupe dispose d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Une application dans Azure AD dispose d’une URI ID d’application qui inclut le nom de domaine.

Vous devez modifier ou supprimer n’importe quelle ressource de ce type dans votre répertoire Azure AD pour pouvoir supprimer le nom de domaine personnalisé.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utiliser PowerShell ou API Graph pour gérer les noms de domaine
La plupart des tâches de gestion des noms de domaine dans Azure Active Directory peuvent également être accomplies à l’aide de Microsoft PowerShell, ou encore par programmation à l’aide d’API Graph d’Azure AD.

* [Utilisation de PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Utilisation d’API Graph pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter des noms de domaines personnalisés](add-custom-domain.md)

