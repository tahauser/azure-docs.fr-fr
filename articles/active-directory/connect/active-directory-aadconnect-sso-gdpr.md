---
title: 'Azure AD Connect : Authentification unique fluide - Conformité RGPD | Microsoft Docs'
description: Cet article traite de l’authentification unique fluide Azure Active Directory (Azure AD) et de la conformité RGPD.
services: active-directory
keywords: Qu’est-ce qu’Azure AD Connect, RGPD, Composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Authentification unique fluide Azure AD - Conformité RGPD

## <a name="overview"></a>Vue d'ensemble

En mai 2018, une loi européenne relative à la confidentialité, le [Règlement général sur la protection des données (RGPD)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), va entrer en vigueur. Le règlement RGPD établit de nouvelles règles que doivent respecter les sociétés, les administrations, les ONG et d’autres organisations qui proposent des biens et services aux personnes de l’Union européenne ou qui collectent et analysent des données liées aux résidents de l’Union européenne. Le règlement RGPD s’applique, quel que soit votre pays de résidence. 

Microsoft met à disposition des produits et des services permettant de mieux répondre aux exigences du règlement RGPD. Pour plus d’informations sur la stratégie Microsoft Privacy, consultez le [Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

L’authentification unique fluide Azure AD crée le type de journal suivant, qui peut contenir des informations EUII :

- Fichiers journaux des traces Azure AD Connect

La conformité RGPD pour l’authentification unique fluide est accessible de deux manières :

1.  Sur demande, en extrayant les données d’une personne, puis en supprimant ces données des installations
2.  En garantissant qu’aucune donnée n’est conservée plus de 48 heures

Nous recommandons vivement la deuxième option, car elle est plus facile à implémenter et à gérer. Voici les instructions pour chaque type de journal :

### <a name="delete-azure-ad-connect-trace-log-files"></a>Supprimer les fichiers journaux des traces Azure AD Connect

Vérifiez le contenu du dossier **%ProgramData%\AADConnect** et supprimez le contenu des journaux de traces (fichiers **trace -\*.log**) de ce dossier dans les 48 heures suivant l’installation ou la mise à niveau d’Azure AD Connect ou la modification de la configuration de l’authentification unique fluide, car cette action peut créer des données couvertes par la réglementation RGPD.

>[!IMPORTANT]
>Ne supprimez pas le fichier **PersistedState.xml** de ce dossier, car il est sert à conserver l’état de l’installation précédente d’Azure AD Connect et est utilisé quand une mise à niveau est effectuée. Ce fichier ne contiendra jamais de données relatives à un individu et ne doit jamais être supprimé.

Vous pouvez passer en revue et supprimer ces fichiers journaux des traces à l’aide de l’Explorateur Windows, ou vous pouvez utiliser le script PowerShell suivant pour effectuer les actions nécessaires :

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Enregistrez le script dans un fichier avec l’extension « .PS1 ». Exécutez ce script selon les besoins.

Pour en savoir plus sur les exigences RGPD relatives à Azure AD Connect, consultez [cet article](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Remarque concernant les journaux des contrôleurs de domaine

Si l’enregistrement d’audit est activé, ce produit peut générer des journaux de sécurité pour vos contrôleurs de domaine. Pour en savoir plus sur la configuration des stratégies d’audit, consultez cet [article](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Étapes suivantes

- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
