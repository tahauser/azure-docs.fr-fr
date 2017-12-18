---
title: Utilisation du pack de contenu Power BI Azure Active Directory | Microsoft Docs
description: "Découvrir comment utiliser le pack de contenu Power BI Azure Active Directory"
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: 
ms.topic: get-started-article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 503b3f4c576382d8ce965d1f90aadda32c819a0b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Utilisation du pack de contenu Power BI Azure Active Directory

Comprendre comment vos utilisateurs adoptent et utilisent les fonctionnalités d’Azure Active Directory est essentiel pour vous en tant qu’administrateur informatique. Cela vous permet de planifier votre infrastructure informatique et votre communication pour augmenter l’utilisation et tirer le meilleur parti des fonctionnalités d’AAD. Le pack de contenu Power BI pour Azure Active Directory vous donne la possibilité d’analyser vos données pour comprendre comment vous pouvez les utiliser afin de recueillir des informations plus détaillées sur ce qui se passe avec Azure Active Directory pour les différentes fonctionnalités sur lesquelles vous comptez fortement.  Avec l’intégration des API Azure Active Directory API à Power BI, vous pouvez facilement télécharger les packs de contenu prédéfinis et obtenir des informations détaillées sur toutes les activités d’Azure Active Directory à l’aide de l’expérience de visualisation enrichie qu’offre Power BI. Vous pouvez créer votre propre tableau de bord et le partager facilement avec d’autres personnes de votre organisation. 

Cette rubrique fournit des instructions détaillées sur la façon d’installer et d’utiliser le pack de contenu dans votre environnement.

## <a name="installation"></a>Installation  

**Pour installer le pack de contenu Power BI :**

