---
title: Protéger les données personnelles avec Azure Security Center | Microsoft Docs
description: utiliser le centre de sécurité pour protéger des données personnelles. Ceci peut être utilisé pour se conformer au Règlement général sur la protection des données (RGPD)
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 118752a030a1c300306aa3e05571bb9b624a2c20
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Protéger les données personnelles contre les violations et attaques : Azure Security Center

Cet article va vous aider à comprendre comment utiliser Azure Security Center pour protéger les données personnelles contre les violations et attaques. Les informations incluses dans cette rubrique vous aideront à être conforme au Règlement général sur la protection des données (RGPD).

## <a name="scenario"></a>Scénario 

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni.

La compagnie utilise Microsoft Azure pour stocker les données d’entreprise dans le cloud. Ces dernières incluent des informations d’identification personnelle telles que les noms, les adresses, les numéros de téléphone et les informations de carte de crédit. Des informations de ressources humaines sont également inclues comme :

- Adresses
- Numéros de téléphone
- Numéros d’identification fiscale
- Informations médicales

La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense. Les employés de la compagnie accèdent au réseau à partir de ses agences distantes et les agents de voyage répartis dans le monde entier ont accès à certaines ressources de l’entreprise.
Les données personnelles transitent sur le réseau entre ces emplacements et le centre de données Microsoft.

## <a name="problem-statement"></a>Définition du problème

La compagnie s’inquiète de la menace que constituent les attaques sur ses ressources Azure. Elle souhaite empêcher l’exposition des données personnelles de ses clients et employés à des personnes non autorisées. Elle a besoin de conseils en matière de prévention et de réponse/correction, ainsi que d’un moyen efficace de surveiller en continu la sécurité de ses ressources cloud.
Elle nécessite une solide ligne de défense contre les attaques complexes et organisées d’aujourd’hui.

## <a name="company-goal"></a>Objectif de l’entreprise

Un des objectifs de la compagnie consiste à assurer la confidentialité des données personnelles des clients et employés en les protégeant contre les menaces. Un de ses objectifs vise à répondre immédiatement aux signes de violation pour en atténuer l’impact. Elle nécessite un moyen d’évaluer l’état actuel de la sécurité, d’identifier les configurations vulnérables et de les corriger.

## <a name="solutions"></a>solutions

Microsoft Azure Security Center (ASC) fournit une solution de surveillance de sécurité et de gestion de stratégie intégrée. Il offre des fonctionnalités de prévention, de détection et de réponse aux menaces efficaces et faciles à utiliser.

### <a name="prevention"></a>Prévention

ASC vous permet d’empêcher les violations en vous permettant de définir des stratégies de sécurité, de fournir un accès juste-à-temps et de mettre en œuvre des recommandations de sécurité.

Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement spécifique. L’accès juste-à-temps permet de bloquer le trafic entrant sur vos machines virtuelles Azure, ce qui réduit l’exposition aux attaques. Des recommandations de sécurité sont créées par ASC après analyse de l’état de sécurité de vos ressources Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Comment définir des stratégies de sécurité dans ASC ?

Vous pouvez configurer des stratégies de sécurité pour chaque abonnement. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou de collaborateur pour l’abonnement concerné. Dans le portail Azure, procédez comme suit :

1. Sélectionnez **Stratégie** dans le tableau de bord ASC.

2. Sélectionnez l’abonnement sur lequel activer la stratégie.

3. Choisissez **Stratégie de prévention** pour configurer des stratégies par abonnement. **Collecter les données des machines virtuelles** doit être défini sur **Activé.**

