---
title: Utiliser Azure Storage Analytics pour collecter les données de journaux et de mesures | Microsoft Docs
description: Storage Analytics vous permet d’une part d’effectuer le suivi des données de métriques pour tous les services de stockage et, d’autre part, de collecter les journaux de Blob Storage, Queue Storage et Table Storage.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.openlocfilehash: edda01cbfe1b53d934f9f4a7bb01c645fa680873
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics effectue la journalisation et fournit des données de métriques pour un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer à partir du [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations [Get Blob Service Properties](https://msdn.microsoft.com/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/library/hh452243.aspx), [Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) et [Get File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) pour activer Storage Analytics pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), qui sont accessibles via les API du service BLOB et du service de Table.

Storage Analytics a une limite de 20 To pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Pour plus d'informations sur les stratégies de facturation et de rétention de données, consultez [Storage Analytics et facturation](https://msdn.microsoft.com/library/hh360997.aspx). Pour plus d'informations sur les limites des comptes de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage](storage-scalability-targets.md).

Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>À propos de la journalisation Storage Analytics
Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort.

Les entrées de journal sont créées uniquement s'il existe une activité du service de stockage. Par exemple, si un compte de stockage a une activité dans son service BLOB, mais pas dans ses services de Table ou de File d'attente, seuls des journaux relatifs au service BLOB sont créés.

La journalisation Storage Analytics n’est pas disponible pour Azure Files.

### <a name="logging-authenticated-requests"></a>Enregistrement des demandes authentifiées
Les types de demandes authentifiées suivants sont enregistrés :

* Demandes ayant réussi.
* Demandes ayant échoué, y compris les erreurs de délai d'expiration, limitation, réseau, autorisation et autres erreurs.
* Demandes utilisant une signature d'accès partagé (SAS), y compris les demandes ayant réussi et ayant échoué.
* Demandes de données d'analyse.

Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) et [Format de journal de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx).

### <a name="logging-anonymous-requests"></a>Journalisation des demandes anonymes
Les types de demandes anonymes suivants sont enregistrés :

* Demandes ayant réussi.
* Erreurs de serveur.
* Erreurs de délai d'expiration pour le client et le serveur.
* Demandes GET ayant échoué avec le code d'erreur 304 (non modifié).

Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) et [Format de journal de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx).

### <a name="how-logs-are-stored"></a>Mode de stockage des journaux
Tous les journaux sont stockés dans des objets blob de blocs dans un conteneur nommé $logs, qui est automatiquement créé lorsque Storage Analytics est activé pour un compte de stockage. Le conteneur $logs se trouve dans l'espace de noms d'objets blob du compte de stockage, par exemple : `http://<accountname>.blob.core.windows.net/$logs`. Ce conteneur ne peut pas être supprimé une fois Storage Analytics activé, mais son contenu peut l'être.

