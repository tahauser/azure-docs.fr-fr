---
title: Playbook de sécurité dans Azure Security Center | Microsoft Docs
description: Ce document vous aide à utiliser des playbooks de sécurité dans Azure Security Center pour automatiser votre réponse aux incidents de sécurité.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 76f2666df76b802c175eadc38d2ac4100799ceae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Playbook de sécurité dans Azure Security Center (préversion)
Ce document vous aide à utiliser des playbooks de sécurité dans Azure Security Center pour gérer votre réponse aux incidents de sécurité.

## <a name="what-is-security-playbook-in-security-center"></a>Qu’est-ce qu’un playbook de sécurité dans Azure Security Center ?
Un playbook de sécurité est un ensemble de procédures exécutable depuis Security Center lorsqu’un playbook spécifique est déclenché depuis une alerte sélectionnée. Ce playbook peut aider à automatiser et orchestrer votre réponse à une alerte de sécurité spécifique détectée par Azure Security Center. Les playbooks de sécurité dans Azure Security Center sont basés sur [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), ce qui signifie que vous pouvez utiliser les modèles qui sont fournis sous la catégorie de sécurité dans les modèles Logic Apps, les modifier selon vos besoins, ou créer des playbooks à l’aide du [flux de travail Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) en utilisant Azure Security Center en tant que déclencheur. 

> [!NOTE]
> Le manuel tire parti d’Azure Logic Apps. Pour cette raison, des frais sont applicables. Consultez la page sur la tarification [d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) pour en savoir plus. 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Comment créer un playbook de sécurité depuis Azure Security Center ?
Pour créer un playbook de sécurité depuis Azure Security Center, procédez comme suit :

1.  Ouvrez le tableau de bord **Security Center**.
2.  Sous **Automation et orchestration**, dans le volet gauche, cliquez sur **Playbooks (préversion)**.

    ![Application logique](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. Dans la page **Security Center - Playbooks (préversion)**, cliquez sur le bouton **Ajouter**.

    ![Créer une application logique](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Dans la page **Créer une application logique**, saisissez les informations requises pour créer votre application logique et cliquez sur le bouton **Créer**. Une fois la création terminée, le nouveau playbook est affiché dans la liste. Si elle n’apparaît pas, cliquez sur le bouton **Actualiser**. Lorsqu’elle s’affiche, cliquez dessus pour commencer la modification du playbook.

    ![Créer une application logique](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Le **Concepteur d’application logique** s’affiche. Cliquez sur **Application logique vide** pour créer un playbook. Vous pouvez également sélectionner **Sécurité** sous les catégories et utiliser un des modèles.
    
    ![Concepteur d’application logique](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. Dans le champ **Rechercher dans l’ensemble des connecteurs et des déclencheurs**, saisissez *Azure Security Center*, et choisissez **Quand une réponse à une alerte Azure Security Center est déclenchée**.

    ![Déclencheur](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Maintenant, vous pouvez définir l’action effectuée lorsque vous déclenchez le playbook. Vous pouvez ajouter une action, une condition logique, des boucles ou des conditions de casse de commutateurs.

    ![Concepteur d’application logique](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>Comment exécuter un playbook de sécurité dans Security Center ?

Si vous voulez exécuter un playbook de sécurité dans Security Center lorsque vous souhaitez gérer l’orchestration, obtenir d’informations supplémentaires à partir d’autres services ou lorsque vous voulez résoudre un problème. Pour accéder au playbook, procédez comme suit :

1.  Ouvrez le tableau de bord **Security Center**.
2.  Dans le volet gauche, sous **Détection de menaces**, cliquez sur **Incidents de sécurité et alertes**.

    ![Alertes](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Cliquez sur l’alerte que vous souhaitez examiner.
4.  En haut de la page de l’alerte, cliquez sur le bouton **Exécuter des playbooks**.

    ![Exécuter des playbooks](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Dans la page des playbooks, sélectionnez celui que vous souhaitez exécuter, puis cliquez sur le bouton **Exécuter**. Si vous souhaitez voir le playbook avant le déclenchement de, vous pouvez cliquer dessus, et le concepteur s’ouvre.

    ![Playbooks](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Historique

Après avoir exécuté le playbook, vous pouvez également accéder aux exécutions précédentes et aux étapes qui contiennent plus d’informations sur l’état des playbooks précédemment exécutés. L’historique est personnalisé en fonction de l’alerte. Cela indique que l’historique du playbook que vous voyez apparaître sur cette page est lié à l’alerte qui l’a déclenché. 

![Historique](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Pour en savoir plus sur l’exécution d’un playbook spécifique, cliquez sur ce dernier. La page d’exécution de l’application logique s’affiche avec le workflow dans son ensemble.

![Détails](./media/security-center-playbooks/security-center-playbooks-fig14.png)

Dans ce flux de travail, vous pouvez voir l’heure à laquelle chaque tâche y a accédé pour être exécutée, et vous pouvez développer chaque tâche pour voir le résultat de l’opération. 

### <a name="changing-an-existing-playbook"></a>Modification d’un playbook existant

Vous pouvez modifier un playbook existant dans Security Center pour ajouter une action ou des conditions. Pour ce faire, vous devez simplement cliquer sur le nom du playbook à modifier, dans l’onglet Playbooks. Le Concepteur d’application logique s’ouvre.

> [!NOTE]
> Pour en savoir plus sur la création de votre playbook à l’aide d’Azure Logic App, consultez la section [Créer votre premier flux de travail d’application logique pour automatiser les processus entre les applications cloud et les services cloud](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger).


## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez vu comment utiliser des playbooks dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

