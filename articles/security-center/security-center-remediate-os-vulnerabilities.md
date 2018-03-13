---
title: "Corriger les configurations de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation de l’Azure Security Center, « Corriger les configurations de sécurité »."
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
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corriger les configurations de sécurité dans Azure Security Center
Azure Security Center analyse quotidiennement le système d’exploitation de vos machines virtuelles et ordinateurs et recherche les configurations qui pourraient les rendre plus vulnérables aux attaques. Security Center vous recommande de résoudre les vulnérabilités lorsque la configuration du système d’exploitation ne correspond pas aux règles de configuration de sécurité recommandées et d’apporter des modifications à la configuration pour supprimer ces vulnérabilités.

Consultez la [liste des règles de configuration recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations spécifiques surveillées. Pour savoir comment personnaliser les évaluations de configuration de sécurité, consultez la section [Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center (version préliminaire)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
La correction de l’incohérence des configurations de sécurité est présentée sous forme de recommandations dans Security Center. La recommandation est affichée sous **Recommendations** > **Calcul**.

Dans cet exemple, nous allons examiner la recommandation de correction de l’incohérence des configurations de sécurité dans **Calcul**.
1. Dans Security Center, dans le panneau de gauche, sélectionnez **Calcul**.  
  La fenêtre **Calcul** s’ouvre.

   ![Corriger les configurations de sécurité][1]

2. Sélectionnez **Corriger les configurations de sécurité**.  
  La fenêtre **Configurations de la sécurité** s’ouvre.

   ![La fenêtre Configurations de la sécurité][2]

  La section supérieure du tableau de bord affiche les éléments suivants :

  - **Règles ayant échoué par gravité** : le nombre total de règles par niveau de gravité que la configuration du système d’exploitation de vos machines virtuelles et ordinateurs ne respecte pas.
  - **Règles ayant échoué par type** : le nombre total de règles par type que la configuration du système d’exploitation de vos machines virtuelles et ordinateurs ne respecte pas.
  - **Règles Windows ayant échoué** : le nombre total de règles que vos configurations de système d’exploitation Windows ne respectent pas.
  - **Règles Linux ayant échoué** : le nombre total de règles que vos configurations de système d’exploitation Linux ne respectent pas.

  La partie inférieure du tableau de bord répertorie toutes les règles non respectées par vos ordinateurs et machines virtuelles, ainsi que le niveau de gravité de la mise à jour manquante. La liste comporte les éléments suivants :

  - **CCEID** : identificateur unique CCE pour la règle. Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques aux règles de configuration.
  - **Nom** : nom de la règle non respectée.
  - **Type de règle** : *clé de Registre*, *stratégie de sécurité* ou *stratégie d’audit*.
  - **Nombre de machines virtuelles et d’ordinateurs** : nombre total de machines virtuelles et d’ordinateurs ne respectant pas cette règle.
  - **Gravité de la règle** : valeur de gravité CCE *Critique*, *Important* ou *Avertissement*.
  - **État** : état actuel de la recommandation :

    - **Ouverte** : la recommandation n’a pas encore été prise en compte.
    - **En cours**: la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
    - **Résolu** : la recommandation a été appliquée. Une fois problème résolu, l’entrée est grisée.

3. Pour afficher les détails d’une règle non respectée, sélectionnez-la dans la liste.

   ![Vue détaillée d’une règle de configuration non respectée][3]

   La vue détaillée affiche les informations suivantes :

   - **Nom** : nom de la règle.
   - **CCIED** : identificateur unique CCE pour la règle.
   - **Version du système d’exploitation** : version du système d’exploitation de la machine virtuelle ou de l’ordinateur.
   - **Gravité de la règle** : valeur de gravité CCE *Critique*, *Important* ou *Avertissement*.
   - **Description complète** : description de la règle.
   - **Vulnérabilité** : explication de la vulnérabilité ou du risque si la règle n’est pas appliquée.
   - **Impact potentiel** : impact sur l’activité lorsque la règle est appliquée.
   - **Contre-mesure** : étapes correctives.
   - **Valeur attendue** :valeur attendue lorsque Security Center analyse la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle.
   - **Valeur réelle** : valeur retournée après analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle.
   - **Opération de la règle** : opération de règle utilisée par Security Center lors de l’analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle.

4. Sur la partie supérieure de la fenêtre de vue détaillée, sélectionnez **Recherche**.  
  Recherche ouvre la liste des espaces de travail ayant des machines virtuelles et des ordinateurs avec l’incohérence des configurations de sécurité sélectionnée. Ce panneau de sélection d’espaces de travail s’affiche uniquement si la règle sélectionnée s’applique à plusieurs machines virtuelles connectées à différents espaces de travail.

   ![Espaces de travail répertoriés][4]

5. Sélectionnez un espace de travail.  
  Une requête de recherche Log Analytics filtrée sur l’espace de travail avec l’incohérence des configurations de sécurité s’ouvre.

   ![Espace de travail avec vulnérabilité de système d’exploitation][5]

6. Sélectionnez un ordinateur dans la liste.  
  Un autre résultat de recherche s’ouvre avec les informations filtrées uniquement pour cet ordinateur.

   ![Informations détaillées sur l’ordinateur sélectionné][6]

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment implémenter la recommandation de Security Center « Corriger les configurations de sécurité ». Pour savoir comment personnaliser les évaluations de configuration de sécurité, consultez la section [Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center (version préliminaire)](security-center-customize-os-security-config.md).

Consultez la [liste des règles de configuration recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations spécifiques surveillées. Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques aux règles de configuration. Consultez le site [CCE](https://nvd.nist.gov/cce/index.cfm) pour plus d’informations.

Pour plus d’informations sur Security Center, consultez les ressources suivantes :

* Pour une liste des machines virtuelles Windows et Linux prises en charge, consultez [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md). 
* Pour découvrir comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure, consultez [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md). 
* Pour découvrir la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure, consultez la section [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md). 
* Pour découvrir comment surveiller l’intégrité de vos ressources Azure, consultez la section [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). 
* Pour découvrir comment gérer et résoudre les alertes de sécurité, consultez la section [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).
* Pour découvrir comment surveiller l’état d’intégrité de vos solutions de partenaire, consultez la section [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md).
* Pour obtenir des réponses aux questions concernant l’utilisation de ce service, consultez la section [FAQ Azure Security Center](security-center-faq.md).
* Pour accéder à des billets de blog sur la sécurité et la conformité Azure, consultez [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
