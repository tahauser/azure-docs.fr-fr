---
title: RGPD dans le proxy d’application Azure Active Directory | Microsoft Docs
description: En savoir plus sur RGPD dans le proxy d’application Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5e99fba2f226e1e9b401bd8ef5b3b85a5e8c6a7c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>RGPD dans le proxy d’application Azure Active Directory  

Le proxy d’application Azure Active Directory (Azure AD) est conforme à la réglementation RGPD, de même que tous les autres services et fonctionnalités Microsoft. Pour en savoir plus sur la prise en charge de la réglementation RGPD par Microsoft, consultez [Licensing Terms and Documentation (Termes du contrat de licence et documentation)](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Étant donné que cette fonctionnalité inclut des connecteurs sur vos ordinateurs, il existe quelques événements que vous devez surveiller pour garantir la conformité à la réglementation RGPD. Le proxy d’application crée le type de journal suivant, qui peut contenir des informations EUII :

- Journaux d’événements des connecteurs
- Journaux des événements Windows

Il existe deux méthodes pour rester conforme aux règles RGPD :

- Supprimer et exporter les requêtes à mesure qu’elles surviennent

- Désactiver la journalisation

Pour :

- Obtenir des informations sur la configuration de la conservation des données dans les journaux des événements Windows, consultez [Settings for event logs (Paramètres de journaux des événements)](https://technet.microsoft.com/library/cc952132.aspx). 
- Obtenir des informations générales sur le journal des événements Windows, consultez [Using Windows Event Log (Utilisation du journal des événements Windows)](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


Vous trouverez les journaux des événements de connecteur à l’emplacement `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. Les sections suivantes fournissent les étapes associées pour les journaux des événements de connecteur. Vous devez effectuer ces étapes sur tous les ordinateurs connecteurs.
 

## <a name="processing-requests"></a>Traitement des requêtes

Vous êtes responsable de trois différents types de requêtes : 

- Suppression
- Affichage
- Exportation
 
Pour traiter les requêtes d’affichage/exportation, vous devez parcourir chacun des fichiers journaux afin de rechercher les entrées connexes. 

Les journaux étant des fichiers texte, vous pouvez par exemple utiliser la commande `findstr` pour rechercher les entrées relatives à votre utilisateur. Recherchez les champs suivants car ils peuvent être dans les journaux : 

- UserId
- Le type de nom d’utilisateur configuré pour toutes les applications qui utilisent la délégation de contrainte Kerberos :
    - Nom d’utilisateur principal
    - Nom d’utilisateur principal local
    - Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal
    - Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal local
    - Nom de compte SAM local 

 
Vous pouvez ensuite recueillir ces champs et les partager avec l’utilisateur.
Pour traiter les requêtes de suppression, vous devez supprimer les journaux appropriés. Vous pouvez redémarrer le service de connecteur (Connecteur de proxy d’application Microsoft Azure AD) afin de générer un nouveau fichier journal. Le nouveau fichier journal vous permet de supprimer les anciens fichiers journaux. Vous pouvez ensuite appliquer la même procédure que pour l’affichage/exportation afin de rechercher tous les journaux pertinents et de supprimer de manière sélective ces champs ou ces fichiers. De plus, vous pouvez toujours simplement supprimer tous les anciens fichiers journaux si vous n’en avez plus besoin.


## <a name="turn-off-connector-logs"></a>Désactiver les journaux du connecteur

Pour désactiver les journaux du connecteur, vous devez modifiant `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` en supprimant la ligne en surbrillance : 


![Configuration](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du proxy d’application Azure AD, consultez [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

