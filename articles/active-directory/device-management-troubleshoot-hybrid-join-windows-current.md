---
title: "Résolution des problèmes des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory| Documents Microsoft"
description: "Résolution des problèmes des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b98d31efcdbd61cf12e2c905f200c1e54f68f69
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Résolution des problèmes des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory 

Cette rubrique s’applique aux clients suivants :

-   Windows 10
-   Windows Server 2016

Pour les autres clients Windows, consultez [Résoudre des problèmes des appareils hybrides de bas niveau joints à Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Cette rubrique suppose que vous avez [configuré les appareils hybrides joints à Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) de façon à prendre en charge les scénarios suivants :

- Accès conditionnel basé sur les appareils

- [Itinérance d’entreprise des paramètres](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello Entreprise](active-directory-azureadjoin-passport-deployment.md)


Ce document fournit des conseils sur la façon de résoudre les problèmes potentiels. 


Pour Windows 10 et Windows Server 2016, la jonction Azure Active Directory hybride prend en charge la mise à jour Windows du 10 novembre 2015 et au-delà. Nous vous recommandons d’utiliser la mise à jour anniversaire.

## <a name="step-1-retrieve-the-join-status"></a>Étape 1 : récupérer l’état de jonction 

**Pour récupérer l’état de jonction :**

1. Ouvrez une invite de commandes en tant qu’administrateur.

2. Entrez **dsregcmd /status**



    +----------------------------------------------------------------------+
    | État de l’appareil                                                    | +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: NO DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform Crypto Provider TpmProtected: YES KeySignTest: : MUST Run elevated to test.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn:ms-drs:enterpriseregistration.windows.net DomainJoined: YES DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | État utilisateur                                                         | +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizations         WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt: YES



## <a name="step-2-evaluate-the-join-status"></a>Étape 2 : Évaluer l’état de jonction 

Examinez les champs suivants et assurez-vous qu’ils disposent des valeurs attendues :

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

Ce champ indique si l’appareil est joint à Azure AD. Si la valeur est **Non**, la jonction à Azure AD n’est pas encore terminée. 

**Causes possibles :**

- Échec de l’authentification de l’ordinateur pour une jonction.

- Il existe un proxy HTTP dans l’organisation qui ne peut pas être détecté par l’ordinateur

- L’ordinateur ne peut pas atteindre Azure AD pour l’authentification ou Azure DRS pour l’inscription

- L’ordinateur n’est peut-être pas sur le réseau interne de l’entreprise ou un réseau privé virtuel avec une vue directe sur un contrôleur de domaine AD local.

- Si l’ordinateur dispose d’un module de plateforme sécurisée, celui-ci est peut-être en mauvais état.

- Il peut y avoir un problème de configuration des services mentionnés plus haut dans ce document que vous devez vérifier à nouveau. Voici des exemples courants :

    - Votre serveur de fédération n’a pas de points de terminaison WS-Trust activés

    - Votre serveur de fédération n’autorise peut-être pas l’authentification entrante à partir d’ordinateurs de votre réseau à l’aide de l’authentification Windows intégrée.

    - Il n’existe aucun objet de point de connexion de service qui pointe vers le nom de votre domaine vérifié dans Azure AD dans la forêt Active Directory à laquelle l’ordinateur appartient

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

Ce champ indique si l’appareil est joint à un répertoire Active Directory local ou non. Si la valeur est **NON**, l’appareil ne peut pas effectuer de jonction hybride Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Ce champ indique si l’appareil est inscrit auprès d’Azure AD mais en tant qu’appareil personnel (avec la mention *Joint à l’espace de travail*). Cette valeur doit être **NON** pour un ordinateur appartenant à un domaine qui est également une jonction hybride Azure AD. Si la valeur est **OUI**, un compte professionnel ou scolaire a été ajouté avant l’achèvement d’une jonction hybride Azure AD. Dans ce cas, le compte est ignoré lors de l’utilisation de la version mise à jour anniversaire de Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES et AzureADPrt : YES
  
Ces champs indiquent que l’utilisateur s’est correctement authentifié auprès d’Azure AD lors de la connexion à l’appareil. Si les valeurs sont **NON**, il peut être dû :

- Clé de stockage défectueuse (STK) dans le module de plateforme sécurisée associé à l’appareil lors de l’inscription (vérifiez KeySignTest en cours d’exécution avec élévation de privilèges).

- ID de connexion de substitution

- Proxy HTTP introuvable

## <a name="next-steps"></a>Étapes suivantes

Pour toute question, consultez [FAQ sur la gestion des appareils](device-management-faq.md) 