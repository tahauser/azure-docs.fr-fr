---
title: Azure Active Directory Domain Services pour les fournisseurs de solutions cloud Azure | Microsoft Docs
description: Azure Active Directory Domain Services pour les fournisseurs de solutions cloud Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 313c4853b0f585921739779bb764c50036c9ac8a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services pour les fournisseurs de solutions cloud (CSP) Azure
Cet article explique comment vous pouvez utiliser Azure AD Domain Services dans un abonnement Azure CSP.

## <a name="overview-of-azure-csp"></a>Présentation d’Azure CSP
Azure CSP est un programme destiné aux partenaires Microsoft qui fournit un canal de licence pour divers services cloud Microsoft. Azure CSP permet aux partenaires de gérer les ventes, de prendre en charge les relations relatives à la facturation, de fournir un support technique et sur la facturation et d’être le seul point de contact du client. De plus, Azure CSP fournit un ensemble complet d’outils, notamment un portail libre-service et des API connexes. Ces outils permettent aux partenaires CSP de provisionner et gérer facilement des ressources Azure et de fournir une facturation pour les clients et leurs abonnements.

Le [portail Espace partenaires](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fait office de point d’entrée pour tous les partenaires Azure CSP. Il fournit des fonctionnalités de gestion de client complètes, un traitement automatisé et bien plus encore. Les partenaires Azure CSP peuvent utiliser les fonctionnalités de l’Espace partenaires en utilisant une interface utilisateur basée sur le web ou PowerShell et différents appels d’API.

Le schéma suivant illustre le fonctionnement général du modèle CSP. Contoso a un annuaire Azure AD Active Directory. Il a un partenariat avec un fournisseur de solutions cloud, qui déploie et gère les ressources de son abonnement Azure CSP. Contoso peut également avoir des abonnements Azure ordinaires (directs), qui lui sont facturés directement.

![Vue d’ensemble du modèle CSP](./media/csp/csp_model_overview.png)

Le locataire du partenaire CSP a trois groupes d’agents spéciaux : les agents d’administration, les agents de support technique et les agents de vente. Le groupe des agents d’administration est attribué au rôle d’administrateur de locataire dans l’annuaire Azure AD de Contoso. Ainsi, un utilisateur appartenant au groupe d’agents d’administration du partenaire CSP a des privilèges d’administration de locataire dans l’annuaire Azure AD de Contoso. Quand le partenaire CSP provisionne un abonnement Azure CSP pour Contoso, son groupe d’agents d’administration est affecté au rôle de propriétaire pour cet abonnement. Ainsi, les agents d’administration du partenaire CSP ont les privilèges nécessaires pour provisionner des ressources Azure telles que des machines virtuelles, des réseaux virtuels et Azure AD Domain Services pour le compte de Contoso.

Pour plus d’informations, consultez la [présentation d’Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Avantages d’utiliser Azure Active Directory Domain Services dans un abonnement Azure CSP
Azure AD Domain Services fournit des services compatibles avec Windows Server AD dans Azure, tels que LDAP, l’authentification Kerberos/NTLM, la jonction de domaine, la stratégie de groupe et DNS. Au fil des décennies, de nombreuses applications ont été générées pour fonctionner avec Active Directory à l’aide de ces fonctionnalités. De nombreux éditeurs de logiciels indépendants (ISV) ont généré et déployé des applications dans les locaux de leurs clients. La prise en charge de ces applications est onéreuse, car elle requiert souvent l’accès aux différents environnements dans lesquels elles sont déployées. Avec des abonnements Azure CSP, vous avez une solution plus simple avec l’adaptabilité et la flexibilité d’Azure.

Azure AD Domain Services prend désormais en charge les abonnements Azure CSP. Vous pouvez déployer votre application dans un abonnement Azure CSP lié à l’annuaire Azure AD de votre client. Ainsi, vos employés (équipes du support) peuvent gérer, administrer et réviser les machines virtuelles sur lesquelles votre application est déployée à l’aide des informations d’identification d’entreprise de votre organisation. De plus, vous pouvez provisionner un domaine managé Azure AD Domain Services pour l’annuaire Azure AD de votre client. Votre application est connectée au domaine managé de votre client. Ainsi, les fonctionnalités au sein de votre application qui s’appuient sur Kerberos/NTLM, LDAP ou [l’API System.DirectoryServices](https://msdn.microsoft.com/library/system.directoryservices) fonctionnent de manière fluide sur l’annuaire de votre client. Vos clients profitent pleinement de la consommation de votre application en tant que service, sans avoir à se soucier de la gestion de l’infrastructure sur laquelle l’application est déployée.

Vous acquittez toute la facturation des ressources Azure que vous utilisez dans cet abonnement, y compris Azure AD Domain Services. Vous contrôlez en totalité la relation avec le client en ce qui concerne les ventes, la facturation, le support technique, etc. Grâce à la flexibilité de la plateforme Azure CSP, une petite équipe d’agents de support peut prendre en charge de nombreux clients pour lesquels des instances de votre application sont déployées.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modèles de déploiement CSP pour Azure AD Domain Services
Il existe deux façons d’utiliser Azure AD Domain Services avec un abonnement Azure CSP. Choisissez celui qui convient, suivant l’importance qu’accordent vos clients à la sécurité et à la simplicité.

### <a name="direct-deployment-model"></a>Modèle de déploiement direct
Dans ce modèle de déploiement, Azure AD Domain Services est activé au sein d’un réseau virtuel appartenant à l’abonnement Azure CSP. Les agents d’administration du partenaire CSP ont les privilèges suivants :
* Privilèges d’administrateur général dans l’annuaire Azure AD du client
* Privilèges de propriétaire d’abonnement sur l’abonnement Azure CSP

![Modèle de déploiement direct](./media/csp/csp_direct_deployment_model.png)

Dans ce modèle de déploiement, les agents d’administration du fournisseur CSP peuvent gérer les identités pour le client. Ces agents d’administration peuvent provisionner de nouveaux utilisateurs et groupes, ajouter des applications dans l’annuaire Azure AD du client, etc. Ce modèle de déploiement peut être adapté aux petites organisations qui n’ont pas d’administrateur d’identité dédié ou qui préfèrent que le partenaire CSP gère les identités pour leur compte.


### <a name="peered-deployment-model"></a>Modèle de déploiement appairé
Dans ce modèle de déploiement, Azure AD Domain Services est activé au sein d’un réseau virtuel appartenant au client, c’est-à-dire dans le cadre d’un abonnement Azure direct payé par le client. Le partenaire CSP peut ensuite déployer des applications au sein d’un réseau virtuel appartenant à l’abonnement CSP du client. Les réseaux virtuels peuvent être connectés à l’aide de l’appairage de réseau virtuel Azure. Ainsi, les charges de travail/applications déployées par le partenaire CSP dans l’abonnement Azure CSP peuvent se connecter au domaine managé du client provisionné dans l’abonnement Azure direct du client.

![Modèle de déploiement appairé](./media/csp/csp_peered_deployment_model.png)

Ce modèle de déploiement fournit une séparation des privilèges et permet aux agents de support technique du partenaire CSP d’administrer l’abonnement Azure et de déployer et gérer les ressources qu’il contient. Toutefois, les agents de support technique du partenaire CSP n’ont pas besoin d’avoir des privilèges d’administrateur général sur l’annuaire Azure AD du client. Les administrateurs d’identité du client peuvent continuer à gérer les identités pour leur organisation.

Ce modèle de déploiement peut être adapté aux scénarios où un éditeur de logiciels indépendant (ISV) fournit une version hébergée de son application locale, qui doit également se connecter à l’annuaire Active Directory du client.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administration des domaines managés Azure AD Domain Services dans les abonnements CSP
Tenez compte des considérations importantes suivantes quand vous administrez un domaine managé dans un abonnement Azure CSP :

* **Les agents d’administration CSP peuvent provisionner un domaine managé à l’aide de leurs informations d’identification :** Azure AD Domain Services prend en charge les abonnements Azure CSP. Ainsi, les utilisateurs appartenant au groupe d’agents d’administration d’un partenaire CSP peuvent provisionner un nouveau domaine managé Azure AD Domain Services.

* **Les fournisseurs de solutions cloud peuvent programmer au moyen d’un script la création de domaines managés pour leurs clients à l’aide de PowerShell :** consultez [Activer Azure Active Directory Domain Services à l’aide de PowerShell](active-directory-ds-enable-using-powershell.md) pour plus d’informations.

* **Les agents d’administration CSP ne peuvent pas effectuer de tâches de gestion en continu sur le domaine managé à l’aide de leurs informations d’identification :** les utilisateurs administrateurs CSP ne peuvent pas effectuer de tâches de gestion courantes dans le domaine managé à l’aide de leurs informations d’identification. Ces utilisateurs étant externes à l’annuaire Azure AD du client, leurs informations d’identification ne sont pas disponibles dans l’annuaire Azure AD du client. Ainsi, Azure AD Domain Services n’a pas accès aux hachages de mot de passe Kerberos et NTLM pour ces utilisateurs. Ces utilisateurs ne peuvent donc pas être authentifiés sur les domaines managés Azure AD Domain Services.

  > [!WARNING]
  > **Vous devez créer un compte d’utilisateur dans l’annuaire du client pour effectuer des tâches d’administration courantes sur le domaine managé.**
  > Vous ne pouvez pas vous connecter au domaine managé à l’aide des informations d’identification d’un utilisateur administrateur CSP. Pour ce faire, utilisez les informations d’identification d’un compte d’utilisateur appartenant à l’annuaire Azure AD du client. Vous avez besoin de ces informations d’identification pour les tâches telles que la jonction de machines virtuelles au domaine managé, l’administration du système DNS ou l’administration de la stratégie de groupe.
  >

* **Le compte d’utilisateur créé pour l’administration courante doit être ajouté au groupe « Administrateurs AAD DC » :** le groupe « Administrateurs AAD DC » dispose de privilèges pour effectuer certaines tâches d’administration déléguée dans le domaine managé. Ces tâches comprennent la configuration du système DNS, la création d’unités d’organisation, l’administration de la stratégie de groupe, etc. Pour qu’un partenaire CSP effectue ces tâches sur un domaine managé, un compte d’utilisateur doit être créé dans l’annuaire Azure AD du client. Les informations d’identification pour ce compte doivent être partagées avec les agents d’administration du partenaire CSP. De plus, ce compte d’utilisateur doit être ajouté au groupe « Administrateurs AAD DC » pour qu’il puisse effectuer des tâches de configuration sur le domaine managé.


## <a name="next-steps"></a>Étapes suivantes
* [S’inscrire au programme Azure CSP](https://partnercenter.microsoft.com/partner/programs) et commencer à développer une activité par le biais d’Azure CSP.
* Passer en revue la liste des [services Azure disponibles dans Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Activer les services de domaine Azure AD à l’aide de PowerShell](active-directory-ds-enable-using-powershell.md)
* [Prise en main des services de domaine Azure AD](active-directory-ds-getting-started.md)
