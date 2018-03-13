---
title: API de facturation Azure Enterprise | Microsoft Docs
description: "En savoir plus sur les API de création de rapports qui permettent aux clients d’Azure Enterprise d’extraire leurs données de consommation par programme."
services: 
documentationcenter: 
author: anandedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: f7a480c77c93035e655606433aea2547a1c105cc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Vue d’ensemble des API de création de rapports pour les clients Enterprise
Les API de création de rapports permettent aux clients Azure Enterprise d’extraire leurs données de consommation et de facturation par programme pour les transférer vers les outils d’analyse de données de leur choix. 

## <a name="enabling-data-access-to-the-api"></a>Autoriser l’API à accéder aux données
* **Générer ou récupérer la clé API** : connectez-vous au portail Enterprise et suivez le didacticiel présenté sous Aide - API de création de rapports. La première section située sous cet article d’aide explique comment générer ou récupérer la clé API pour l’inscription spécifiée.
* **Transmission des clés dans l’API** : vous devez transmettre la clé API pour chaque appel à des fins d’authentification et d’autorisation. Les en-têtes HTTP doivent contenir la propriété suivante

|Clé d’en-tête de demande | Valeur|
|-|-|
|Authorization| Indiquez la valeur dans ce format : **porteur {API_CLÉ}** <br/> Exemple : porteur eyr... 09|

## <a name="consumption-apis"></a>API de consommation
Un point de terminaison Swagger est disponible [ici](https://consumption.azure.com/swagger/ui/index) pour les API décrites ci-dessous. Il doit faciliter l’introspection de l’API et la capacité à générer des Kits de développement logiciel (SDK) clients en utilisant [AutoRest](https://github.com/Azure/AutoRest) ou [CodeGen Swagger](http://swagger.io/swagger-codegen/). À compter du 1er mai 2014, ces données sont disponibles via cette API. 

* **Balance and Summary** (Solde et résumé) : l’[API Balance and Summary](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) permet un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et dépassement des frais.

* **Usage Details** (Détails d’utilisation) : l’[API Usage Details](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) offre une analyse quotidienne des quantités consommées et des frais estimés pour un abonnement. Le résultat inclut également des informations sur les instances, les compteurs et les services. L’API peut être interrogée par période de facturation ou en indiquant une date de début et de fin. 

* **Marketplace Store Charge** (Frais de stockage de la Place de marché) : l’[API Marketplace Store Charge](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) renvoie l’analyse détaillée des frais de Place de marché basée sur l’utilisation par jour pour la période de facturation ou les dates de début et de fin indiquées (les frais ponctuels ne sont pas inclus).

* **Price Sheet** (Grille tarifaire) : l’[API Price Sheet](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fournit les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés. 

## <a name="data-freshness"></a>Actualisation des données
Les Etags s’afficheront dans la réponse de toutes les API ci-dessus. Une modification de l’ETag indique que les données ont été actualisées.  Dans les appels suivants à la même API utilisant les mêmes paramètres, passez l’ETag capturée avec la clé « If-None-Match » dans l’en-tête de requête http. Le code d’état de réponse est « NotModified » si les données n’a plus été actualisées et qu’aucune donnée n’est retournée. L’API retourne le jeu de données complet pour la période demandée chaque fois qu’il y a une modification de l’ETag.

## <a name="helper-apis"></a>API d’assistance
 **List Billing Periods** (Liste des périodes de facturation) : l’[API List Billing Periods](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) renvoie une liste des périodes de facturation contenant les données de consommation pour l’abonnement indiqué, par ordre chronologique inverse. Chaque période contient une propriété qui pointe vers l’itinéraire d’API pour les quatre ensembles de données : BalanceSummary (Solde et résumé), UsageDetails (Détails d’utilisation), Marketplace Charges (Frais de la Place de marché) et PriceSheet (Grille tarifaire).


## <a name="api-response-codes"></a>Codes de réponse HTTP  
|Code du statut de réponse|Message|Description|
|-|-|-|
|200| OK|Aucune erreur|
|401| Non autorisé| Clé API introuvable, non valide, expirée, etc.|
|404| Non disponible| Point de terminaison de rapport introuvable|
|400| Demande incorrecte| Paramètres non valides : plages de dates, nombres de Contrats Entreprise (EA), etc.|
|500| Erreur de serveur| Erreur inattendue lors du traitement de la demande| 









