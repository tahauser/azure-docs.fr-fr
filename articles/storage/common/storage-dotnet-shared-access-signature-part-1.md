---
title: Utilisation des signatures d’accès partagé (SAP) dans le stockage Azure | Microsoft Docs
description: Apprenez à utiliser des signatures d’accès partagé (SAP) pour déléguer l’accès aux ressources de stockage Azure, notamment les objets blob, les files d’attente, les tables et les fichiers.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: 46fd99d7-36b3-4283-81e3-f214b29f1152
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2017
ms.author: cshoe
ms.openlocfilehash: d3f8b3261f9e2e86dbcaa41b92111545abeffe54
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="using-shared-access-signatures-sas"></a>Utilisation des signatures d’accès partagé (SAP)

Une signature d’accès partagé (SAP) constitue un moyen d’octroyer aux autres clients un accès limité aux objets dans votre compte de stockage, sans exposer votre clé de compte. Dans cet article, nous vous présentons un aperçu du modèle SAP, passons en revue les bonnes pratiques concernant les signatures d’accès partagé et examinons quelques exemples.

Pour obtenir d’autres exemples de code utilisant la SAP, consultez la page [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Exemples de stockage Blob Azure pour .NET). Pour d’autres exemples, consultez la bibliothèque [Exemples de code Azure](https://azure.microsoft.com/documentation/samples/?service=storage). Vous pouvez télécharger des exemples d’application et les exécuter ou parcourir le code sur GitHub.

## <a name="what-is-a-shared-access-signature"></a>Présentation de la signature d’accès partagé
Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Avec une signature d’accès partagé, vous pouvez accorder aux clients l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. C’est tout l’intérêt d’utiliser des signatures d’accès partagé dans vos applications : une SAP est un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Une SAP vous donne un contrôle précis sur le type d’accès que vous accordez aux clients qui disposent de la SAP, notamment :

* L’intervalle pendant lequel la SAP est valide, y compris l’heure de début et l’heure d’expiration.
* Les autorisations accordées par la SAP. Par exemple, une SAP pour un objet blob peut accorder des autorisations en lecture et en écriture sur cet objet blob, mais pas d’autorisations de suppression.
* Une adresse IP ou plage d’adresses IP facultative à partir de laquelle le stockage Azure acceptera la signature d’accès partagé. Par exemple, vous pouvez spécifier une plage d’adresses IP appartenant à votre organisation.
* Le protocole sur lequel le stockage Azure acceptera la SAP. Vous pouvez utiliser ce paramètre facultatif pour restreindre l’accès aux clients à l’aide de HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Quand devez-vous utiliser une signature d’accès partagé ?
Vous pouvez utiliser une signature d’accès partagé quand vous voulez fournir un accès aux ressources dans votre compte de stockage à un client qui ne dispose pas des clés d’accès de votre compte de stockage. Votre compte de stockage inclut une clé d’accès primaire et une clé d’accès secondaire, qui octroient toutes deux un accès administratif à votre compte et à toutes les ressources qu’il contient. En exposant l’une ou l’autre de ces clés, vous courez le risque d’une utilisation malveillante ou négligente de votre compte. Les signatures d’accès partagé offrent une alternative sûre qui permet aux clients de lire, d’écrire et de supprimer des données dans votre compte de stockage en fonction des autorisations que vous avez explicitement octroyées, sans avoir besoin d’une clé du compte.

Un service où les utilisateurs lisent et écrivent leurs propres données dans votre compte de stockage correspond à un scénario courant dans lequel une signature d'accès partagé peut s'avérer utile. Dans un scénario où un compte de stockage stocke les données utilisateur, il existe deux modèles de conception types :

1. Les clients chargent et téléchargent les données par le biais d’un service proxy frontal, qui se charge de l’authentification. Ce service présente l'avantage de permettre la validation des règles métier, mais pour de grosses quantités de données ou des transactions à haut volume, la création d'un service qui peut être mis à l'échelle en fonction de la demande peut se révéler coûteuse ou difficile.

  ![Schéma du scénario : service proxy frontal](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Un service léger authentifie le client en fonction des besoins, puis génère une signature d’accès partagé. Une fois que le client reçoit la signature, il peut accéder aux ressources du compte de stockage directement avec les autorisations définies par la signature d'accès partagé et pendant l'intervalle autorisé par cette dernière. La signature d'accès partagé atténue la nécessité du routage de toutes les données via le service proxy frontal.

  ![Schéma du scénario : service du fournisseur SAP](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

De nombreux services réels peuvent utiliser un mélange de ces deux approches. Par exemple, certaines données peuvent être traitées et validées via le proxy frontal, tandis que les autres données sont enregistrées et/ou lues directement à l’aide de SAP.

En outre, vous devez utiliser une SAP pour authentifier l’objet source d’une opération de copie, dans certains cas de figure :

* Lorsque vous copiez un objet blob dans un autre objet blob qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour authentifier l’objet blob source. Vous pouvez éventuellement utiliser une SAP pour authentifier également l’objet blob de destination.
* Lorsque vous copiez un objet blob dans un autre fichier qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour authentifier le fichier source. Vous pouvez éventuellement utiliser une SAP pour authentifier également le fichier de destination.
* Lorsque vous copiez un objet blob dans un fichier ou un fichier dans un objet blob, vous devez utiliser une SAP pour authentifier l’objet source, même si les objets source et de destination résident dans le même compte de stockage.

## <a name="types-of-shared-access-signatures"></a>Types de signatures d’accès partagé
Vous pouvez créer deux types de signatures d’accès partagé :

* **SAP de service.** Une SAP de service délègue l’accès à une ressource d’un seul des services de stockage : le service blob, de file d’attente, de table ou de fichiers. Pour obtenir des informations détaillées sur la construction du jeton de SAP de service, consultez les pages [Construction d’une SAP de service](https://msdn.microsoft.com/library/dn140255.aspx) et [Exemples de SAP de service](https://msdn.microsoft.com/library/dn140256.aspx).
* **SAP de compte.** La SAP de compte délègue l’accès aux ressources d’un ou plusieurs des services de stockage. Toutes les opérations disponibles via une SAP de service sont également disponibles via une SAP de compte. En outre, avec la SAP de compte, vous pouvez déléguer l’accès à des opérations qui s’appliquent à un service donné, telles que **Get/Set Service Properties** et **Get Service Stats**. Vous pouvez également déléguer l'accès aux opérations de lecture, d’écriture et de suppression sur les conteneurs d'objets blob, les tables, les files d'attente et les partages de fichiers qui ne sont pas autorisées avec une SAP de service. Pour obtenir des informations détaillées sur la construction du jeton SAP de compte, consultez la page [Construction d’une SAP de compte](https://msdn.microsoft.com/library/mt584140.aspx).

## <a name="how-a-shared-access-signature-works"></a>Fonctionnement d’une signature d’accès partagé
Une signature d’accès partagé est un URI signé qui désigne une ou plusieurs ressources de stockage et inclut un jeton qui contient un ensemble spécial de paramètres de requête. Le jeton indique comment le client peut accéder aux ressources. L’un des paramètres de requête, la signature, est construit à partir des paramètres de signature d’accès partagé et signé avec la clé du compte. Cette signature est utilisée par Azure Storage pour authentifier la signature d'accès partagé.

Voici un exemple d’URI de SAP, montrant l’URI de la ressource et le jeton de la SAP :

![Composants d’un URI SAP](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

Le jeton de la SAP est une chaîne que vous générez côté *client* (consultez la section [Exemples de SAP](#sas-examples) pour obtenir des exemples de code). Un jeton de la SAP que vous générez avec la bibliothèque cliente de stockage, par exemple, n’est pas suivi par le stockage Azure. Vous pouvez créer un nombre illimité de jetons de SAP côté client.

Lorsqu’un client fournit un URI de SAP au stockage Azure dans le cadre d’une demande, le service vérifie les paramètres et la signature de la SAP pour s’assurer que celle-ci est valide pour authentifier la demande. Si le service vérifie que la signature est valide, la demande est authentifiée. Dans le cas contraire, la demande est refusée avec le code d’erreur 403 (Interdit).

## <a name="shared-access-signature-parameters"></a>Paramètres de la signature d’accès partagé (SAP)
Les jetons SAP de compte et de service incluent des paramètres communs ainsi que quelques paramètres différents.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Paramètres communs aux jetons de SAP de compte et de SAP de service
* **Version de l’API.** Paramètre facultatif qui spécifie la version du service de stockage à utiliser pour exécuter la demande.
* **Version du service.** Paramètre obligatoire qui spécifie la version du service de stockage à utiliser pour authentifier la demande.
* **Heure de début.** Il s'agit de l'heure à laquelle la signature d'accès partagé devient valide. L’heure de début pour une signature d’accès partagé est facultative. Si elle est omise, la signature d’accès partagé prend effet immédiatement. L’heure de début doit être exprimée en temps universel coordonné (UTC), par un indicateur UTC spécial (« Z »), par exemple `1994-11-05T13:15:30Z`.
* **Heure d’expiration.** Il s'agit de l'heure à laquelle la signature d'accès partagé cesse d'être valide. Les meilleures pratiques recommandent soit de spécifier une heure d’expiration pour une signature d’accès partagé, soit de l’associer à une stratégie d’accès stockée. L’heure d’expiration doit être exprimée en temps universel coordonné (UTC), par un indicateur UTC spécial (« Z »), par exemple `1994-11-05T13:15:30Z` (voir détails ci-dessous).
* **Autorisations.** Les autorisations spécifiées sur la signature d'accès partagé indiquent quelles opérations le client peut exécuter avec cette dernière sur la ressource de stockage. Les autorisations disponibles ne sont pas les mêmes pour une SAP de compte et une SAP de service.
* **IP.** Paramètre facultatif qui spécifie une adresse IP ou une plage d’adresses IP en dehors d’Azure en provenance de laquelle accepter les demandes (consultez la section [État de configuration d’une session de routage](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) pour ExpressRoute).
* **Protocole.** Paramètre facultatif qui spécifie le protocole autorisé pour une demande. Les valeurs possibles sont HTTPS et HTTP à la fois (`https,http`), qui est la valeur par défaut, ou HTTPS uniquement (`https`). Notez que HTTP uniquement n’est pas une valeur autorisée.
* **Signature.** La signature est construite à partir des autres paramètres spécifiés pour le jeton, puis chiffrée. Elle est utilisée pour authentifier la SAP.

### <a name="parameters-for-a-service-sas-token"></a>Paramètres d’un jeton de SAP de service
* **Ressource de stockage.** Les ressources de stockage dont vous pouvez déléguer l’accès à l’aide d’une SAP de service incluent :
  * Conteneurs et objets blob
  * Partages de fichiers et fichiers
  * Files d’attente
  * Tables et plages d’entités de table.

### <a name="parameters-for-an-account-sas-token"></a>Paramètres d’un jeton de compte SAP
* **Service ou services.** Une SAP de compte peut déléguer l’accès à un ou plusieurs des services de stockage. Par exemple, vous pouvez créer une SAP de compte qui délègue l’accès au service BLOB et au service de fichiers. Vous pouvez également créer une SAP qui délègue l’accès à l’ensemble des quatre services (BLOB, File d’attente, Table et fichiers).
* **Types de ressources de stockage.** Une SAP de compte s’applique à une ou plusieurs classes de ressources de stockage plutôt qu’à une ressource spécifique. Vous pouvez créer une SAP de compte pour déléguer l’accès à :
  * Des API au niveau de service, qui sont appelées sur la ressource du compte de stockage. Exemples : **Get/Set Service Properties**, **Get Service Stats** et **List Containers/Queues/Tables/Shares**.
  * Des API au niveau du conteneur, qui sont appelés sur les objets de conteneur pour chaque service : conteneurs d’objets blob, files d’attente, tables et partages de fichiers. Exemples : **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** et **List Blobs/Files and Directories**.
  * Des API au niveau de l’objet, qui sont appelées sur les objets blob, les messages de file d’attente, les entités de table et les fichiers. Exemples : **Put Blob**, **Query Entity**, **Get Messages** et **Create File**.

## <a name="examples-of-sas-uris"></a>Exemples d’URI de SAP

### <a name="service-sas-uri-example"></a>Exemple d’URI SAP de service

Voici un exemple d’URI de SAP de service qui fournit des autorisations d’accès en lecture et en écriture sur un objet blob. La table décompose chaque partie de l’URI pour comprendre comment elle contribue à la signature d’accès partagé :

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| NOM | Partie de la SAP | Description |
| --- | --- | --- |
| URI de l’objet blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adresse de l'objet blob. Notez que l'utilisation de HTTPS est fortement recommandée. |
| Version des services de stockage |`sv=2015-04-05` |Pour la version 2012-02-12 des services de stockage et les versions ultérieures, ce paramètre indique la version à utiliser. |
| Heure de début |`st=2015-04-29T22%3A18%3A26Z` |Spécifiée en heure UTC. Si vous voulez que la signature d'accès partagé soit valide immédiatement, omettez l'heure de début. |
| Heure d’expiration |`se=2015-04-30T02%3A23%3A26Z` |Spécifiée en heure UTC. |
| Ressource |`sr=b` |La ressource est un objet blob. |
| Autorisations |`sp=rw` |Les autorisations octroyées par la signature d'accès partagé incluent les opérations de lecture (r) et d'écriture (w). |
| Plage d’adresses IP |`sip=168.1.5.60-168.1.5.70` |Plage d’adresses IP dont les demandes seront acceptées. |
| Protocole |`spr=https` |Seules les demandes utilisant HTTPS sont autorisées. |
| Signature |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Utilisée pour authentifier l'accès à l'objet blob. La signature est un HMAC calculé sur une chaîne de signature et une clé à l'aide de l'algorithme SHA256, puis codé en Base64. |

### <a name="account-sas-uri-example"></a>Exemple d’URI SAP de compte

Voici un exemple de SAP de compte qui utilise les mêmes paramètres communs sur le jeton. Dans la mesure où ces paramètres sont décrits ci-dessus, ils ne sont pas décrits ici. Seuls les paramètres propres à la SAP de compte sont décrits dans le tableau ci-dessous.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| NOM | Partie de la SAP | Description |
| --- | --- | --- |
| URI de ressource |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Point de terminaison du service BLOB, avec les paramètres d’obtention des propriétés du service (appel avec la méthode GET) ou de définition des propriétés du service (appel avec la méthode SET). |
| Services |`ss=bf` |La SAP s’applique au service BLOB et au service de fichiers. |
| Types de ressources |`srt=s` |La SAP s’applique aux opérations au niveau du service. |
| Autorisations |`sp=rw` |Les autorisations accordent l’accès aux opérations de lecture et d’écriture. |

Étant donné que les autorisations sont limitées au niveau du service, les opérations accessibles avec cette SAP sont **Get Blob Service Properties** (lecture) et **Set Blob Service Properties** (écriture). Cependant, avec un autre URI de ressource, le même jeton de SAP peut également être utilisé pour déléguer l’accès à l’opération **Get Blob Service Stats** (lecture).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Contrôle d’une SAP avec une stratégie d’accès stockée
Une signature d’accès partagé peut prendre deux formes :

* **Signature d’accès partagé ad hoc :** quand vous créez une signature d’accès partagé ad hoc, l’heure de début, l’heure d’expiration et les autorisations associées à cette signature sont spécifiées dans l’URI SAP (ou sont implicites, dans le cas où l’heure de début est omise). Ce type de SAP peut être créé en tant que SAP de compte ou SAP de service.
* **Signature d’accès partagé avec stratégie d’accès stockée :** une stratégie d’accès stockée est définie sur un conteneur de ressources (conteneur d’objets blob, table, file d’attente ou partage de fichiers) et permet de gérer les contraintes d’une ou de plusieurs signatures d’accès partagé. Quand vous associez une signature d’accès partagé à une stratégie d’accès stockée, la signature hérite des contraintes (heure de début, heure d’expiration et autorisations) définies pour la stratégie.

> [!NOTE]
> À l’heure actuelle, une SAP de compte doit être une SAP ad hoc. Les stratégies d’accès stockées ne sont pas encore prises en charge pour les SAP de compte.

La différence entre les deux formes est importante pour un scénario clé : la révocation. Comme un URI SAP est une URL, toute personne qui obtient la signature d’accès partagé peut s’en servir, quel que soit celui qui l’a créée initialement. Si une SAP est publiée publiquement, elle peut être utilisée par n’importe qui. Une signature d’accès partagé accorde l’accès aux ressources à toute personne qui la possède jusqu’à ce que l’un des quatre événements suivants ait lieu :

1. L'heure d'expiration spécifiée sur la signature d'accès partagé est atteinte.
2. L'heure d'expiration spécifiée sur la stratégie d'accès stockée référencée par la signature d'accès partagé est atteinte (si une stratégie d'accès stockée est référencée et si elle spécifie une heure d'expiration). Cela peut arriver soit parce que l’intervalle s’est écoulé, soit parce que vous avez modifié la stratégie d’accès stockée pour définir une heure d’expiration dans le passé, ce qui est une manière de révoquer la signature d’accès partagé.
3. La stratégie d'accès stockée référencée par la signature d'accès partagé est supprimée, ce qui est une autre manière de révoquer la signature d'accès partagé. Notez que si vous recréez la stratégie d'accès stockée avec exactement le même nom, tous les jetons de signature d'accès partagé existants seront de nouveau valides en fonction des autorisations associées à cette stratégie d'accès stockée (en partant du principe que l'heure d'expiration sur la signature d'accès partagé n'est pas passée). Si vous avez l'intention de révoquer la signature d'accès partagé, veillez à utiliser un nom différent si vous recréez la stratégie d'accès avec une heure d'expiration située dans le futur.
4. La clé de compte qui a été utilisée pour créer la signature d'accès partagé est régénérée. La regénération d’une clé de compte provoquera l’échec de l’authentification de tous les composants de l’application qui utilisent cette clé jusqu’à ce qu’ils soient mis à jour afin d’utiliser l’autre clé de compte valide ou la clé de compte nouvellement regénérée.

> [!IMPORTANT]
> L’URI d’une signature d’accès partagé est associé à la clé du compte utilisée pour créer la signature et à la stratégie d’accès stockée correspondante (le cas échéant). Si aucune stratégie d’accès stockée n’est spécifiée, la seule façon de révoquer une signature d’accès partagé consiste à modifier la clé du compte.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Authentification à partir d’une application cliente avec la SAP
Un client qui est en possession d’une SAP peut l’utiliser pour authentifier une demande sur un compte de stockage pour lequel il ne possède pas les clés de compte. Une SAP peut être incluse dans une chaîne de connexion ou utilisée directement à partir de la méthode ou du constructeur approprié(e).

### <a name="using-a-sas-in-a-connection-string"></a>Utilisation d’une SAP dans une chaîne de connexion
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Utilisation d’une SAP dans un constructeur ou une méthode
Plusieurs surcharges de méthodes et constructeurs de bibliothèques clientes du Stockage Azure offrent un paramètre SAP, afin de vous permettre d’authentifier une demande au service avec une SAP.

Ici, par exemple, un URI de SAP est utilisé pour créer une référence à un objet blob de blocs. La SAP fournit uniquement les informations d’identification nécessaires à la demande. La référence à l’objet blob de bloc est ensuite utilisée pour une opération d’écriture :

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Bonnes pratiques lors de l’utilisation de SAP
Lorsque vous utilisez des signatures d'accès partagé dans vos applications, vous devez être conscient de deux risques potentiels :

* Si une signature d'accès partagé est divulguée, toute personne qui se la procure peut s'en servir et votre compte de stockage court donc le risque d'être compromis.
* Si une signature d'accès partagé fournie à une application cliente expire et que l'application est incapable d'en récupérer une nouvelle à partir de votre service, la fonctionnalité de votre application risque d'être entravée.

Les recommandations suivantes relatives à l’utilisation des signatures d’accès partagé peuvent aider à limiter ces risques :

1. **Utilisez toujours HTTPS** pour créer ou distribuer une signature d’accès partagé. Si une signature d’accès partagé est transmise sur HTTP et interceptée, un pirate qui lance une attaque de type « attaque de l’intercepteur » (man-in-the-middle) peut lire la signature et s’en servir exactement comme l’utilisateur concerné aurait pu le faire, d’où le risque que les données sensibles soient compromises ou que les données soient altérées par l’utilisateur malveillant.
2. **Référencez si possible les stratégies d'accès stockées.** Celles-ci vous donnent la possibilité de révoquer les autorisations sans avoir à régénérer les clés de compte de stockage. Définissez une échéance très éloignée dans le temps (voire infinie) pour l’expiration de ces dernières et veillez à ce qu’elle soit régulièrement mise à jour et repoussée dans le futur.
3. **Utilisez des heures d'expiration avec une échéance à court terme sur une signature d'accès partagé ad hoc.** De cette manière, même si une signature d’accès partagé est compromise, elle n’est valide que pendant une courte durée. Cette pratique est particulièrement importante si vous ne pouvez pas référencer une stratégie d'accès stockée. Des heures d’expiration avec une échéance à court terme permettent également de limiter la quantité de données pouvant être écrites dans un objet blob en limitant le temps disponible pour le chargement vers ce dernier.
4. **Faites en sorte que les clients renouvellent automatiquement la signature d'accès partagé si nécessaire.** Les clients doivent renouveler la signature d’accès partagé bien avant l’heure d’expiration afin de laisser suffisamment de temps pour de nouvelles tentatives, si le service qui fournit la signature est indisponible. Si votre signature d’accès partagé doit être utilisée pour un petit nombre d’opérations immédiates de courte durée, censées être terminées avant l’heure d’expiration, cela ne sera peut-être pas nécessaire, car il n’est pas prévu que la signature d’accès partagé soit renouvelée. Toutefois, si vous avez un client qui effectue régulièrement des demandes par le biais de signatures d'accès partagé, le risque d'expiration est à prendre en compte. La principale considération consiste à trouver un équilibre entre la nécessité que la signature d’accès partagé ait une durée de vie limitée (comme indiqué plus haut) et la nécessité de veiller à ce que le client demande le renouvellement suffisamment tôt pour éviter une interruption due à une expiration de la signature avant le renouvellement effectif.
5. **Faites attention à la date de début de la signature d’accès partagé.** Si vous définissez la date de début d’une signature d’accès partagé sur **maintenant**, en raison du décalage d’horloge (différences constatées dans l’heure actuelle sur des machines différentes), des défaillances peuvent être observées par intermittence pendant les premières minutes. En règle générale, définissez une heure de début située au moins 15 minutes avant l’heure courante ou ne la définissez pas du tout, et elle sera alors valide immédiatement dans tous les cas. Cela vaut également d’une manière générale pour l’heure d’expiration. Souvenez-vous que vous pouvez observer jusqu’à 15 minutes de décalage d’horloge (dans l’une ou l’autre direction) sur une demande. Pour les clients qui utilisent une version de REST antérieure à la version 2012-02-12, la durée maximale pour une signature d’accès partagé qui ne renvoie pas à une stratégie d’accès stockée est de 1 heure et toutes les stratégies spécifiant une période plus longue échouent.
6. **Soyez précis quant à la ressource pour laquelle vous voulez configurer l'accès.** Une bonne pratique en matière de sécurité consiste à fournir à l’utilisateur les privilèges minimaux requis. Si un utilisateur a besoin d'un accès en lecture à une seule entité, accordez-lui un accès en lecture à cette seule entité, plutôt qu'un accès en lecture/écriture/suppression à toutes les entités. Cela permet également d’atténuer les dégâts si une signature d’accès partagé est compromise, car son pouvoir est moindre entre les mains d’une personne malveillante.
7. **Sachez que toute utilisation de votre compte sera facturée, y compris pour les signatures d'accès partagé.** Si vous fournissez un accès en écriture à un objet blob, un utilisateur peut choisir de charger un objet blob de 200 Go. Si vous lui avez également accordé un accès en lecture, il peut choisir de le télécharger 10 fois, et vous devrez alors acquitter des frais de sortie pour l’équivalent de 2 To. Accordez des autorisations limitées pour atténuer les risques liés aux actions éventuelles d’utilisateurs malveillants. Utilisez des signatures d'accès partagé à durée de vie limitée pour atténuer cette menace (mais pensez au décalage d'horloge pour l'heure de fin).
8. **Validez les données écrites avec une signature d'accès partagé.** Lorsqu'une application cliente écrit des données dans votre compte de stockage, n'oubliez pas que ces données peuvent être une source de problèmes. Si votre application exige que ces données soient validées ou autorisées avant de pouvoir être utilisées, vous devez effectuer cette validation après l'écriture des données et avant qu'elles ne soient utilisées par votre application. Cette pratique assure également une protection contre l'écriture de données endommagées ou malveillantes dans votre compte, soit par un utilisateur qui a acquis correctement la signature d'accès partagé, soit par un utilisateur qui exploite sa divulgation.
9. **N'utilisez pas toujours une signature d'accès partagé.** Parfois, les risques associés à une opération particulière sur votre compte de stockage l'emportent sur les avantages offerts par la signature d'accès partagé. Pour ces opérations, créez un service de niveau intermédiaire qui écrit dans votre compte de stockage après avoir effectué la validation des règles métier, l'authentification et un audit. Parfois aussi, il est plus simple de gérer l'accès par d'autres moyens. Par exemple, si vous voulez que tous les objets blob dans un conteneur soient publiquement lisibles, vous pouvez rendre le conteneur public, au lieu de fournir une signature d'accès partagé à chaque client.
10. **Utilisez Storage Analytics pour surveiller votre application.** Vous pouvez utiliser la journalisation et les mesures pour observer tout pic dans les échecs d’authentification dus à une interruption du service de votre fournisseur de signatures d’accès partagé ou à la suppression par inadvertance d’une stratégie d’accès stockée. Pour plus d'informations, consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) .

## <a name="sas-examples"></a>Exemples de SAP
Vous trouverez ci-dessous des exemples des deux types de signatures d’accès partagé, SAP de compte et SAP de service.

Pour exécuter ces exemples C#, vous devez référencer les packages NuGet suivants dans votre projet :

* [Bibliothèque cliente de stockage Azure pour .NET](http://www.nuget.org/packages/WindowsAzure.Storage), version 6.x ou ultérieure (pour utiliser une SAP de compte).
* [Gestionnaire de configuration Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Pour obtenir des exemples supplémentaires expliquant comment créer et tester une SAP, consultez les [Exemples de code Azure pour le stockage](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exemple : création et utilisation d’une SAP de compte
L’exemple de code suivant crée une SAP de compte valide pour le service BLOB et le service de fichiers, et donne au client des autorisations d’accès en lecture, en écriture et en liste pour accéder aux API au niveau du service. Le SAP de compte limitant le protocole à HTTPS, la demande doit être effectuée avec ce protocole.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Afin utiliser la SAP de compte pour accéder aux API au niveau du service pour le service BLOB, construisez un client d’objet blob à l’aide de la SAP et du point de terminaison de stockage d’objets blob de votre compte de stockage.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Exemple : création d’une stratégie d’accès stockée
Le code suivant crée une stratégie d’accès stockée sur un conteneur. Vous pouvez utiliser la stratégie d’accès pour spécifier des contraintes pour une SAP de service sur le conteneur ou sur ses objets blob.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Exemple : création d’une SAP de service sur un conteneur
Le code suivant crée une SAP sur un conteneur. Si le nom d’une stratégie d’accès stockée existante est fourni, cette stratégie est associée à la SAP. Dans le cas contraire, le code crée une SAP ad hoc sur le conteneur.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Exemple : création d’une SAP de service sur un objet blob
Le code suivant crée une SAP sur un objet blob. Si le nom d’une stratégie d’accès stockée existante est fourni, cette stratégie est associée à la SAP. Dans le cas contraire, le code crée une SAP ad hoc sur l’objet blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Conclusion
Les signatures d'accès partagé sont utiles pour fournir des autorisations d'accès limitées à votre compte de stockage aux clients qui ne doivent pas avoir la clé du compte. À ce titre, elles sont un élément crucial du modèle de sécurité pour toute application utilisant Azure Storage. Si vous suivez les meilleures pratiques énumérées ci-dessus, vous pouvez utiliser une signature d'accès partagé pour offrir une plus grande souplesse d'accès aux ressources de votre compte de stockage, sans compromettre la sécurité de votre application.

## <a name="next-steps"></a>Étapes suivantes
* [Signatures d’accès partagé, partie 2 : créer et utiliser une signature d’accès partagé avec Stockage Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md)
* [Délégation de l'accès avec une signature d'accès partagé](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Présentation des signatures d’accès partagé de table et de file d’attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
