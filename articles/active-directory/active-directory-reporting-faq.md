---
title: FAQ sur les rapports Azure Active Directory | Microsoft Docs
description: FAQ sur les rapports Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e1c1b8bdf94104c0047e367f67a29d557fcc8df9
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-reporting-faq"></a>FAQ sur les rapports Azure Active Directory

Cet article répond aux questions fréquemment posées sur les rapports Azure Active Directory (Azure AD). Pour plus d’informations, consultez la page [Génération de rapports Azure Active Directory](active-directory-reporting-azure-portal.md). 

**Q : J’utilise les API de point de terminaison https://graph.windows.net/&lt;nom-locataire&gt;/reports/ pour tirer (pull) par programmation les rapports d’audit Azure AD et les rapports d’utilisation des applications intégrées dans nos systèmes de création de rapports. Que dois-je utiliser à présent ?**

**R :** Consultez notre [documentation de référence sur les API](https://developer.microsoft.com/graph/) pour savoir comment utiliser les nouvelles API dans le but d’accéder aux [rapports d’activité](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Ce point de terminaison comporte deux rapports (un d’audit et un autre sur les connexions) qui fournissent toutes les données dont vous disposiez dans l’ancien point de terminaison d’API. Ce nouveau point de terminaison comporte également un rapport sur les connexions, relatif à la licence Azure AD Premium, que vous pouvez utiliser pour obtenir des informations sur l’utilisation des applications, l’utilisation des appareils et les connexions utilisateur.


--- 

**Q : J’utilise les API de point de terminaison https://graph.windows.net/&lt;nom-locataire&gt;/reports/ pour tirer (pull) par programmation les rapports de sécurité Azure AD (concernant certains types de détections, telles que les fuites d’informations d’identification ou les connexions à partir d’adresses IP anonymes) dans notre système de création de rapports. Que dois-je utiliser à présent ?**

**R :** Vous pouvez utiliser [l’API Identity Protection relative aux événements à risque](active-directory-identityprotection-graph-getting-started.md) pour accéder aux détections de sécurité via Microsoft Graph. Ce nouveau format offre davantage de flexibilité dans la façon dont vous pouvez interroger les données, c’est-à-dire avec un filtrage avancé, une sélection des champs, etc. De plus, il rassemble les événements à risque sous un même type pour faciliter l’intégration aux serveurs SIEM et autres outils de collecte de données. Étant donné que les données ont différents formats, vous ne pouvez pas remplacer vos anciennes requêtes par une nouvelle requête. Toutefois, [la nouvelle API utilise Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), qui est désormais la plateforme standard de Microsoft pour les API telles qu’Office 365 et Azure AD. Le travail nécessaire peut donc développer vos investissements MS Graph actuels ou vous aider à démarrer la transition vers cette nouvelle plateforme standard.

--- 

**Q : Quelle est la rétention des données des journaux d’activité (audit et connexions) sur le Portail Azure ?** 

**R :** Les clients de la version gratuite bénéficient de 7 jours de données. Toutefois, en passant à une licence Azure AD Premium 1 ou Premium 2, ils peuvent accéder à 30 jours de données. Pour plus d’informations sur la rétention des rapports, consultez [Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md).

--- 

**Q : Au bout de combien de temps peut-on voir les données d’activité une fois la tâche terminée ?**

**R :** Les journaux d’activité d’audit ont une latence comprise entre 15 minutes et 1 heure. Pour certains enregistrements, la mise à jour des journaux d’activité de connexion peut prendre de 15 minutes à 2 heures.

---

**Q : Faut-il être administrateur général pour voir les activités de connexion sur le portail Azure ou pour obtenir des données par le biais de l’API ?**

**R :** Non. Vous devez être **Lecteur Sécurité**, **Administrateur de sécurité** ou **Administrateur général** pour afficher les données des rapports dans le portail Azure ou par le biais de l’API.

---

**Q : Peut-on obtenir des informations du journal d’activité d’Office 365 avec le Portail Azure ?**

**R :** Bien que les journaux d’activité d’Office 365 et d’Azure AD partagent une grande partie des ressources de l’annuaire, vous devez accéder au Centre d’administration Office 365 pour obtenir une vue complète des journaux d’activité d’Office 365.

---


**Q : Quelles API faut-il utiliser pour obtenir des informations sur les journaux d’activité d’Office 365 ?**

**R :** Utilisez les API Gestion d’Office 365 pour accéder aux [journaux d’activité d’Office 365 avec une API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**Q : Combien d’enregistrements peut-on télécharger à partir du Portail Azure ?**

**R :** Vous pouvez télécharger jusqu’à 120 000 enregistrements à partir du Portail Azure. Les enregistrements sont triés par *date* et, par défaut, vous obtenez les 120 000 enregistrements les plus récents. 

---

**Q : Combien d’enregistrements peut-on interroger avec l’API d’activités ?**

**R :** Vous pouvez interroger jusqu’à 1 million d’enregistrements (si vous n’utilisez pas l’opérateur top, qui trie les enregistrements par date). Si vous utilisez l’opérateur « top », vous pouvez interroger jusqu’à 500 000 enregistrements. Vous trouverez [ici](active-directory-reporting-api-getting-started.md) des exemples de requêtes sur l’utilisation de l’API.

---

**Q : Comment obtenir une licence Premium ?**

**R :** Consultez [Prise en main d’Azure Active Directory Premium](active-directory-get-started-premium.md) pour connaître la réponse à cette question.

---

**Q : Au bout de combien de temps peut-on consulter les données d’activité après avoir obtenu une licence Premium ?**

**R :** Si vous avez déjà des données d’activité sous une licence gratuite, vous pouvez voir les mêmes données. Si vous n’avez pas de données, cela prendra un ou deux jours.

---

**Q : Peut-on voir les données du mois dernier après avoir obtenu une licence Azure AD Premium ?**

**R :** Si vous êtes récemment passé à une version Premium (y compris une version d’évaluation), vous pouvez voir les données jusqu’à 7 jours dans un premier temps. Au fur et à mesure de l’accumulation des données, vous verrez jusqu’à 30 jours.

---

**Q : ldentity Protection m’informe d’un événement à risque, mais je ne vois pas à quelle connexion il correspond parmi toutes les connexions répertoriées. Est-ce normal ?**

**R :** Oui, Identity Protection évalue les risques pour tous les flux d’authentification, qu’ils soient interactifs ou non. Toutefois, seules les connexions interactives sont répertoriées.

---

**Q : Comment puis-je télécharger le rapport « Utilisateurs avec indicateur de risque » dans le portail Azure ?**

**R :** L’option permettant de télécharger le rapport *Utilisateurs avec indicateur de risque* sera bientôt disponible.

---

**Q : Comment puis-je savoir pourquoi une connexion ou un utilisateur ont été signalés comme étant « à risque » dans le portail Azure ?**

**R :** Les utilisateurs de l’édition Premium peuvent en savoir plus sur les événements à risque sous-jacents en cliquant sur l’utilisateur répertorié sous « Utilisateurs avec indicateur de risque » ou en cliquant sur « Connexions à risque ». Les utilisateurs de l’édition gratuite et de l’édition De base peuvent voir les utilisateurs et les connexions à risque, mais pas les événements à risque sous-jacents.

---

**Q : Comment les adresses IP sont-elles calculées dans le rapport des connexions et des connexions à risque ?**

**R :** les adresses IP sont émises de manière à ce qu’il n’existe aucune connexion définitive entre une adresse IP et l’endroit où se trouve physiquement l’ordinateur avec cette adresse. Ceci est compliqué par des facteurs tels que les fournisseurs mobiles et les VPN qui émettent des adresses IP à partir de pools centraux souvent très éloignés de l’endroit où l’appareil client est réellement utilisé. Étant donné ce qui précède, la conversion de l’adresse IP en un emplacement physique constitue la meilleure solution pour les suivis, les données de registre, les recherches inversées et d’autres informations. 

---

**Q : Que signifie l’événement à risque « Connexion avec un risque supplémentaire détectée » ?**

**R :** Pour vous donner une idée de toutes les connexions à risque dans votre environnement, nous montrons l’événement à risque « Connexion avec un risque supplémentaire détectée » pour les connexions considérées comme présentant un risque en raison de détections propres aux abonnés d’Azure Active Directory Identity Protection.

---
