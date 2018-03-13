---
title: "Collecte de données dans Azure Security Center | Microsoft Docs"
description: " Découvrez comment activer la collecte des données dans Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: terrylan
ms.openlocfilehash: d5f2c9960b720fc44f37956f9150e89d6425d154
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="data-collection-in-azure-security-center"></a>Collecte de données dans Azure Security Center
Azure Security Center collecte des données à partir de vos machines virtuelles Azure et ordinateurs autres qu’Azure pour surveiller les menaces et vulnérabilités de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Il peut s’agir des données suivantes : type et version de système d’exploitation, journaux de système d’exploitation (journaux d’événements Windows), processus en cours d’exécution, nom de machine, adresses IP, utilisateur connecté et ID de locataire. Microsoft Monitoring Agent copie également les fichiers de vidage sur incident dans votre espace de travail.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Activer l’approvisionnement automatique de Microsoft Monitoring Agent     
Lorsque l’approvisionnement automatique est activé, Security Center approvisionne Microsoft Monitoring Agent pour toutes les machines virtuelles Azure prises en charge et toutes celles nouvellement créées. L’approvisionnement automatique est fortement recommandé et est requis pour les abonnements appartenant au niveau Standard de Security Center.

> [!NOTE]
> La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources. Pour plus d’informations, consultez la section [Désactiver l’approvisionnement automatique](security-center-enable-data-collection.md#disable-automatic-provisioning) de cet article. La collecte des artefacts et les captures instantanées des disques de machine virtuelle sont activées, même si l’approvisionnement automatique est désactivé.
>
>

Pour activer l’approvisionnement automatique de Microsoft Monitoring Agent :
1. Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement.
3. Dans **Stratégie de sécurité**, sélectionnez **Collecte de données**.
4. Dans **Intégration**, sélectionnez **Activé** pour activer l’approvisionnement automatique.
5. Sélectionnez **Enregistrer**.

![Activer l’approvisionnement automatique][1]

## <a name="default-workspace-configuration"></a>Configuration d’espace de travail par défaut
Les données collectées par Security Center sont stockées dans les espaces de travail Log Analytics.  Vous pouvez choisir de stocker les données collectées à partir des machines virtuelles Azure dans des espaces de travail créés par Security Center ou dans un espace de travail existant que vous avez créé.

Pour utiliser votre espace de travail Log Analytics existant :
- L’espace de travail doit être associé à votre abonnement Azure sélectionné.
- Au minimum, vous devez avoir des autorisations de lecture pour accéder à l’espace de travail.

Pour sélectionner un espace de travail Log Analytics existant :

1. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Use another workspace** (Utiliser un autre espace de travail).

   ![Sélectionner un espace de travail existant][2]

2. Dans le menu déroulant, sélectionnez un espace de travail pour stocker les données collectées.

> [!NOTE]
> Dans le menu déroulant, seuls les espaces de travail auxquels vous avez accès et se trouvant dans votre abonnement Azure sont affichés.
>
>

3. Sélectionnez **Enregistrer**.
4. Après avoir sélectionné **Enregistrer**, vous serez invité à reconfigurer les machines virtuelles surveillées.

   - Sélectionnez **Non** si vous souhaitez que les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles machines virtuelles. Les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles installations d’agent ; machines virtuelles nouvellement détectées qui n’ont pas Microsoft Monitoring Agent installé.
   - Sélectionnez **Oui** si vous souhaitez que les nouveaux paramètres de l’espace de travail s’appliquent à toutes les machines virtuelles. En outre, chaque machine virtuelle connectée à un espace de travail créé par Security Center est reconnectée au nouvel espace de travail cible.

   > [!NOTE]
   > Si vous sélectionnez Oui, vous ne devez pas supprimer les espaces de travail créés par Security Center tant que toutes les machines virtuelles n’ont pas été reconnectées au nouvel espace de travail cible. Cette opération échoue si un espace de travail est supprimé trop tôt.
   >
   >

   - Sélectionnez **Annuler** pour annuler l’opération.

   ![Sélectionner un espace de travail existant][3]

## <a name="data-collection-tier"></a>Niveau de collecte des données
Security Center peut réduire le volume d’événements tout en conservant suffisamment d’événements pour l’investigation, l’audit et la détection des menaces. Vous pouvez choisir la stratégie de filtrage adaptée à vos abonnements et espaces de travail à partir de quatre ensembles d’événements à collecter par l’agent.

- **Tous les événements** : pour les clients qui souhaitent s’assurer que tous les événements sont collectés. Il s’agit de la valeur par défaut.
- **Commun** : il s’agit d’un ensemble d’événements qui répond aux besoins de la plupart des clients et leur permet d’avoir une piste d’audit complète.
- **Minimal** : un plus petit ensemble d’événements pour les clients qui souhaitent réduire le volume d’événements.
- **Aucun** : désactivation de la collecte d’événements de sécurité à partir des journaux App Locker et de sécurité. Les tableaux de bord de sécurité des clients qui choisissent cette option afficheront uniquement les journaux de pare-feu Windows et les évaluations proactives comme les logiciels anti-programme malveillant, la ligne de base et la mise à jour.

> [!NOTE]
> Ces ensembles ont été conçus pour des scénarios classiques. Veillez à évaluer celui qui correspond à vos besoins avant de l’implémenter.
>
>

Pour déterminer les événements qui appartiennent aux ensembles d’événements **Commun** et **Minimal**, nous avons travaillé avec les clients et les normes industrielles pour en savoir plus sur la fréquence non filtrée de chaque événement et leur utilisation. Nous avons utilisé les instructions suivantes dans ce processus :

- **Minimal** : vérifiez que cet ensemble couvre uniquement les événements qui peuvent indiquer une violation avérée et des événements importants qui ont un volume très faible. Par exemple, cet ensemble contient une connexion utilisateur ayant réussi et ayant échoué (ID d’événement4624, 4625), mais il ne contient pas de déconnexion, ce qui est important pour l’audit, mais pas pour la détection, et a un volume relativement élevé. La plupart du volume de données de cet ensemble est constituée d’événements de connexion et d’un événement de création de processus (ID d’événement 4688).
- **Commun** : fournissez une piste d’audit utilisateur complète dans cet ensemble. Par exemple, cet ensemble contient les connexions et déconnexions de l’utilisateur (ID d’événement 4634). Nous incluons les actions d’audit, telles que les modifications de groupe de sécurité, les opérations Kerberos du contrôleur de domaine clé et les autres événements recommandés par les organisations du secteur.

Les événements qui ont un volume très faible ont été inclus dans l’ensemble Commun, car la motivation principale à préférer cet ensemble à l’ensemble Tous les événements est de réduire le volume et de ne pas filtrer d’événements spécifiques.

Voici le détail complet des ID d’événement App Locker et de sécurité pour chaque ensemble :

| Couche Données | Indicateurs d’événements collectés |
| --- | --- |
| Minimales | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Commun (par défaut) | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> Si vous utilisez l’objet de stratégie de groupe (GPO), il est recommandé d’activer les stratégies d’audit d’événement de création de processus 4688 et le champ *CommandLine* à l’intérieur de l’événement 4688. Pour plus d’informations sur l’événement de création de processus 4688, consultez la [FAQ](security-center-faq.md#what-happens-when-data-collection-is-enabled) de Security Center. Pour plus d’informations sur ces stratégies d’audit, consultez les [recommandations pour la stratégie d’audit](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
>
>

Pour choisir votre stratégie de filtrage :
1. Dans le panneau **Stratégie de sécurité et paramètres**, sélectionnez votre stratégie de filtrage dans **Événements de sécurité**.
2. Sélectionnez **Enregistrer**.

   ![Choix de la stratégie de filtrage][5]

## <a name="disable-automatic-provisioning"></a>Désactiver l’approvisionnement automatique
Vous pouvez désactiver l’approvisionnement automatique à partir des ressources à tout moment en désactivant ce paramètre dans la stratégie de sécurité. L’approvisionnement automatique est fortement recommandé si vous souhaitez obtenir des alertes de sécurité et des recommandations sur les mises à jour système, les vulnérabilités du système d’exploitation et la protection du point de terminaison.

> [!NOTE]
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné.
>
>

1. Retournez au menu principal de Security Center et sélectionnez Stratégie de sécurité.

   ![Désactiver l’approvisionnement automatique][6]

2. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Dans le panneau **Stratégie de sécurité : collecte de données**, dans **Intégration** sélectionnez **Non** pour désactiver l’approvisionnement automatique.
4. Sélectionnez **Enregistrer**.  

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré le fonctionnement de la collecte de données et de l’approvisionnement automatique dans Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md): découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment les données sont gérées et protégées dans Security Center.
* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
