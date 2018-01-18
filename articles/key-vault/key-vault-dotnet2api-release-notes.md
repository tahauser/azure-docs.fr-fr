---
title: "Notes de publication de l’API Key Vault .NET 2.x | Microsoft Docs"
description: "Les développeurs .NET utiliseront cette API pour coder pour Azure Key Vault"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: 5b03f5092ee4236ca3e7b12db37dc47bd6d3a309
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - Notes de publication et guide de migration
Les notes et conseils suivants sont destinés aux développeurs utilisant Azure Key Vault .NET/bibliothèque C#. Lors du passage de la version 1.0 à la version 2.0, un certain nombre de mises à jour effectuées nécessiteront un travail de migration dans votre code pour que vous puissiez bénéficier des améliorations fonctionnelles et des ajouts de fonctionnalités telles que la prise en charge des **certificats Key Vault**.

## <a name="key-vault-certificates"></a>Certificats Key Vault

La prise en charge des certificats Key Vault permet de gérer vos certificats x509 et les comportements suivants :  

* Permet à un propriétaire de certificat de créer un certificat via un processus de création de Key Vault ou l’importation d’un certificat existant. Cela concerne à la fois les certificats auto-signés et ceux générés par une autorité de certification.
* Permet à un propriétaire de certificat Key Vault d’implémenter le stockage sécurisé et la gestion des certificats X509 sans interaction avec des éléments de clé privée.  
* Permet à un propriétaire de certificat de créer une stratégie qui ordonne à Key Vault de gérer le cycle de vie d’un certificat.  
* Permet aux propriétaires de certificat de fournir des informations de contact pour les notifications concernant des événements d’expiration du cycle de vie et le renouvellement de certificat.  
* Prend en charge le renouvellement automatique avec des émetteurs sélectionnés : fournisseurs de certificats X509 de partenaire Key Vault/autorités de certification.
  
  * REMARQUE : les fournisseurs/autorités non partenaires sont également autorisés, mais ils ne prendront pas en charge la fonctionnalité de renouvellement automatique.

## <a name="net-support"></a>Prise en charge de .NET

* **.NET 4.0** n’est pas pris en charge par la version 2.0 de la bibliothèque .NET d’Azure Key Vault
* **.NET Framework 4.5.2** n’est pas pris en charge par la version 2.0 de la bibliothèque .NET d’Azure Key Vault
* **.NET Standard 1.4** n’est pas pris en charge par la version 2.0 de la bibliothèque .NET d’Azure Key Vault

## <a name="namespaces"></a>Espaces de noms

* L’espace de noms des **modèles** **Microsoft.Azure.KeyVault** est remplacé par **Microsoft.Azure.KeyVault.Models**.
* L’espace de noms **Microsoft.Azure.KeyVault.Internal** est supprimé.
* L’espace de noms des dépendances du Kit de développement logiciel (SDK) Azure **Hyak.Common** et **Hyak.Common.Internals** est remplacé par **Microsoft.Rest** et **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Modifications des types

* *Secret* est remplacé par *SecretBundle*
* *Dictionary* est remplacé par *IDictionary*
* *List<T>, chaîne []* est remplacé par *IList<T>*
* *NextList* est remplacé par *NextPageLink*

## <a name="return-types"></a>Types de retour

* **KeyList** et **SecretList** renvoient *IPage<T>* au lieu de *ListKeysResponseMessage*
* Le résultat généré **BackupKeyAsync** renvoie *BackupKeyResult* qui contient *Valeur* (objet blob de sauvegarde). Auparavant, la méthode était encapsulée et ne renvoyait que la valeur.

## <a name="exceptions"></a>Exceptions

* *KeyVaultClientException* est remplacé par *KeyVaultErrorException*
* L’erreur de service *exception.Error* est remplacée par *exception.Body.Error.Message*.
* Les informations supplémentaires du message d’erreur pour **[JsonExtensionData]** ont été supprimées.

## <a name="constructors"></a>Constructeurs

* Au lieu d’accepter un *HttpClient* comme un argument de constructeur, le constructeur accepte seulement *HttpClientHandler* ou *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Packages téléchargés

Lorsqu’un client traite une dépendance sur Key Vault, les éléments suivants sont téléchargés

### <a name="previous-package-list"></a>Liste des packages précédents

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>Liste des packages actuels

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>Modifications de classe

* La classe **UnixEpoch** a été supprimée
* La classe **Base64UrlConverter** a été renommée **Base64UrlJsonConverter**

## <a name="other-changes"></a>Autres modifications

* La prise en charge de la configuration de la stratégie de nouvelle tentative d’opération KV sur les défaillances passagères a été ajoutée à cette version de l’API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Pour les opérations qui renvoyaient *vault*, le type de retour était une classe contenant une propriété Vault. Le type de retour est maintenant *Vault*.
* *PermissionsToKeys* et *PermissionsToSecrets* sont à présent *Permissions.Keys* et *Permissions.Secrets*
* Certaines modifications des types de retour s’appliquent également à control-plane.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Le package est interrompu jusqu’à **Microsoft.Azure.KeyVault.Extensions** et **Microsoft.Azure.KeyVault.Cryptography** pour les opérations de chiffrement.