> [!NOTE]
> Il ne s’affiche pas lorsqu'une opération d'énumération des conteneurs est en cours, comme la méthode [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Vous devez y accéder directement. Par exemple, vous pouvez utiliser la méthode [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) pour accéder aux objets blob dans le conteneur `$logs`.
> À mesure que des demandes sont enregistrées, Storage Analytics télécharge les résultats intermédiaires en tant que blocs. Périodiquement, Storage Analytics valide ces blocs et les rend accessibles sous forme d'objets blob.
> 
> 

Il peut exister des enregistrements en double pour les journaux créés dans la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant les nombres **RequestId** et **Operation**.

### <a name="log-naming-conventions"></a>Conventions d'appellation de journal
Chaque journal est écrit au format suivant.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Le tableau suivant décrit chaque attribut du nom du journal.

| Attribut | Description |
| --- | --- |
| <service-name> |Nom du service de stockage. Par exemple : blob, table ou file d'attente. |
| YYYY |Année à quatre chiffres pour le journal. Par exemple : 2011. |
| MM |Mois à deux chiffres pour le journal. Par exemple : 07. |
| DD |Mois à deux chiffres pour le journal. Par exemple : 07. |
| hh |Heure à deux chiffres qui indique l’heure de début pour les journaux, au format UTC 24 heures. Par exemple : 18. |
| MM |Nombre à deux chiffres qui indique la minute de début pour les journaux. Cette valeur n'étant pas prise en charge dans la version actuelle de Storage Analytics, elle est toujours égale à 00. |
| <counter> |Compteur de base zéro à six chiffres qui indique le nombre d'objets blob de journal générés pour le service de stockage durant une période d'une heure. Ce compteur commence à 000000. Par exemple : 000001. |

Voici un exemple complet de nom de journal qui combine les exemples précédents.

    blob/2011/07/31/1800/000001.log

Voici un exemple d'URI qui peut être utilisé pour accéder au journal précédent.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Lorsqu'une demande de stockage est enregistrée, le nom du journal qui en résulte correspond à l'heure de fin de l'opération demandée. Par exemple, si une demande GetBlob a été traitée à 18 h 30 le 31 juillet 2011, le journal est écrit avec le préfixe suivant : `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Métadonnées du journal
Tous les objets blob de journal sont stockés avec des métadonnées qui peuvent être utilisées pour identifier les données de journalisation contenues dans l'objet blob. Le tableau suivant décrit chaque attribut de métadonnées.

| Attribut | Description |
| --- | --- |
| LogType |Décrit si le journal contient des informations relatives aux opérations de lecture, écriture ou suppression. Cette valeur peut inclure un type ou une combinaison des trois, séparés par des virgules. Exemple 1 : write ; Exemple 2 : read,write ; Exemple 3 : read,write,delete. |
| StartTime |Heure la plus antérieure d'une entrée dans le journal, au format AAAA-MM-JJThh:mm:ssZ. Par exemple : 2011-07-31T18:21:46Z. |
| EndTime |Heure la plus récente d'une entrée dans le journal, au format AAAA-MM-JJThh:mm:ssZ. Par exemple : 2011-07-31T18:22:09Z. |
| LogVersion |Version du format du journal. Actuellement, la seule valeur possible est : 1.0. |

La liste suivante présente un exemple de métadonnées complètes utilisant les exemples précédents.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Accès aux données de journalisation
Toutes les données du conteneur `$logs` sont accessibles à l'aide des API de service BLOB, y compris les API .NET fournies par la bibliothèque gérée Azure. L'administrateur de compte de stockage peut lire et supprimer des journaux, mais il ne peut pas les créer ou les mettre à jour. Les métadonnées et le nom du journal peuvent être utilisés lors de l’interrogation d’un journal. Il est possible que les journaux d’une heure donnée semblent désordonnés, mais les métadonnées spécifient toujours la plage de temps des entrées dans un journal. Par conséquent, vous pouvez utiliser une combinaison de noms de journaux et de métadonnées lors de la recherche d'un journal particulier.

## <a name="about-storage-analytics-metrics"></a>À propos des métriques de Storage Analytics
Storage Analytics peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage. Les transactions sont indiquées au niveau de l'opération d'API ainsi qu'au niveau du service de stockage, et la capacité est indiquée au niveau du service de stockage. Les données de métriques peuvent être utilisées pour analyser l'utilisation du service de stockage, diagnostiquer les problèmes au niveau des demandes effectuées auprès du service de stockage et améliorer les performances des applications qui utilisent un service.

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer à partir du [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations **Get Service Properties** pour activer Storage Analytics pour chaque service.

### <a name="transaction-metrics"></a>Métriques de transaction
Un ensemble fiable de données est enregistré toutes les heures ou chaque minute pour chaque service de stockage et opération d'API demandée, notamment les entrées/sorties, la disponibilité, les erreurs et les pourcentages de demande triés par catégorie. La liste complète des détails de transaction est disponible dans la rubrique [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx) .

Les données de transaction sont enregistrées à deux niveaux : au niveau du service et au niveau de l'opération API. Au niveau du service, des statistiques résumant toutes les opérations d'API demandées sont écrites dans une entité de table toutes les heures même si aucune demande n'a été adressée au service. Au niveau de l'opération d'API, les statistiques sont écrites uniquement dans une entité si l'opération a été demandée dans l'heure.

Par exemple, si vous exécutez une opération **GetBlob** sur votre service BLOB, les métriques Storage Analytics enregistrent la demande et l’incluent dans les données agrégées pour le service BLOB et l’opération **GetBlob**. Toutefois, si aucune opération **GetBlob** n’est demandée pendant l’heure, aucune entité n’est écrite dans `$MetricsTransactionsBlob` pour cette opération.

Les métriques de transaction sont enregistrées pour les demandes utilisateur et les demandes effectuées par Storage Analytics. Par exemple, les demandes par Storage Analytics pour écrire des journaux et des entités de table sont enregistrées. Pour plus d'informations sur la façon dont ces demandes sont facturées, consultez [Storage Analytics et facturation](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métriques de capacité
> [!NOTE]
> Actuellement, les métriques de capacité sont disponibles uniquement pour le service BLOB. Les métriques de capacité pour le service de Table et le service de File d'attente seront disponibles dans les versions ultérieures de Storage Analytics.
> 
> 

Les données de capacité sont enregistrées quotidiennement pour le service BLOB d’un compte de stockage, et deux entités de table sont écrites. Une entité fournit des statistiques sur les données utilisateur et l'autre, sur le conteneur d'objets blob `$logs` utilisé par Storage Analytics. La table `$MetricsCapacityBlob` inclut les statistiques suivantes :

* **Capacité** : quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.
* **ContainerCount** : nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.
* **ObjectCount** : nombre d’objets blob de blocs ou de pages validés et non validés dans le service BLOB du compte de stockage.

Pour plus d'informations sur les métriques de capacité, consultez [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Stockage des métriques
Toutes les données de métriques pour chacun des services de stockage sont stockées dans trois tables réservées à ce service : une table pour les informations sur les transactions, une autre pour les informations sur les transactions par minute et une troisième pour les informations sur la capacité. Les informations relatives aux transactions et aux transactions par minute se composent des données de demande et de réponse, et les informations de capacité se composent des données d'utilisation du stockage. Les métriques par heure, les métriques par minute et la capacité pour le service BLOB d’un compte de stockage sont accessibles dans des tables nommées comme indiqué dans le tableau ci-dessous.

| Niveau de métriques | Noms de tables | Versions prises en charge |
| --- | --- | --- |
| Métriques toutes les heures, emplacement principal |$MetricsTransactionsBlob  <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versions antérieures à la version du 15 août 2013 uniquement. Bien que ces noms soient toujours pris en charge, il est recommandé d’utiliser les tables listées ci-dessous. |
| Métriques toutes les heures, emplacement principal |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Toutes les versions, y compris celle du 15 août 2013. |
| Métriques par minute, emplacement principal |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Toutes les versions, y compris celle du 15 août 2013. |
| Métriques toutes les heures, emplacement secondaire |$MetricsHourSecondaryTransactionsBlob  <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Toutes les versions, y compris celle du 15 août 2013. La géo-réplication redondante avec accès en lecture doit être activée. |
| Métriques par minute, emplacement secondaire |$MetricsMinuteSecondaryTransactionsBlob  <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Toutes les versions, y compris celle du 15 août 2013. La géo-réplication redondante avec accès en lecture doit être activée. |
| Capacité (service Blob uniquement) |$MetricsCapacityBlob |Toutes les versions, y compris celle du 15 août 2013. |

Ces tables sont automatiquement créées lorsque Storage Analytics est activé pour un compte de stockage. Elles sont accessibles via l'espace de noms du compte de stockage, par exemple : `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Accès aux données de métriques
Toutes les données des tables de métriques sont accessibles à l'aide des API du service de Table, y compris les API .NET fournies par la bibliothèque managée Azure. L'administrateur de compte de stockage peut lire et supprimer des entités de table, mais il ne peut pas les créer ou les mettre à jour.

## <a name="billing-for-storage-analytics"></a>Facturation pour Storage Analytics
Toutes les données de métriques sont écrites par les services d'un compte de stockage. En conséquence, chaque opération d'écriture effectuée par Storage Analytics est facturable. En outre, la quantité de stockage utilisée par les données de métriques est également facturable.

Les actions suivantes effectuées par Storage Analytics sont facturables :

* Demandes de création d'objets blob pour la journalisation. 
* Demandes de création d'entités de table pour les métriques.

Si vous avez configuré une stratégie de rétention des données, vous n'êtes pas facturé pour les transactions de suppression lorsque Storage Analytics supprime les anciennes données de journalisation et de métriques. Toutefois, les transactions de suppression d'un client sont facturables. Pour plus d'informations sur les stratégies de rétention, consultez [Définition d'une stratégie de rétention des données Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Présentation des demandes facturables
Chaque demande adressée à un service de stockage d’un compte est facturable ou non. Storage Analytics enregistre chaque demande effectuée à un service, avec un message d'état qui indique comment la demande a été traitée. De même, Storage Analytics stocke des métriques pour un service et pour les opérations de l'API de ce service, y compris les pourcentages et le décompte de certains messages d'état. Conjointement, ces fonctionnalités peuvent vous aider à analyser vos demandes facturables, à améliorer votre application et à diagnostiquer les problèmes liés aux demandes à vos services. Pour plus d'informations sur la facturation, consultez [Présentation de la facturation d’Azure Storage - bande passante, transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Lorsque vous consultez des données Storage Analytics, vous pouvez utiliser les tables de la rubrique [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/azure/hh343260.aspx) pour identifier les demandes facturables. Vous pouvez ensuite comparer vos données de journaux et de métriques aux messages d'état pour déterminer si vous avez été facturé pour une demande spécifique. Vous pouvez également utiliser les tables de la rubrique précédente pour examiner la disponibilité d'un service de stockage ou d'une opération individuelle de l'API.

## <a name="next-steps"></a>Étapes suivantes
### <a name="setting-up-storage-analytics"></a>Configuration de Storage Analytics
* [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
* [Activation et configuration de Storage Analytics](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Journalisation Storage Analytics
* [À propos de la journalisation Storage Analytics](https://msdn.microsoft.com/library/hh343262.aspx)
* [Format de journal de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx)
* [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métriques de Storage Analytics
* [À propos des métriques de Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
* [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx)
* [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)  