1. Connectez-vous à [Power BI](https://app.powerbi.com/groups/me/getdata/services) avec votre compte Power BI (Il s’agit du même compte que votre compte O365 ou Azure AD).

2. Sélectionnez **Obtenir des données** au bas du volet de navigation gauche.

    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. Dans la zone **Services** , cliquez sur **Obtenir**.
   
    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Recherchez **Azure Active Directory**.

    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Lorsque vous y êtes invité, tapez votre ID client Azure AD, puis cliquez sur **Suivant**.

    > [!TIP] 
    > Une façon rapide d’obtenir l’ID client pour votre client Office 365 / Azure AD consiste à vous connecter au portail Azure AD, descendre dans la hiérarchie, et copier **l’ID Directory** à partir de la page [**Propriétés**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Cliquez sur **Se connecter**. 
 
    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Entrez votre nom d’utilisateur et un mot de passe, puis cliquez sur **Se connecter**.
 
    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  Dans la boîte de dialogue de consentement de l’application, cliquez sur **Accepter**.
 
9.  Une fois le tableau de bord des journaux Azure Active Directory créé, cliquez dessus.
 
    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>Que puis-je faire avec ce pack de contenu ?

Avant de passer en revue toutes les possibilités qu’offre ce pack de contenu, voici un aperçu rapide des différents rapports qu’il contient. Les données des rapports portent sur les **30 derniers jours**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Rapports inclus dans cette version du pack de contenu de journaux Azure Active Directory

**Rapport de tendance et d’utilisation des applications** : pour obtenir des informations sur les applications utilisées dans votre organisation et savoir quelles sont celles les plus utilisées et à quel moment. Vous pouvez utiliser ce rapport pour recueillir des informations sur la façon dont une application récemment déployée dans votre organisation est utilisée ou savoir quelles sont les applications populaires. Cela permet d’améliorer l’utilisation si vous voyez que l’application n’est pas utilisée.

**Connexions par emplacement et utilisateurs** : pour connaître toutes les connexions effectuées à l’aide d’Azure Identity et fournir des informations sur l’identité des utilisateurs. Cette option permet d’examiner de manière plus approfondie les connexions individuelles et de répondre aux questions telles que :

- À partir de quel endroit l’utilisateur s’est-il connecté ?
- Quel utilisateur se connecte le plus et d’où se connecte-t’il ? 
- La connexion a-t-elle réussie ?  
 
Vous pouvez parcourir les informations en cliquant sur une date ou un emplacement spécifique.

**Utilisateurs uniques par application** : pour obtenir une vue de l’ensemble des utilisateurs uniques à l’aide d’une application donnée. Cela comprend uniquement les utilisateurs qui se sont connectés « *avec succès* » à une application.

**Connexions d’appareil** : pour obtenir une vue du type de système d’exploitation et de navigateurs utilisés par les utilisateurs de votre organisation avec des informations détaillées sur les utilisateurs, notamment les suivantes :

- User Name
- Adresse IP
- Lieu 
- État de la connexion 

Ce rapport vous permet de comprendre les différents profils d’appareils utilisés au sein de votre organisation et de déterminer les stratégies d’appareils en fonction de ce qui est utilisé

**Synthèse SSPR** : pour comprendre le fonctionnement de la réinitialisation des mots de passe de votre organisation. Obtenez un aperçu du nombre de tentatives de réinitialisations de mot de passe via l’outil SSPR et du nombre de celles ayant abouti. Approfondissez les échecs de réinitialisation de mot de passe à l’aide de la synthèse SSPR pour comprendre les raisons pour lesquelles certains échecs sont survenus. Ce rapport fournit une meilleure compréhension de la manière d’utiliser l’outil SSPR au sein de votre organisation afin de prendre les bonnes décisions.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personnalisation du pack de contenu d’activité Azure AD

**Modifier la visualisation** : vous pouvez modifier une visualisation des rapports en cliquant sur **Modifier le rapport** et sélectionner la visualisation que vous souhaitez.
 
![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Inclure des champs supplémentaires** : vous pouvez ajouter un champ au rapport ou le supprimer en sélectionnant l’élément visuel auquel vous souhaitez ajouter/supprimer le champ. Dans l’exemple ci-dessous, j’ajoute le champ « État de la connexion » à la vue de la table. 
 
![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Épingler des visualisations à votre tableau de bord** : vous pouvez personnaliser votre tableau de bord et inclure vos propres visualisations au rapport et l’épingler sur le tableau de bord. Dans l’exemple ci-dessous, j’ai ajouté un nouveau filtre appelé « État de la connexion » et je l’ai inclus dans le rapport. J’ai également modifié la visualisation pour passer d’un histogramme à un graphique en courbes et peux épingler ce nouvel élément visuel au tableau de bord.

![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Partage de votre tableau de bord** : une fois que vous avez créé le contenu souhaité, vous pouvez partager le tableau de bord avec les utilisateurs de votre organisation. N’oubliez pas qu’une fois le rapport partagé, les utilisateurs peuvent voir les champs que vous avez sélectionnés dans le rapport.
 
![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Planification d’une actualisation quotidienne de votre rapport Power BI

Pour planifier une actualisation quotidienne de votre rapport Power BI, accédez à **Jeux de données > Paramètres > Planifier l’actualisation** et définissez-la comme indiqué ci-dessous.
 
![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Mise à jour vers une version plus récente du pack de contenu

Si vous souhaitez mettre à jour votre pack de contenu pour obtenir une version plus récente :

- Téléchargez le nouveau pack de contenu et configurez-le conformément aux instructions figurant dans cet article.

- Une fois configuré, accédez à **Source de données > Paramètres > Informations d’identification de la source de données** et entrez à nouveau vos informations d’identification comme indiqué ci-dessous

    ![Pack de contenu Power BI Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Dès que la nouvelle version du pack de contenu est en fonctionnement, vous pouvez supprimer l’ancienne version, si nécessaire en supprimant les rapports et les jeux de données sous-jacents associés à ce pack de contenu.

## <a name="still-having-issues"></a>Vous rencontrez toujours des problèmes ? 

Consultez notre [guide de résolution des problèmes](active-directory-reporting-troubleshoot-content-pack.md). Pour obtenir une aide générale sur Power BI, consultez ces [articles d’aide](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble des rapports, consultez [Création de rapports Azure Active Directory](active-directory-reporting-azure-portal.md).
