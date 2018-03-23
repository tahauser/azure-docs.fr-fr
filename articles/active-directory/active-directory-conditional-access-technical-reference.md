---
title: Référence des paramètres d’accès conditionnel Azure Active Directory | Microsoft Docs
description: Découvrez les paramètres pris en charge dans une stratégie d’accès conditionnel Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: ba93e1b597bb6bdb752563f2873b8e0c5f7a8181
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Référence des paramètres d’accès conditionnel Azure Active Directory

Vous pouvez utiliser [l’accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) pour contrôler les méthodes d’accès des utilisateurs autorisés aux ressources.   

Cet article fournit des informations de prise en charge sur les options de configuration suivantes dans une stratégie d’accès conditionnel : 

- Affectations des applications cloud

- Condition de plateforme d’appareil 

- Condition des applications clientes

- Exigence d’une application cliente approuvée


Si ce n’est pas les informations que vous recherchez, laissez un commentaire à la fin de cet article.

## <a name="cloud-apps-assignments"></a>Affectations des applications cloud

Les stratégies d’accès conditionnel vous permettent de contrôler la façon dont les utilisateurs accèdent à vos [applications cloud](active-directory-conditional-access-conditions.md#cloud-apps). Quand vous configurez une stratégie d’accès conditionnel, vous devez sélectionner au moins une application cloud. 

![Sélectionner les applications cloud pour votre stratégie](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Applications cloud Microsoft

Vous pouvez affecter une stratégie d’accès conditionnel aux applications cloud Microsoft  suivantes :

- Azure Information Protection - [En savoir plus](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (notamment OneDrive Entreprise et Project Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Autres applications 

Outre les applications cloud Microsoft, vous pouvez affecter une stratégie d’accès conditionnel aux types d’applications cloud suivants :

- applications connectées à Azure AD ;

- application software as a service (SaaS) fédérée préintégrée ;

- applications qui utilisent l’authentification unique (SSO) par mot de passe ;

- applications métier ;

- applications utilisant le proxy d’application Azure AD.


## <a name="device-platform-condition"></a>Condition de plateforme d’appareil

Dans une stratégie d’accès conditionnel, vous pouvez configurer la condition de plateforme d’appareil pour lier la stratégie au système d’exploitation d’un client. L’accès conditionnel Azure AD prend en charge les plateformes suivantes :

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Lier la stratégie d’accès au système d’exploitation client](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Condition d’applications clientes 

Dans votre stratégie d’accès conditionnel, vous pouvez configurer la condition [Applications clientes](active-directory-conditional-access-conditions.md#client-apps) pour lier la stratégie à l’application cliente à l’origine d’une tentative d’accès. Configurez la condition d’applications clientes afin d’accorder ou de bloquer l’accès en cas de tentative d’accès à partir des types d’applications clientes suivantes :

- Browser
- Applications mobiles et de bureau

![Contrôler l’accès pour les applications clientes](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Navigateurs pris en charge 

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Navigateurs** comme application cliente.

![Contrôler l’accès pour les navigateurs pris en charge](./media/active-directory-conditional-access-technical-reference/05.png)

Ce paramètre fonctionne avec tous les navigateurs. Toutefois, pour satisfaire à une stratégie d’appareil, telle qu’une exigence d’appareil conforme, les systèmes d’exploitation et navigateurs suivants sont pris en charge :


| SE                     | Navigateurs                            | Support     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Vérification][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Vérification][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Vérification][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Vérification][1] |
| Android                | Chrome, Intune Managed Browser      | ![Vérification][1] |
| Windows Phone          | Internet Explorer, Edge             | ![Vérification][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Vérification][1] |
| Windows Server 2016    | Chrome                              | Bientôt disponible |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Vérification][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Vérification][1] |
| macOS                  | Chrome, Safari                      | ![Vérification][1] |



#### <a name="chrome-support"></a>Prise en charge Chrome

Dans **Windows 10 Creators Update (version 1703)** ou version ultérieure, la prise en charge de Chrome nécessite l’installation de [cette extension](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Pour la prise en charge de Chrome dans **Windows 8.1 et 7**, créez la clé de Registre suivante :

|    |    |
|--- | ---|
|path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|NOM | 1 |
|type | REG_SZ (String) |
|Données | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Ces navigateurs prennent en charge l’authentification des appareils, ce qui permet de les identifier et de les valider par rapport à une stratégie. La vérification de l’appareil échoue si le navigateur est en cours d’exécution en mode privé. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Applications mobiles et clients de bureau pris en charge

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Applications mobiles et clients de bureau** comme application cliente.


![Contrôler l’accès pour les applications mobiles ou les clients de bureau pris en charge](./media/active-directory-conditional-access-technical-reference/06.png)


Ce paramètre a un impact sur les tentatives d’accès provenant des applications mobiles et des clients de bureau suivants : 


|Applications clientes|Service cible|Plateforme|
|---|---|---|
|Application distante Azure|Service Application distante Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android et MAC OS X|
|Application Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS, Android|
|Application de messagerie/calendrier/contacts, Outlook 2016, Outlook 2013 (avec l’authentification moderne)|Office 365 Exchange Online|Windows 10|
|Stratégie MFA et d’emplacement pour les applications. Les stratégies basées sur les appareils ne sont pas prises en charge. |Tout service d’application Mes applications|Android et iOS|
|Services Microsoft Teams, soit tous les services qui prennent en charge Microsoft Teams et toutes ses applications clientes : Bureau Windows, iOS, Android, WP et client web|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android et macOS |
|Applications Office 2016, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Applications Office 2016, applications Universal Office, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), prise en charge à venir des groupes Office et de l’application SharePoint|Office 365 SharePoint Online|Windows 10|
|Office 2016 pour Mac OS (Word, Excel, PowerPoint, OneNote uniquement). Prise en charge de OneDrive Entreprise prévue|Office 365 SharePoint Online|Mac OS X|
|Applications mobiles Office|Office 365 SharePoint Online|Android, iOS|
|Application Yammer Office|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office pour Mac OS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Application Outlook Mobile|Office 365 Exchange Online|Android, iOS|
|Application PowerBI|Service PowerBI|Windows 10, Windows 8.1, Windows 7 et iOS|
|Skype Entreprise|Office 365 Exchange Online|Android, IOS |
|Application Visual Studio Team Services|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS, Android|


## <a name="approved-client-app-requirement"></a>Spécification d’application cliente approuvée 

Dans votre stratégie d’accès conditionnel, vous pouvez exiger que toute tentative d’accès aux applications cloud sélectionnées provienne d’une application cliente approuvée. 

![Contrôler l’accès pour les applications clientes approuvées](./media/active-directory-conditional-access-technical-reference/21.png)

Ce paramètre s’applique aux applications clientes suivantes :


- Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft Invoicing
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Planificateur Microsoft
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Entreprise
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Remarques**

- Les applications clientes approuvées prennent en charge la fonctionnalité de gestion des applications mobiles Intune.

- Exigence **Nécessite une application cliente approuvée** :

    - elle prend uniquement en charge iOS et Android pour la [condition de plate-forme de périphérique](#device-platforms-condition).

    - elle ne prend pas en charge l’option **Navigateur** pour la [condition d’applications clientes](#supported-browsers).
    
    - elle remplace l’option **Applications mobiles et clients de bureau** pour la [condition d’applications clientes](#supported-mobile-apps-and-desktop-clients) lorsque cette option est sélectionnée.


## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble de l’accès conditionnel sur la page [Accès conditionnel Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel dans votre environnement, consultez les [Pratiques recommandées d’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


