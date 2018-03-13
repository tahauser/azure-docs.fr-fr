---
title: "Azure AD Connect Sync : extensions d’annuaire | Microsoft Docs"
description: "Cette rubrique décrit la fonctionnalité d’extensions d’annuaire dans Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 4430f445a836f4baa90511c71bb734eda8674249
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Sync : extensions d’annuaire
Vous pouvez utiliser les extensions d’annuaire pour étendre le schéma dans Azure Active Directory (Azure AD) avec vos propres attributs à partir d’un annuaire Active Directory local. Cette fonctionnalité vous permet de générer des applications métiers en consommant les attributs que vous continuez à gérer en local. Ces attributs peuvent être consommés par le biais des [extensions d’annuaire de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou de [Microsoft Graph](https://graph.microsoft.io/). Vous pouvez visualiser les attributs disponibles en utilisant [l’explorateur Azure AD Graph](https://graphexplorer.azurewebsites.net/) et [l’Explorateur Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer) respectivement.

Actuellement, aucune charge de travail Office 365 ne consomme ces attributs.

Vous pouvez configurer les attributs supplémentaires à synchroniser dans le chemin d’accès des paramètres personnalisés dans l’Assistant d’installation.

![Assistant d’extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

L’installation affiche les attributs suivants, qui sont des candidats valides :

* Types d’utilisateur et d’objet de groupe
* Attributs à valeur unique : chaîne, booléen, entier, binaire
* Attributs à valeurs multiples : chaîne, binaire


>[!NOTE]
> Azure AD Connect prend en charge la synchronisation des attributs Active Directory à valeurs multiples avec Azure AD sous la forme d’extensions d’annuaire à valeurs multiples. Toutefois, aucune fonctionnalité d’Azure AD ne prend actuellement en charge l’utilisation d’extensions d’annuaire à valeurs multiples.

La liste des attributs est lue à partir du cache du schéma qui est créé pendant l’installation d’Azure AD Connect. Si vous avez étendu le schéma Active Directory avec des attributs supplémentaires, vous devez [actualiser le schéma](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) pour que ces nouveaux attributs soient visibles.

Un objet dans Azure AD peut comporter jusqu’à 100 attributs pour extensions d’annuaire. La longueur maximale est de 250 caractères. Si une valeur d’attribut est plus longue, le moteur de synchronisation la tronque.

Lors de l’installation d’Azure AD Connect, une application dans laquelle ces attributs sont disponibles est enregistrée. Vous pouvez voir cette application dans le portail Azure.

![Application d’extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Les attributs ont pour préfixe l’extension \_{AppClientId}\_. AppClientId présente la même valeur pour tous les attributs de votre locataire Azure AD.

Ces attributs sont désormais disponibles par le biais de l’API Azure AD Graph. Vous pouvez les interroger en utilisant [l’explorateur Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Explorateur Azure AD Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Vous avez également la possibilité d’interroger les attributs par le biais de l’API Microsoft Graph, en utilisant [l’explorateur Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer#).

>[!NOTE]
> Vous devez demander que les attributs soient renvoyés. Sélectionnez explicitement les attributs comme suit : https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Pour plus d’informations, consultez l’article [Microsoft Graph : Utilisation de paramètres de requête](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
