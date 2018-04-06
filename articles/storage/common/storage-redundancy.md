---
title: Réplication de données dans le stockage Azure | Microsoft Docs
description: Les données de votre compte Stockage Microsoft Azure sont répliquées à des fins de durabilité et de haute disponibilité. Les options de réplication incluent le stockage localement redondant (LRS), le stockage redondant dans une zone (ZRS), le stockage géo-redondant (GRS) et le stockage géo-redondant avec accès en lecture (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Réplication Azure Storage

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour en garantir la durabilité et la haute disponibilité. La réplication copie vos données de sorte qu’elles soient protégées contre les défaillances matérielles transitoires, garantissant ainsi la disponibilité de votre application. 

Vous pouvez choisir de répliquer vos données dans le même centre de données, sur des centres de données dans la même région ou entre des régions. Si vos données sont répliquées sur plusieurs centres de données ou dans différentes régions, elles sont également protégées contre une défaillance irrémédiable dans un emplacement unique.

La réplication garantit que votre compte de stockage répond aux exigences du [contrat de niveau de service (SLA) pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/) même en cas de panne. Pour plus d’informations sur les garanties de durabilité et de disponibilité du stockage Azure, consultez le contrat de niveau de service.

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :

* [Stockage localement redondant (LRS)](#locally-redundant-storage)
* [Stockage redondant dans une zone (ZRS)](#zone-redundant-storage)
* [Stockage géo-redondant (GRS)](#geo-redundant-storage)
* [Stockage géo-redondant avec accès en lecture (RA-GRS)](#read-access-geo-redundant-storage)

Le stockage localement redondant (LRS) est l’option par défaut lorsque vous créez un compte de stockage.

Le tableau suivant fournit une rapide vue d’ensemble des différences entre LRS, ZRS, GRS et RA-GRS. Les sections suivantes de cet article décrivent chaque type de réplication plus en détails.

| Stratégie de réplication | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Les données sont répliquées entre plusieurs centres de données. |Non  |OUI |OUI |OUI |
| Les données peuvent être lues tant à partir d’un emplacement secondaire que de l’emplacement principal. |Non  |Non  |Non  |OUI |
| Conçue pour fournir une certaine durabilité d’objets sur une année donnée. |Au moins 99,999999999 % (11 chiffres 9)|Au moins 99,9999999999 % (12 chiffres 9)|Au moins 99,99999999999999 % (16 chiffres 9)|Au moins 99,99999999999999 % (16 chiffres 9)|

Consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/) pour connaître les informations de tarification des différentes options de redondance.

> [!NOTE]
> Stockage Premium prend en charge uniquement un stockage localement redondant (LRS). Pour plus d’informations sur Stockage Premium, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Stockage localement redondant
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Stockage redondant dans une zone
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>Comptes ZRS classiques

La fonctionnalité ZRS existante est maintenant appelée ZRS classique. Les comptes ZRS classiques sont disponibles uniquement pour les objets Blob de bloc dans des comptes de stockage V1 à usage général. 

ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il est possible qu’un réplica ne soit disponible que si Microsoft lance le basculement vers la région secondaire. 

Les comptes ZRS classiques ne peuvent pas être convertis en ou à partir d’un stockage LRS, GRS ou RA-GRS. De plus, les comptes ZRS classiques ne prennent en charge ni les métriques ni la journalisation.   

Lorsque le stockage ZRS est généralement disponible dans une région, vous ne pourrez plus créer un compte ZRS classique à partir du portail dans cette région, mais vous pouvez en créer un par d’autres moyens.  
Un processus de migration automatisée de ZRS classique vers ZRS sera fourni ultérieurement.

Vous pouvez migrer manuellement les données du compte ZRS vers ou à partir d’un compte RAGRS, GRS, ZRS classique ou LRS. Vous pouvez effectuer cette migration manuelle à l’aide d’AzCopy, de l’Explorateur Stockage Azure, d’Azure PowerShell, d’Azure CLI ou de l’une des bibliothèques clientes Stockage Azure.

> [!NOTE]
> Les comptes ZRS classiques sont prévus pour être un abandon et une migration requise sur le 31 mars 2021. Microsoft enverra plus de détails aux clients ZRS classique avant la désapprobation.

D’autres questions sont traitées dans la section [Forum aux questions](#frequently-asked-questions). 

## <a name="geo-redundant-storage"></a>Stockage géo-redondant
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Stockage géo-redondant avec accès en lecture
Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage. Le stockage RA-GRS fournit un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la géoréplication entre deux régions.

Lorsque vous activez l’accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, ainsi que sur le point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

Certaines considérations à prendre en compte lors de l’utilisation du stockage RA-GRS :

* Votre application doit gérer le point de terminaison avec lequel l’interaction se fait lors de l’utilisation du stockage RA-GRS.
* Étant donné que la réplication asynchrone implique un délai, il est possible que les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues si des données ne peuvent pas être récupérées à partir de la région primaire, par exemple en cas de sinistre régional.
* Si Microsoft initie un basculement vers la région secondaire, vous obtiendrez un accès en lecture et écriture à ces données une fois le basculement effectué. Pour plus d’informations, consultez [Conseils sur la récupération d’urgence](../storage-disaster-recovery-guidance.md).
* Le stockage RA-GRS est destiné à des fins de haute disponibilité. Pour obtenir des conseils sur l’évolutivité, consultez la [liste de vérification de performances](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Comment modifier le type de géoréplication de mon compte de stockage ?

   Vous pouvez modifier le type de géoréplication de votre compte de stockage à l’aide du [portail Azure](https://portal.azure.com/), d’[Azure PowerShell](storage-powershell-guide-full.md) ou de l’une des bibliothèques clientes Stockage Azure.

   > [!NOTE]
   > Les comptes ZRS ne peuvent pas être convertis en LRS ou GRS. De même, un compte LRS ou GRS ne peut pas être converti en un compte ZRS.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. La modification du type de réplication de mon compte de stockage entraîne-t-elle un temps d’arrêt ?

   Non. La modification du type de réplication de votre compte de stockage n’entraîne as de temps d’arrêt.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Des coûts supplémentaires sont-ils ajoutés en cas de modification du type de réplication de mon compte de stockage ?

   Oui. Si vous remplacez votre compte de stockage LRS par un compte GRS (ou RA-GRS), vous encourez des frais supplémentaires pour la sortie impliquée par la copie des données existantes de l’emplacement principal vers l’emplacement secondaire. Lorsque les données initiales sont copiées, il n’existe aucuns frais d’entrée supplémentaires pour la géoréplication de l’emplacement principal vers l’emplacement secondaire. Pour plus d’informations sur les coûts de bande passante, consultez la [page de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Si vous remplacez un compte de stockage GRS par un compte LRS, il n’existe aucun coût supplémentaire, mais vos données sont supprimées de l’emplacement secondaire.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. En quoi un stockage RA-GRS peut-il m’aider ?

   Le stockage GRS assure la réplication de vos données d’une région primaire vers une région secondaire distante de centaines de kilomètres de la région primaire. Avec le stockage GRS, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération de la région primaire. Le stockage RA-GRS offre la réplication GRS et permet par ailleurs de lire les données de l’emplacement secondaire. Pour obtenir des suggestions sur la conception pour la haute disponibilité, consultez [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Existe-t-il un moyen permettant de déterminer la durée de la réplication de mes données de la région primaire vers la région secondaire ?

   Si vous utilisez un stockage RA-GRS, vous pouvez vérifier l’heure de la dernière synchronisation de votre compte de stockage. La dernière heure de synchronisation correspond à une valeur de date/heure GMT. Toutes les écritures principales effectuées avant l’heure de la dernière synchronisation sont correctement écrites à l’emplacement secondaire, ce qui signifie qu’elles peuvent être lues à partir de l’emplacement secondaire. Les écritures principales effectuées après l’heure de la dernière synchronisation peuvent ou non être encore disponibles pour les lectures. Vous pouvez interroger cette valeur à l’aide du [portail Azure](https://portal.azure.com/), d’[Azure PowerShell](storage-powershell-guide-full.md) ou de l’une des bibliothèques clientes Stockage Azure.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. En cas de panne dans la région primaire, comment basculer vers la région secondaire ?

   Pour plus d’informations, consultez [Que faire en cas de panne du stockage Azure](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Que sont l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO) avec GRS ?

   **Objectif de point de récupération (RPO, Recovery Point Objective) :** dans les stockages GRS et RA-GRS, le service de stockage effectue de façon asynchrone la géoréplication des données de l’emplacement principal vers l’emplacement secondaire. En cas de sinistre régional majeur dans la région principale, Microsoft effectue un basculement vers la région secondaire. Si un basculement se produit, les modifications récentes qui n’ont pas encore été géo-répliquées peuvent être perdues. Le nombre de minutes de perte de données potentielle correspond au point RPO, et il indique le point dans le temps auquel les données peuvent être récupérées. Stockage Azure comporte généralement un point RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de la géoréplication.

   **Objectif de délai de récupération (RTO, Recovery Time Objective) :** le RTO est une mesure de la durée nécessaire pour effectuer le basculement et replacer le compte de stockage en ligne. Le temps nécessaire à l’exécution du basculement inclut les actions suivantes :

   * Le temps nécessaire à Microsoft pour déterminer si les données peuvent être récupérées à l’emplacement principal ou si un basculement est nécessaire.
   * Le temps nécessaire à l’exécution du basculement du compte de stockage en modifiant les entrées DNS principales pour pointer vers l’emplacement secondaire.

   Microsoft prend la responsabilité de conserver vos données de manière rigoureuse. Si la récupération des données dans la région principale est possible, Microsoft retarde le basculement et se concentre sur la récupération de vos données. Une version future du service vous permettra de déclencher un basculement au niveau d’un compte afin que vous puissiez contrôler vous-même le RTO.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Quels objets Stockage Azure prennent en charge le stockage ZRS ? 
Objets Blob de bloc, objets Blob de page (sauf ceux des disques de machine virtuelle de sauvegarde), tables, fichiers et files d’attente. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. Le stockage ZRS inclut-il également la géoréplication ? 
Le stockage ZRS ne prend actuellement pas en charge la géoréplication. Si votre scénario requiert la réplication entre régions dans le cadre d’une récupération d’urgence, utilisez plutôt un compte de stockage GRS ou RA-GRS.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. Que se passe-t-il quand une ou plusieurs zones ZRS est défaillante ? 
Lorsque la première zone est défaillante, le stockage ZRS continue d’écrire des réplicas de vos données sur les deux zones restantes de la région. Si une deuxième zone est défaillante, l’accès en lecture et en écriture est indisponible jusqu’à ce qu’au moins deux zones soient à nouveau disponibles. 

## <a name="next-steps"></a>Étapes suivantes
* [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/)
* [À propos des comptes de stockage Azure](../storage-create-storage-account.md)
* [Objectifs de performance et d’extensibilité du Stockage Azure](storage-scalability-targets.md)
* [Options de redondance et stockage géo-redondant avec accès en lecture de Stockage Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [Document SOSP - Stockage Azure : service de stockage cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
