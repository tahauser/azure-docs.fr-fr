---
title: "Corriger les configurations de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation de l’Azure Security Center **Corriger les configurations de sécurité**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 412234b1486fa15cbc399bcf43be8ce90aac252a
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corriger les configurations de sécurité dans Azure Security Center
Azure Security Center analyse quotidiennement le système d’exploitation de vos machines virtuelles et ordinateurs et recherche les configurations qui pourraient les rendre plus vulnérables aux attaques. Security Center vous recommande de résoudre les vulnérabilités lorsque la configuration du système d’exploitation ne correspond pas aux règles de configuration de sécurité recommandées et d’apporter des modifications à la configuration pour supprimer ces vulnérabilités.

Consultez la [liste des règles de configuration recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations surveillées. Consultez [Personnaliser les configurations de sécurité du système d’exploitation](security-center-customize-os-security-config.md) pour apprendre à personnaliser les évaluations de configuration de sécurité dans Security Center.

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
La correction de l’incohérence des configurations de sécurité est présentée sous forme de recommandations dans Security Center. Cette recommandation s’affichera dans **Recommandations** et dans **Calcul**.

Dans cet exemple, nous allons examiner la recommandation **Corriger les configurations de sécurité** dans **Calcul**.
1. Dans le menu principal de Security Center, sélectionnez **Calcul**.

   ![Corriger les configurations de sécurité][1]

2. Sous **Calcul**, sélectionnez **Corriger les configurations de sécurité**. **Configurations de sécurité** s’affiche.

   ![Configurations de sécurité][2]

  La partie supérieure du tableau de bord indique :

  - Le nombre total de règles par niveau de gravité que la configuration du système d’exploitation de vos machines virtuelles et ordinateurs ne respecte pas.
  - Le nombre total de règles par type que la configuration du système d’exploitation de vos machines virtuelles et ordinateurs ne respecte pas.
  - Le nombre total de règles que vos configurations de système d’exploitation Windows et Linux ne respectent pas.

  La partie inférieure du tableau de bord répertorie toutes les règles non respectées par vos ordinateurs et machines virtuelles, ainsi que le niveau de gravité de la mise à jour manquante. Cette liste comprend les éléments suivants :

  - **CCEID** : identificateur unique CCE pour la règle. Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques pour les règles de configuration.
  - **NOM** : nom de la règle non respectée.
  - **TYPE DE RÈGLE** : clé de Registre, stratégie de sécurité OU stratégie d’audit.
  - **NO. DE MACHINES VIRTUELLES ET D’ORDINATEURS** : nombre total de machines virtuelles et d’ordinateurs ne respectant pas cette règle.
  - **GRAVITÉ DE LA RÈGLE** : valeur de gravité CCE (critique, important ou avertissement).
  - **ÉTAT**: état actuel de la recommandation :

    - **Ouverte**: la recommandation n’a pas encore été prise en compte.
    - **En cours** : la recommandation est actuellement appliquée à ces ressources ; aucune action de votre part n’est nécessaire.
    - **Résolue** : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée est grisée).

3. Sélectionnez une règle non respectée dans la liste pour afficher les détails.

   ![Règles de configuration ayant échoué][3]

  Ce panneau contient les informations suivantes :

  - NOM : nom de règle
  - CCIED : identificateur unique CCE pour la règle
  - Version du système d’exploitation : version du système d’exploitation de la machine virtuelle ou de l’ordinateur.
  - GRAVITÉ DE LA RÈGLE : valeur de gravité CCE (critique, important ou avertissement).
  - DESCRIPTION COMPLÈTE : description de la règle.
  - VULNÉRABILITÉ : explication de la vulnérabilité ou du risque si la règle n’est pas appliquée
  - IMPACT POTENTIEL : impact sur l’activité lorsque la règle est appliquée.
  - CONTRE-MESURE : étapes correctives.
  - VALEUR ATTENDUE : valeur attendue lorsque Security Center analyse la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle
  - VALEUR RÉELLE : valeur retournée après analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle
  - OPÉRATION DE LA RÈGLE : opération de règle utilisée par Security Center lors de l’analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle

4. Sélectionnez l’icône **Recherche** dans le ruban supérieur. Recherche ouvre la liste des espaces de travail ayant des machines virtuelles et des ordinateurs avec l’incohérence des configurations de sécurité sélectionnée. Ce panneau de sélection d’espaces de travail s’affiche uniquement si la règle sélectionnée s’applique à plusieurs machines virtuelles connectées à différents espaces de travail.

  ![Espaces de travail répertoriés][4]

5. Sélectionnez un espace de travail. Une requête de recherche Log Analytics filtrée sur l’espace de travail avec l’incohérence des configurations de sécurité s’ouvre.

  ![Espace de travail avec vulnérabilité de système d’exploitation][5]

6. Sélectionnez un ordinateur dans la liste pour plus d’informations. Un autre résultat de recherche s’ouvre avec les informations filtrées uniquement pour cet ordinateur.

  ![Filtré sur cet ordinateur][6]

## <a name="next-steps"></a>étapes suivantes
Cet article vous a montré comment implémenter la recommandation de Security Center « Corriger les configurations de sécurité ». Consultez [Personnaliser les configurations de sécurité du système d’exploitation](security-center-customize-os-security-config.md) pour apprendre à personnaliser les évaluations de configuration de sécurité dans Security Center.

Vous pouvez consulter l’ensemble des règles de configuration [ici](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques pour les règles de configuration. Visitez le site [CCE](https://nvd.nist.gov/cce/index.cfm) pour plus d’informations.

Pour plus d’informations sur Security Center, consultez les ressources suivantes :

* [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md) : répertorie les machines virtuelles Windows et Linux prises en charge.
* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