4. Dans les options **Stratégie de prévention**, sélectionnez **Activé** pour activer les recommandations de sécurité pertinentes pour l’abonnement.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Pour obtenir d’autres instructions et une explication de chacune de ces recommandations de stratégie pouvant être activées, consultez [Définir des stratégies de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Comment configurer l’accès juste-à-temps (JIT) ?

Quand la fonctionnalité JIT est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant sera verrouillé. Pour utiliser l’accès JIT, procédez comme suit :

1. Sélectionnez la **vignette Accès Juste à temps à la machine virtuelle** dans le panneau ASC.

2. Sélectionnez l’onglet **Recommandé**.

3. Sous **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez activer. Une coche est alors placée en regard des machines virtuelles concernées. 
4. Sélectionnez **Activer JIT** sur les machines virtuelles.
5. Sélectionnez **Enregistrer**.

Ensuite, vous pouvez voir les ports par défaut qu’ASC recommande d’activer pour JIT. Vous pouvez également ajouter et configurer un nouveau port sur lequel activer la solution Juste-à-temps. La vignette **Accès Juste à temps à la machine virtuelle** de Security Center indique le nombre de machines virtuelles configurées pour l’accès JIT. Il montre également le nombre de demandes d’accès approuvées pendant la semaine passée.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Pour obtenir des instructions sur la procédure à suivre, et des informations supplémentaires sur l’accès Juste-à-temps, consultez [Gérer l’accès Juste à temps à la machine virtuelle.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Comment implémenter les recommandations de sécurité ASC ?

Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires. 
1. Sélectionnez la vignette **Recommandations** dans le tableau de bord ASC.

2. Prenez connaissance des recommandations affichées sous forme de tableau où chaque ligne correspond à une recommandation.

3. Pour filtrer les recommandations, sélectionnez **Filtre** et sélectionnez les valeurs de gravité et d’état à afficher.

4. Pour masquer une recommandation non applicable, vous pouvez cliquer avec le bouton droit et sélectionner **Faire disparaître.**

5. Évaluez les recommandations à appliquer en premier.

6. Appliquez les recommandations dans leur ordre de priorité.

Pour obtenir la liste des recommandations possibles et des procédures pas à pas permettant de les appliquer, consultez [Gestion des recommandations de sécurité dans Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Détection et réponse

Détection et réponse vont de pair, car vous avez besoin de répondre aussi rapidement que possible une fois qu’une menace est détectée.
La détection des menaces par ASC fonctionne en collectant automatiquement les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées. ASC peut donc rapidement mettre à jour ses algorithmes de détection, puisque les pirates sont à l’origine d’attaques innovantes de plus en plus sophistiquées. Pour plus d’informations sur le fonctionnement de la détection des menaces par ASC, consultez [Fonctionnalités de détection d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Comment gérer les alertes de sécurité et y répondre ?

La liste hiérarchisée des alertes de sécurité s’affiche dans Security Center, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque. Security Center inclut également des recommandations sur la manière de corriger les problèmes liés à une attaque. Pour gérer vos alertes de sécurité, procédez comme suit :

1. Sélectionnez la vignette **Alertes de sécurité** dans le tableau de bord ASC. Celle-ci permet d’afficher les détails de chaque alerte.

2. Pour filtrer les alertes en fonction de leur date, état et gravité, sélectionnez **Filtre**, puis sélectionnez les valeurs à afficher.

3. Pour répondre à une alerte, sélectionnez-la, puis passez en revue les informations et sélectionnez la ressource attaquée.

4. Dans le champ **Description** figurent des détails, notamment la correction recommandée.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Pour obtenir des instructions sur la réponse à donner à des alertes de sécurité, consultez [Gestion et résolution des alertes de sécurité dans Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

Pour obtenir de l’aide dans l’examen des alertes de sécurité, la compagnie peut intégrer les alertes ASC à sa propre solution SIEM, à l’aide de l’[intégration des journaux Azure](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Comment gérer les incidents de sécurité ?

Dans ASC, un incident de sécurité est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de chaîne de destruction. Un incident affiche la liste des alertes associées, qui vous permet d’en savoir plus sur chaque occurrence. Les incidents apparaissent dans la vignette et le panneau Alertes de sécurité.

Pour vérifier et gérer des incidents de sécurité, procédez comme suit :

1. Sélectionnez la vignette **Alertes de sécurité**. Si un incident de sécurité est détecté, il apparaît sous le graphe des alertes de sécurité. Son icône diffère de celles des autres alertes.

2. Sélectionnez l’incident pour afficher d’autres détails sur cet incident de sécurité. Les détails supplémentaires incluent sa description complète, sa gravité, son état actuel, la ressource attaquée, les étapes de correction de l’incident et les alertes qui étaient incluses dans cet incident.

Vous pouvez filtrer pour afficher **uniquement les incidents**, **uniquement les alertes** ou **les deux**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Comment accéder au rapport d’informations sur les menaces ?

ASC analyse les informations provenant de plusieurs sources pour identifier les menaces. Pour aider les équipes de réponse aux incidents à examiner et à corriger les menaces, Azure Security Center inclut un rapport qui contient des informations sur la menace détectée.

Azure Security Center propose trois types de rapports sur les menaces, qui peuvent varier en fonction de l’attaque.
Les rapports disponibles sont les suivants :

- Rapport sur le groupe d’activités : fournit des informations détaillées sur les attaquants, leurs objectifs et leurs tactiques.

- Rapport sur la campagne : se concentre sur les détails des campagnes d’attaque spécifiques.

- Rapport de synthèse sur la menace : couvre tous les éléments des deux rapports précédents.

Ce type d’information est très utile pendant le processus de réponse aux incidents, au cours duquel des examens sont effectués en continu afin de comprendre la source de l’attaque, les motivations de l’attaquant et les solutions d’atténuation de ce problème à l’avenir.

1. Pour accéder au rapport d’informations sur les menaces, procédez comme suit :

2. Sélectionnez la vignette **Alertes de sécurité** dans le tableau de bord ASC.

3. Sélectionnez l’alerte de sécurité pour laquelle vous voulez obtenir plus d’informations.

4. Dans le champ **Rapports**, cliquez sur le lien vers le rapport d’informations sur les menaces.

5. Le fichier PDF, que vous pouvez télécharger, s’ouvre.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Pour plus d’informations sur le rapport d’informations sur les menaces d’ASC, consultez [Rapport d’informations sur les menaces d’Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Évaluation

Pour vous aider à tester, estimer et évaluer l’état de votre sécurité, ASC propose une des vulnérabilités intégrée aux agents cloud Qualys, dans le cadre de son composant de recommandations pour les machines virtuelles.

L’agent Qualys transmet des données de vulnérabilité à la plateforme de gestion Qualys, laquelle renvoie alors des données de surveillance des vulnérabilités et de l’intégrité à ASC. La recommandation d’ajouter une solution d’évaluation des vulnérabilités s’affiche dans le panneau **Recommandations** du tableau de bord ASC.

Une fois la solution d’évaluation des vulnérabilités installée sur la machine virtuelle cible, Azure Security Center analyse cette dernière pour détecter et identifier les vulnérabilités des applications et du système. Les problèmes détectés sont visibles sous l’option **Virtual Machines Recommendations (Recommandations relatives aux machines virtuelles)**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Comment mettre en œuvre une solution d’évaluation des vulnérabilités ? 

Si une machine virtuelle n’a pas déjà une solution d’évaluation des vulnérabilités intégrée déployée, Security Center recommande d’en installer une.

1. Dans le tableau de bord ASC, dans le panneau **Recommandations**, sélectionnez **Ajouter une solution d’évaluation des vulnérabilités.**

2. Sélectionnez les machines virtuelles sur lesquelles vous voulez installer la solution d’évaluation des vulnérabilités.

3. Cliquez sur **Installer sur [nombre] machines virtuelles.**

4. Sélectionnez une solution de partenaire dans Place de marché Microsoft Azure ou sous **Utiliser une solution existante,** sélectionnez **Qualys.**

5. Vous pouvez activer ou désactiver les paramètres de mise à jour automatique dans le panneau **Solutions de partenaire**.

Pour obtenir des instructions sur la façon d’implémenter une solution d’évaluation des vulnérabilités, consultez [Évaluation des vulnérabilités dans Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Étapes suivantes

- [Guide de démarrage rapide d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Présentation d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Intégration des alertes Azure Security Center aux journaux Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Renforcer Azure Security Center avec l’évaluation des vulnérabilités intégrée](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
