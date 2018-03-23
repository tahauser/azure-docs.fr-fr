 ---
title: FAQ sur les API d’utilisation | Microsoft Docs description: Liste de compteurs Azure Stack, comparaison avec les API d’utilisation Azure, temps d’utilisation et temps rapporté, codes d’erreur.
services: azure-stack documentationcenter: '' author: mattbriggs manager: femila editor: ''

ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071 ms.service: azure-stack ms.workload: na ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 09/03/2018 ms.author: mabrigg ms.reviewer: alfredop

---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Forum aux questions sur l’API d’utilisation d’Azure Stack
Cet article répond à certaines questions fréquentes sur l’API d’utilisation d’Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Quels ID de compteur sont visibles ?
Des rapports d’utilisation sont générés pour les fournisseurs de ressources suivants :

| **Fournisseur de ressources** | **ID du compteur** | **Nom du compteur** | **Unité** | **Informations supplémentaires** |
| --- | --- | --- | --- | --- |
| **Réseau** |F271A8A388C44D93956A063E1D2FA80B |Static IP Address Usage |Adresses IP| Nombre d’adresses IP utilisées. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne l’adresse IP multipliée par le nombre d’heures. |
| |9E2739BA86744796B465F64674B822BA |Dynamic IP Address Usage |Adresses IP| Nombre d’adresses IP utilisées. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne l’adresse IP multipliée par le nombre d’heures. |
| **Stockage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |Go\*heures |Capacité totale consommée par table. |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |Go\*heures |Capacité totale consommée par objet blob de pages. |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |Go\*heures |Capacité totale consommée par file d’attente. |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |Go\*heures |Capacité totale consommée par objet blob de blocs. |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Nombre de requêtes en 10 000 s |Requêtes de service de Table (en 10 000 s). |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Données en entrée, en Go |Entrée de données de service de Table, en Go. |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Sortie en Go |Sortie de données de service de Table, en Go |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Nombre de requêtes en 10 000 s |Demandes de service BLOB (en 10 000 s). |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Données en entrée, en Go |Entrée de données de service BLOB, en Go. |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Sortie en Go |Sortie de données de service BLOB, en Go. |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Nombre de requêtes en 10 000 s |Demandes de service de File d’attente (en 10 000 s). |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Données en entrée, en Go |Entrée de données de service de File d’attente, en Go. |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Sortie en Go |Sortie de données de service de File d’attente, en Go |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | Mo\*heures   | Capacité totale de la base de données à sa création. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne les Mo multipliés par le nombre d’heures. |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | Mo\*heures    | Capacité totale de la base de données à sa création. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne les Mo multipliés par le nombre d’heures. |
| **Calcul** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Base VM Size Hours |Heures cœur virtuel | Nombre de cœurs virtuels multiplié par les heures d’exécution de la machine virtuelle. |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM Size Hours |Heures cœur virtuel | Nombre de cœurs virtuels multiplié par les heures d’exécution de la machine virtuelle. |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM size hours |Heures de machine virtuelle |Capture à la fois la machine virtuelle de base et la machine virtuelle Windows. Ne s’ajuste pas en fonction des cœurs. |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault transactions | Nombre de requêtes en 10 000 s| Nombre de requêtes d’API REST reçues par le plan de données Key Vault. |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |Transactions de clés avancées | 10 000 transactions|     RSA 3K/4K, transactions de clés ECC (préversion). |
| **App Service** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service | Heures cœur virtuel | Nombre de cœurs virtuels utilisés pour exécuter App Service. Remarque : Microsoft utilise ce compteur pour facturer App Service sur Azure Stack. Les fournisseurs de services cloud peuvent utiliser les autres compteurs App Service (ci-dessous) pour calculer l’utilisation de leurs clients. |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Demandes de fonctions | 10 requêtes | Nombre total d’exécutions demandées (pour 10 exécutions). Une exécution est comptée chaque fois qu’une fonction s’exécute en réponse à un événement ou est déclenchée par une liaison. |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Fonctions – Calcul | Go/s | Consommation de ressources en gigaoctets/seconde (Go/s). La **consommation de ressources constatée** est calculée en multipliant la taille moyenne de la mémoire en Go par la durée en millisecondes nécessaire à l’exécution de la fonction. La mémoire utilisée par une fonction est mesurée en arrondissant aux 128 Mo les plus proches, pour une mémoire maximale de 1 536 Mo, le temps d’exécution étant calculé en arrondissant à la milliseconde la plus proche. Le délai d’exécution et la mémoire minimum pour une seule exécution de fonction sont respectivement de 100 ms et de 128 Mo. |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Heures partagés App Service | 1 heure | Par heure d’utilisation du plan App Service partagé. Les plans sont facturés par application. |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Heures Free App Service | 1 heure | Par heure d’utilisation du plan App Service gratuit. Les plans sont facturés par application. |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Heures réduites App Service Standard | 1 heure | Le calcul dépend de la taille et du nombre d’instances. |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Heures moyennes App Service Standard | 1 heure | Le calcul dépend de la taille et du nombre d’instances. |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Heures nombreuses App Service Standard | 1 heure | Le calcul dépend de la taille et du nombre d’instances. |
|  | *Niveaux de travail personnalisés* | Niveaux de travail personnalisés | Heures | L’identifiant de compteur déterministe est créé en fonction de la référence SKU et du nom du niveau de travail personnalisé. Il est unique pour chaque niveau de travail personnalisé. |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | Par liaison SNI SSL | App Service prend en charge deux types de connexion SSL : avec Indication du nom du serveur (SNI) et avec Adresse IP. Les connexions SSL basées sur SNI fonctionnent sur les navigateurs modernes, alors que les connexions SSL avec Adresse IP fonctionnent sur tous les navigateurs. |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | Par liaison SSL basée sur IP | App Service prend en charge deux types de connexion SSL : avec Indication du nom du serveur (SNI) et avec Adresse IP. Les connexions SSL basées sur SNI fonctionnent sur les navigateurs modernes, alors que les connexions SSL avec Adresse IP fonctionnent sur tous les navigateurs. |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Traitement Web |  | Calculé par site actif par heure. |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | Bande passante de sortie externe | GB | Total d’octets de la réponse à la demande entrante + total d’octets de la demande sortante + total d’octets de la réponse à la demande FTP entrante + total d’octets de la réponse à la demande de déploiement web entrante. |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>En quoi les API d’utilisation d’Azure Stack sont-elles comparables aux [API d’utilisation d’Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (actuellement en préversion publique) ?
* L’API d’utilisation du locataire est cohérente avec l’API Azure, à une exception près : l’indicateur *showDetails* n’est actuellement pas pris en charge dans Azure Stack.
* L’API d’utilisation du fournisseur s’applique uniquement à Azure Stack.
* Actuellement, l’[API RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) qui est disponible dans Azure n’est pas disponible dans Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Quelle est la différence entre l’Heure d’utilisation et l’Heure du rapport ?
Les rapports de données d’utilisation comportent deux valeurs de durée principales :

* **Heure du rapport**. Heure à laquelle l’événement d’utilisation est entré dans le système d’utilisation
* **Heure d’utilisation**. Heure à laquelle la ressource Azure Stack a été consommée

Vous pouvez voir une différence entre les valeurs Heure d’utilisation et Heure du rapport pour un événement d’utilisation spécifique. Ce décalage peut atteindre plusieurs heures dans n’importe quel environnement.

Actuellement, vous pouvez interroger uniquement par *Heure du rapport*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Que signifient les codes d’erreur de l’API d’utilisation suivants ?
| **Code d’état HTTP** | **Code d’erreur** | **Description** |
| --- | --- | --- |
| 400 - Demande incorrecte |*NoApiVersion* |Le paramètre de requête *api-version* est manquant. |
| 400 - Demande incorrecte |*InvalidProperty* |Une propriété est manquante ou a une valeur non valide. Le message indiqué dans le code d’erreur du corps de la réponse identifie la propriété manquante. |
| 400 - Demande incorrecte |*RequestEndTimeIsInFuture* |La valeur *ReportedEndTime* est dans le futur. Les valeurs dans le futur ne sont pas autorisées pour cet argument. |
| 400 - Demande incorrecte |*SubscriberIdIsNotDirectTenant* |Un appel d’API de fournisseur a utilisé un ID d’abonnement qui n’est pas un locataire valide de l’appelant. |
| 400 - Demande incorrecte |*SubscriptionIdMissingInRequest* |L’ID d’abonnement de l’appelant est manquant. |
| 400 - Demande incorrecte |*InvalidAggregationGranularity* |Une granularité d’agrégation non valide a été demandée. Les valeurs valides sont « quotidienne » et « horaire ». |
| 503 |*ServiceUnavailable* |Une erreur renouvelable s’est produite, car le service est occupé ou l’appel est limité. |

## <a name="next-steps"></a>Étapes suivantes
[Facturation des clients et rétrofacturation dans Azure Stack](azure-stack-billing-and-chargeback.md)

[API d’utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)

[API d’utilisation des ressources de locataire](azure-stack-tenant-resource-usage-api.md)
