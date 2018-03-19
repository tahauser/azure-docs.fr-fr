---
title: "Stockage Azure Stack : différences et points à prendre en compte"
description: "Découvrez les différences entre le stockage Azure Stack et le stockage Azure, ainsi que les points à prendre en compte quand vous déployez Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 7c4f030018f388302c3b60a41086bbd97c86513d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Stockage Azure Stack : différences et points à prendre en compte

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le stockage Azure Stack est l’ensemble des services cloud de stockage fournis dans Microsoft Azure Stack. Le stockage Azure Stack fournit des fonctionnalités de gestion des objets blob, des tables, des files d’attente et des comptes avec une sémantique Azure cohérente.

Cet article récapitule les différences connues entre le stockage Azure Stack et le stockage Azure. Il expose également les principaux points à prendre en compte quand vous déployez Azure Stack. Pour connaître les différences majeures entre Azure Stack et Azure, consultez la rubrique [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Aide-mémoire : différences entre les stockages

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
|Stockage Fichier|Partages de fichiers SMB sur le cloud pris en charge|Pas encore pris en charge
|Azure Storage Service Encryption pour les données au repos|Chiffrement AES 256 bits|Chiffrement AES 128 bits BitLocker
|Type de compte de stockage|Comptes de stockage à usage général et comptes de stockage d’objets blob Azure|À usage général uniquement.
|Options de réplication|Stockage localement redondant, stockage géoredondant, stockage géoredondant avec accès en lecture et stockage redondant dans une zone|Stockage localement redondant.
|Stockage Premium|Entièrement pris en charge|Peut être approvisionné, mais sans limite ni garantie de performances.
|Disques gérés|Premium et standard pris en charge|Pas encore pris en charge.
|Nom de l’objet blob|1 024 caractères (2 048 octets)|880 caractères (1 760 octets)
|Taille maximale d’un objet blob de blocs|4,75 To (100 Mo X 50 000 blocs)|4,75 To (100 Mo x 50 000 blocs) pour la mise à jour 1802 ou une version plus récente. 50 000 x 4 Mo (environ 195 Go) pour les versions précédentes.
|Copie d’instantané d’objet blob de pages|Prise en charge des disques de machine virtuelle non gérés par Sauvegarde Azure attachés à une machine virtuelle en fonctionnement|Pas encore pris en charge.
|Copie d’instantané incrémentiel d’objet blob de pages|Objets blob de pages Azure Premium et standard pris en charge|Pas encore pris en charge.
|Niveaux de stockage pour le Stockage Blob|Niveaux de stockage chaud, à froid et archivage.|Pas encore pris en charge.
Suppression réversible pour le Stockage Blob|VERSION PRÉLIMINAIRE|Pas encore pris en charge.
|Taille maximale d’un objet blob de pages|8 To|1 To
|Taille de page d’un objet blob de pages|512 octets|4 Ko
|Taille de la clé de ligne et de la clé de partition de table|1 024 caractères (2 048 octets)|400 caractères (800 octets)

### <a name="metrics"></a>Mesures
Il existe également des différences sur le plan des métriques de stockage :
* Les données de transaction dans les métriques de stockage ne font pas la distinction entre la bande passante réseau interne et externe.
* Les données de transaction dans les métriques de stockage ne prennent pas en compte l’accès des machines virtuelles aux disques montés.

## <a name="api-version"></a>Version de l'API
Les versions suivantes sont prises en charge avec le stockage Azure Stack :

API de services de Stockage Azure :

Mise à jour 1802 ou plus récente :
 - [2017-04-17](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)

Versions antérieures :
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)


API de gestion des services de Stockage Azure :

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Versions du SDK

Les bibliothèques de client suivantes sont prises en charge avec le stockage Azure Stack :

| Bibliothèque cliente | Version prise en charge par Azure Stack | Lien                                                                                                                                                                                                                                                                                                                                     | Spécification du point de terminaison       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | De la version 6.2.0 à 8.7.0.          | Package NuGet :<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Version de GitHub :<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | Fichier app.config              |
| Java           | De la version 4.1.0 à 6.1.0           | Package Maven :<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Version de GitHub :<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configuration de la chaîne de connexion      |
| Node.js        | De la version 1.1.0 à 2.7.0           | Lien NPM :<br>https://www.npmjs.com/package/azure-storage<br>(par exemple : exécutez « npm install azure-storage@2.7.0 »)<br> <br>Version de GitHub :<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Déclaration d’instance de service |
| C++            | De la version 2.4.0 à 3.1.0           | Package NuGet :<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Version de GitHub :<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configuration de la chaîne de connexion      |
| PHP            | De la version 0.15.0 à 1.0.0          | Version de GitHub :<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Installation via Composer (voir détails ci-dessous)                                                                                                                                                                                                                  | Configuration de la chaîne de connexion      |
| Python         | De la version 0.30.0 à 1.0.0          | Version de GitHub :<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Déclaration d’instance de service |
| Ruby           | De la version 0.12.1 à 1.0.1          | Package RubyGems :<br>Courant :<br>https://rubygems.org/gems/azure-storage-common/<br>Blob : https://rubygems.org/gems/azure-storage-blob/<br>File d’attente : https://rubygems.org/gems/azure-storage-queue/<br>Table : https://rubygems.org/gems/azure-storage-table/<br> <br>Version de GitHub :<br>https://github.com/Azure/azure-storage-ruby/releases | Configuration de la chaîne de connexion      |

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec les outils de développement du stockage Azure Stack](azure-stack-storage-dev.md)
* [Présentation du stockage Azure Stack](azure-stack-storage-overview.md)

