---
title: Connexion de Microsoft Advanced Threat Analytics à Azure Security Center | Microsoft Docs
description: Découvrez comment Azure Security Center s’intègre avec Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: 02e14315018f6586644eb560894785e6b6eb022e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Connexion de Microsoft Advanced Threat Analytics à Azure Security Center
Ce document vous aide à configurer l’intégration entre Microsoft Advanced Threat Analytics et Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Pourquoi ajouter des données d’Advanced Threat Analytics ?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) est une plateforme locale qui aide à détecter les comportements suspects d’utilisateurs. Une fois connecté, vous pouvez voir dans Security Center les actions suspectes détectées par ATA. Grâce à cette intégration, vous pouvez afficher, mettre en corrélation et examiner toutes les alertes de sécurité liées à vos charges de travail cloud hybrides dans Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Comment configurer cette intégration ?
En supposant qu’ATA est déjà installé et fonctionne correctement localement, procédez comme suit pour configurer cette intégration :

1. Connectez-vous à l’ATA Center, puis accédez au portail ATA.
2. Cliquez sur **Serveur Syslog** dans le volet gauche.

    ![Serveur syslog](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. Dans le champ **Point de terminaison du serveur Syslog**, tapez 127.0.0.7 (il doit s’agir de cette adresse), puis tapez 5114 sur le port (recommandé). Le numéro de port étant une recommandation, tout port unique devrait fonctionner. Laissez toutes les autres options telles quelles, puis cliquez sur **Enregistrer**.
4. Cliquez sur **Notifications** dans le volet gauche, puis activez toutes les notifications Syslog (recommandé), comme illustré dans l’image suivante :

    ![Notifications](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Cliquez sur **Enregistrer**.
6. Ouvrez le tableau de bord **Security Center**.
7. Dans le volet gauche, cliquez sur **Solutions de sécurité**.
8. Sous **Advanced Threat Analytics**, cliquez sur **Ajouter**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Allez à la dernière étape, puis cliquez sur **Télécharger l’agent**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. Dans la page **Ajouter un nouvel ordinateur non-Azure**, sélectionnez l’espace de travail.

    ![Non-Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. Dans la page **Agent direct**, téléchargez l’agent Windows approprié, puis notez l’**ID d’espace de travail** et la **Clé primaire**.

    ![Agent direct](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Installez cet agent dans l’ATA Center. Pendant l’installation, veillez à activer l’option **Connecter l’agent à Azure Log Analytique (OMS)** et à fournir l’*ID de l’espace de travail* et la *clé primaire* quand vous y êtes invité.


Lorsque vous avez fini l’installation, l’intégration est terminée et vous pouvez voir les nouvelles alertes envoyées à partir d’ATA au Security Center dans les résultats de **Recherche**. La solution apparaît dans la page **Solutions de sécurité** sous **Solutions connectées**. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert comment connecter Microsoft ATA à Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Connecting Azure Active Directory Identity Protection to Azure Security Center](security-center-aadip-integration.md) (Connexion d’Azure Active Directory Identity Protection à Azure Security Center)
* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment les données sont gérées et protégées dans Security Center.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.


