---
title: "Intégration des journaux Azure avec Security Center | Microsoft Docs"
description: "Découvrez comment utiliser les alertes d’Azure Security Center via l’intégration des journaux"
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
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Recevoir les alertes de Security Center via l’intégration des journaux à Azure

Cet article présente les étapes nécessaires pour activer le service d’intégration des journaux Azure et extraire des informations sur les alertes de sécurité générées par Azure Security Center. Vous devez avoir terminé avec succès les étapes de l’article [Prise en main](security-azure-log-integration-get-started.md) avant de suivre la procédure définie dans cet article.

## <a name="detailed-steps"></a>Procédure détaillée

Les étapes suivantes créent le principal de service nécessaire pour Azure Active Directory et attribuent les autorisations de lecture du principal de service à l’abonnement :
1. Ouvrez l’invite de commandes et rendez-vous dans **c:\Program Files\Microsoft Azure Log Integration**
2. Exécutez la commande ``azlog createazureid``

    Cette commande vous invite à entrer votre nom d’utilisateur Azure. La commande crée ensuite un [principal du Service Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) dans les locataires Azure AD qui hébergent les abonnements Azure dans lesquels l’utilisateur connecté est un administrateur, un coadministrateur ou un propriétaire. La commande échouera si l’utilisateur connecté est seulement un utilisateur invité dans le locataire Azure AD. L’authentification à Azure s’effectue par le biais d’Azure Azure Active Directory (AD). La création d’un principal du service pour l’intégration d’Azlog crée l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.

3. Vous créez ensuite une commande qui affecte les accès en lecture de l’abonnement au principal de service que vous venez de créer. Si vous ne spécifiez pas de SubscriptionID, la commande tente d’affecter le rôle de lecteur du principal du service à tous les abonnements auxquels vous avez accès. </br></br>
``azlog authorize <SubscriptionID>`` </br> par exemple </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Des avertissements peuvent s’afficher si vous exécutez la commande authorize immédiatement après la commande ```createazureid```. Il existe un temps de latence entre la création du compte Azure AD et la disponibilité du compte pour une utilisation. Si vous patientez environ 60 secondes après l’exécution de la commande ```createazureid``` pour exécuter la commande authorize, vous ne devriez pas voir ces avertissements.

4. Vérifiez que les fichiers JSON de journaux d’audit sont présents dans les dossiers suivants :
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Vérifiez que les alertes Security Center existent dans les dossiers suivants :</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Si vous rencontrez des problèmes pendant l’installation et la configuration, ouvrez une [demande de support](/azure-supportability/how-to-create-azure-support-request.md) et sélectionnez le service **Intégration des journaux** pour demander un support.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’intégration des journaux Azure, consultez les documents suivants :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : Centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration des journaux Azure.
* [Introduction à l’intégration de journaux Azure](security-azure-log-integration-overview.md) : ce document présente l’intégration des journaux Azure, ses principales fonctionnalités et son fonctionnement.
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.
