---
title: "Azure AD Connect Health et règlement général sur la protection des données (RGPD) | Microsoft Docs"
description: "Ce document explique comment obtenir la conformité RGPD avec Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: d66f717f546271a5e5c3c49d6cbaef1c190d18d8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>Conformité RGPD et Azure AD Connect Health 

Le [règlement général sur la protection des données (RGPD)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) est une loi de l’Union européenne sur la protection et la confidentialité des données. Le règlement RGPD établit de nouvelles règles que doivent respecter les sociétés, les administrations, les ONG et d’autres organisations qui proposent des biens et services aux personnes de l’Union européenne ou qui collectent et analysent des données liées à des résidents de l’Union européenne. 

Microsoft met à disposition des produits et des services permettant de mieux répondre aux exigences du règlement RGPD. Pour plus d’informations sur la stratégie Microsoft Privacy, consultez le [Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

Azure AD Connect Health surveille votre infrastructure d’identité locale et votre service de synchronisation. Il fournit également des insights et des alertes de surface. Pour l’entrée en vigueur du règlement RGPD en mai 2018, Microsoft s’est engagé à rendre tous ses services cloud conformes au règlement RGPD, ainsi qu’à fournir des garanties sur ses engagements contractuels se rapportant au dit règlement. 

>[!NOTE] 
> Cet article présente la conformité RGPD dans Azure AD Connect Health. Pour plus d’informations sur la conformité RGPD dans Azure AD Connect, consultez [Conformité avec RGPD et Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>Classification RGPD
Azure AD Connect Health est classé dans la catégorie **Processeur de données** de la classification catégorie RGPD. En tant que pipeline de processeur de données, le service fournit un traitement des données pour les partenaires et les clients. Azure AD Connect Health ne génère pas de données utilisateur et n’a aucun contrôle indépendant sur les données personnelles qui sont collectées, ni sur la façon dont elles sont utilisées. Dans Azure AD Connect Health, l’extraction, l’agrégation et l’analyse de données, ainsi que la création de rapports contenant ces données, sont basées sur des données locales existantes. 

## <a name="data-retention-policy"></a>Stratégie de conservation des données
Azure AD Connect Health ne génère pas de rapports, n’effectue pas d’analyses et ne fournit pas d’insights au-delà de 30 jours. Par conséquent, Azure AD Connect Health ne stocke pas, ne traite pas et ne conserve pas de données au-delà de 30 jours. Cette conception est conforme aux réglementations RGPD, aux réglementations de Microsoft concernant la conformité, ainsi qu’aux stratégies de conservation des données Azure AD. 

Les serveurs sur lesquels il existe des alertes d’**erreur** **Les données du Service de contrôle d’intégrité ne sont pas à jour** pendant plus de 30 jours consécutifs suggèrent qu’aucune donnée n’a atteint Connect Health pendant cet intervalle de temps. Ces serveurs sont désactivés et n’apparaissent pas dans le portail Connect Health. Pour les réactiver, vous devez désinstaller et [réinstaller l’agent d’intégrité](active-directory-aadconnect-health-agent-install.md). Notez que cette désactivation ne s’applique pas aux **avertissements** avec le même type d’alerte. Les avertissements indiquent qu’il manque une partie des données en provenance du serveur pour lequel vous voulez être alerté. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Désactiver la collecte et la surveillance des données dans Azure AD Connect Health
Azure AD Connect Health vous permet d’arrêter la collecte des données pour chaque serveur surveillé ou pour une instance d’un service surveillé. Par exemple, vous pouvez arrêter la collecte des données pour chaque serveur ADFS qui est surveillé à l’aide d’Azure AD Connect Health. Vous pouvez également arrêter la collecte de données pour l’intégralité d’une instance ADFS qui est surveillée par Azure AD Connect Health. Dans ce cas, les serveurs correspondants sont supprimés du portail Azure AD Connect Health, après l’arrêt de la collecte de données. 

>[!IMPORTANT]
> Pour supprimer les serveurs surveillés d’Azure AD Connect Health, vous devez disposer de privilèges d’administrateur général Azure AD ou du rôle Contributeur dans RBAC.
>
> La suppression d’un serveur ou d’une instance de service d’Azure AD Connect Health est une action irréversible. 

### <a name="what-to-expect"></a>À quoi s’attendre ?
Si vous arrêtez la collecte et la surveillance des données pour un serveur surveillé ou une instance de service surveillé, prenez en compte les points suivants :

- Lorsque vous supprimez une instance d’un service surveillé, l’instance est supprimée de la liste du service de surveillance d’Azure AD Connect Health dans le portail. 
- Lorsque vous supprimez un serveur surveillé ou une instance d’un service surveillé, Health Agent n’est pas désinstallé ni supprimé de vos serveurs. Health Agent est configuré pour ne pas envoyer de données vers Azure AD Connect Health. Vous devez désinstaller manuellement Health Agent sur les serveurs précédemment surveillés.
- Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Toutes les données appartenant à l’instance du service surveillé sont supprimées, conformément à la stratégie de conservation des données de Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Désactiver la collecte et la surveillance des données pour un serveur surveillé
Regardez [comment supprimer un serveur d’Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Désactiver la collecte et la surveillance des données pour une instance d’un service surveillé
Regardez [comment supprimer une instance de service d’Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Réactiver la collecte et la surveillance des données dans Azure AD Connect Health
Si vous souhaitez réactiver la surveillance dans Azure AD Connect Health pour un service surveillé ayant été supprimé, vous devez désinstaller puis [réinstaller Health Agent](active-directory-aadconnect-health-agent-install.md) sur tous les serveurs.


## <a name="next-steps"></a>Étapes suivantes
* [Lire la politique de confidentialité Microsoft sur le Centre de confidentialité](https://www.microsoft.com/trustcenter)
* [Azure AD Connect et RGPD](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
