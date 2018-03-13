---
title: "Intégration des journaux Azure aux journaux d’audit Azure Active Directory | Microsoft Docs"
description: "Découvrez comment installer le service d’intégration des journaux Azure et intégrer les journaux à partir des journaux d’audit Azure."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/16/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0f45f43a0296a7d90a68b0526f805ea50a1ce6c6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Intégrer des journaux d’audit Azure Active Directory

Les événements d’audit Azure Active Directory (Azure AD) vous aident à identifier les actions privilégiées survenues dans Azure Active Directory. Vous pouvez voir les types d’événements que vous pouvez suivre en examinant [les événements du rapport d’audit Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).


> [!NOTE]
> Avant d’effectuer les étapes décrites dans cet article, vous devez lire l’article [Bien démarrer](security-azure-log-integration-get-started.md) et effectuez les étapes qui y sont citées.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Procédure d’intégration des journaux d’audit Azure Active Directory

1. Ouvrez l’invite de commandes et exécutez la commande suivante :

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Exécutez cette commande : 
 
   ``azlog createazureid``

   Cette commande vous invite à entrer votre nom d’utilisateur Azure. La commande crée ensuite un principal du service Azure Active Directory dans les locataires Azure AD qui hébergent les abonnements Azure dans lesquels l’utilisateur connecté est un administrateur, un coadministrateur ou un propriétaire. La commande échoue si l’utilisateur connecté est seulement un utilisateur invité dans le locataire Azure AD. L’authentification à Azure s’effectue avec Azure AD. La création d’un principal du service pour l’intégration de journaux Azure crée l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.

3. Exécutez la commande suivante pour fournir votre ID de locataire. Vous devez être membre du rôle administrateur de locataire pour exécuter la commande.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemple :

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Vérifiez les dossiers suivants pour confirmer que les fichiers JSON de journaux d’audit Azure Active Directory y sont créés :

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

La vidéo suivante montre les étapes décrites dans cet article :

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Pour obtenir des instructions précises sur l’ajout des informations de vos fichiers JSON dans votre système de gestion des événements et des informations de sécurité (SIEM), contactez votre fournisseur SIEM.

L’aide de la Communauté est disponible via le [forum MSDN d’intégration des journaux Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ce forum permet aux membres de la communauté de l’intégration des journaux Azure de s’entraider, grâce à des questions, des réponses, des conseils et des astuces. En outre, l’équipe d’intégration des journaux Azure supervise ce forum et apporte son aide quand cela est possible.

Vous pouvez également ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour ce faire, sélectionnez **Intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’intégration des journaux Azure, consultez :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : cette page du Centre de téléchargement donne plus d’informations, la configuration système requise et les instructions d’installation pour l’intégration des journaux Azure.
* [Introduction à l’intégration de journaux Azure](security-azure-log-integration-overview.md) : cet article présente l’intégration des journaux Azure, ses principales fonctionnalités et son fonctionnement.
* [Forum aux questions sur l’intégration des journaux Azure](security-azure-log-integration-faq.md) : cet article répond à des questions sur l’intégration des journaux Azure.
* [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.
