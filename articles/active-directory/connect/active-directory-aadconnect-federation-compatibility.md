---
title: "Liste de compatibilité de fédération Azure AD"
description: "Cette page présente les fournisseurs d’identité non-Microsoft qui peuvent être utilisés pour mettre en œuvre l’authentification unique."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: billmath
ms.openlocfilehash: 86c247b6a9d197e89040109ac6b8686e3310dbf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Liste de compatibilité de fédération Azure AD
Azure Active Directory fournit l’authentification unique et une sécurité de l’accès aux applications améliorée pour Office 365 et d’autres ressources de Microsoft Online Services pour des implémentations hybrides et uniquement dans le cloud ne nécessitant aucune solution non-Microsoft. À l’instar de la plupart des services Microsoft Online, Office 365 est intégré à Azure Active Directory pour les services de répertoire, l’authentification et l’autorisation. En outre, Azure Active Directory fournit l’authentification unique à des milliers d’applications SaaS et à des applications web locales. Consultez la galerie d’applications Azure Active Directory pour connaître les applications SaaS prises en charge.

Pour les entreprises qui ont investi dans des solutions de fédération non-Microsoft, cette rubrique contient des conseils relatifs à la configuration de l’authentification unique des utilisateurs Windows Server Active Directory avec les services Microsoft Online à l’aide de fournisseurs d’identité non-Microsoft qui sont inclus dans la « liste de compatibilité de fédération Azure Active Directory » ci-dessous. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/), organisme tiers, a testé, de la part de Microsoft, ces expériences d’authentification unique à l’aide de fournisseurs d’identité non-Microsoft par rapport à un ensemble de cas d’utilisation courants avec Azure Active Directory.

Pour plus d’informations sur la façon dont votre fournisseur d’identité tiers peut être répertorié ici, contactez Oxford Computer Groupe à l’adresse [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Oxford Computer Group a uniquement testé la fonctionnalité de fédération de ces scénarios d’authentification unique. Oxford Computer Group n’a pas testé les composants de synchronisation, d’authentification à 2 facteurs, etc., de ces scénarios d’authentification unique.
> 
> Par ailleurs, l’utilisation de la connexion au moyen d’un ID secondaire à UPN n’est pas testée dans ce programme.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP avec Access Policy Manager BIG-IP ver. 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [Authentification composite DigitalPersona](#digitalpersona-composite-authentication)
* [ForgeRock Identity Platform Access Management V5.x](#forgerock-identity-platform-access-management-v5x)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&amp;go 5.3](#signgo-53) 
* [Portail du service en ligne SoftBank Technology](#softbank)
* [VMware Workspace One](#vmware-workspace-one)



> [!IMPORTANT]
> Étant donné qu’il s’agit de produits tiers, Microsoft n’offre aucun support technique concernant les problèmes et questions liés au déploiement, à la configuration, au dépannage, aux meilleures pratiques, etc., de ces fournisseurs d’identité tiers. Pour toute question sur ces fournisseurs d’identité et pour bénéficier d’un support technique, contactez directement les éditeurs tiers pris en charge.
> 
> L’interopérabilité de ces fournisseurs d’identité tiers avec les services cloud Microsoft a uniquement été testée à l’aide des protocoles WS-Federation et WS-Trust. Le protocole SAML n’a pas fait l’objet de tests.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

Voici la matrice de prise en charge de scénarios de cette expérience d’authentification : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |
| Applications modernes utilisant ADAL (p. ex., Office 2016) |Pris en charge |Aucune |

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec AD FS, consultez [Services AD FS (Active Directory Federation Services)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec une synchronisation de mot de passe, consultez [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations, consultez [AuthAnvil Single Sign On](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP avec Access Policy Manager BIG-IP ver. 11.3x – 11.6x

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Non pris en charge |Non pris en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |

Pour plus d’informations sur BIG-IP Access Policy Manager, consultez [BIG-IP Access Policy Manager.](https://f5.com/products/modules/access-policy-manager) 

Pour obtenir des instructions sur la façon de configurer ce STS pour fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec BIG-IP Access Policy Manager, téléchargez le fichier pdf [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur BitGlass, consultez [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>CA Secure Cloud

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur CA Secure Cloud, consultez [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 version cumulative 4

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur CA SiteMinder, consultez [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Le contrôle d’accès des clients n’est pas pris en charge. |

Pour plus d’informations sur Centrify, consultez [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur Dell One Identity Cloud Access Manager, consultez [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Pour savoir comment configurer ce STS afin de fournir l’expérience d’authentification unique à vos utilisateurs Office 365, consultez [Configure Office 365 Users](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365). 

## <a name="digitalpersona-composite-authentication"></a>Authentification composite DigitalPersona  

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations, consultez [DigitalPersona Composite Authentication](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf).

## <a name="forgerock-identity-platform-access-management-v5x"></a>ForgeRock Identity Platform Access Management V5.x

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |

Pour plus d’informations, consultez [ForgeRock Identity Platform Access Management V5.x](https://backstage.forgerock.com/knowledge/kb/article/a98278517).

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur IBM Tivoli Federated Identity Manager, consultez [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517).

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur IceWall Federation, consultez [IceWall Federation Version 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) et [IceWall Federation - Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="memority"></a>Memority

Voici la matrice de prise en charge de scénarios de cette expérience d’authentification : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur l’utilisation de Memority, consultez [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations, consultez [NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée nécessite la configuration supplémentaire d’un serveur web et de l’application Okta. |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Authentification Windows intégrée |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur Okta, consultez [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Authentification Windows intégrée |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Authentification Windows intégrée |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur OneLogin, consultez [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM Virtual Identity Server Federation Services

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Authentification Windows intégrée |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |

Pour plus d’informations sur les stratégies d’accès des clients, consultez [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx).





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |

Pour obtenir des instructions sur la façon de configurer ce STS afin de fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec PingFederate, consultez l’une des rubriques suivantes : 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Authentification Windows intégrée |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur RadiantOne CFS, consultez [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations, consultez [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique : 

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Aucun |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |

Pour plus d’informations sur SecureAuth, consultez [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Sign&go 5.3

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |Contrats Kerberos pris en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |Aucun |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucun |

Sign&go 5.3 prend en charge l’authentification Kerberos via la configuration d’un contrat Kerberos.  Pour obtenir de l’aide sur cette configuration, contactez Ilex ou consultez le guide d’installation et de configuration [Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf).

## <a name="softbank-technology-online-service-gate"></a>Portail du service en ligne SoftBank Technology

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations sur Online Service Gate de SoftBank Technology, consultez [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/).

## <a name="vmware-workspace-one"></a>VMware Workspace One

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client | Support | Exceptions |
| --- | --- | --- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) |Pris en charge |L’authentification Windows intégrée n’est pas prise en charge |
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) |Pris en charge |Aucune |

Pour plus d’informations, consultez [VMware Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf).

