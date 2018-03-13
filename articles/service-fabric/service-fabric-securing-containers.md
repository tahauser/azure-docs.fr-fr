---
title: "Sécurité de conteneurs Azure Service Fabric | Microsoft Docs"
description: "En savoir plus sur les services de conteneurs sécurisés."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: cc3095d9067ea529c565cc66a5e503218c822a39
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="container-security"></a>Sécurité du conteneur

Service Fabric fournit un mécanisme pour les services à l’intérieur d’un conteneur pour accéder à un certificat installé sur les nœuds dans un cluster Windows ou Linux (version 5.7 ou version ultérieure). En outre,Service Fabric prend également en charge les gMSA (comptes de service géré de groupe) pour les conteneurs Windows. 

## <a name="certificate-management-for-containers"></a>Gestion des certificats pour les conteneurs

Vous pouvez sécuriser vos services de conteneur en spécifiant un certificat. Le certificat doit être installé sur tous les nœuds du cluster de LocalMachine. Les informations de certificat sont fournies dans le manifeste d’application avec la balise `ContainerHostPolicies` comme le montre l’extrait de code suivant :

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Pour les clusters Windows, au lancement de l’application, le runtime lit les certificats et génère un fichier PFX ainsi qu’un mot de passe pour chaque certificat. Ce fichier PFX et le mot de passe sont accessibles à l’intérieur du conteneur à l’aide des variables d’environnement suivantes : 

* **Certificates_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificates_ServicePackageName_CodePackageName_CertName_Password**

Pour les clusters Linux, les certificats (PEM) sont simplement copiés à partir du magasin spécifié par X509StoreName sur le conteneur. Voici les variables d’environnement correspondantes sous Linux :

* **Certificates_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey**

Si vous disposez déjà des certificats au format requis et que vous souhaitez simplement y accéder à l’intérieur du conteneur, vous pouvez également créer un package de données à l’intérieur du package de votre application et spécifier les éléments suivants au sein du manifeste :

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Le processus ou le service de conteneur est responsable de l’importation des fichiers de certificats dans le conteneur. Pour importer le certificat, vous pouvez utiliser les scripts `setupentrypoint.sh` ou exécuter un code personnalisé au sein du processus de conteneur. L’exemple de code en C# qui importe le fichier PFX est le suivant :

```csharp
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Ce certificat PFX peut être utilisé pour authentifier l’application ou le service afin de sécuriser la communication avec d’autres services. Par défaut, les fichiers ne sont présents que dans la liste ACL SYSTEM. Vous pouvez les ajouter à d’autres comptes selon les exigences du service.


## <a name="set-up-gmsa-for-windows-containers"></a>Configurer le gMSA pour les conteneurs Windows

Pour configurer un gMSA (compte de service géré de groupe), un fichier de spécification des informations d’identification (`credspec`) est placé sur tous les nœuds du cluster. Le fichier peut être copié sur tous les nœuds à l’aide d’une extension de machine virtuelle.  Le fichier `credspec` doit contenir les informations de compte gMSA. Pour plus d’informations sur le fichier `credspec`, consultez la page [Comptes de service](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). La spécification d’informations d’identification et la balise `Hostname` sont spécifiées dans le manifeste d’application. La balise `Hostname` doit correspondre au nom de compte gMSA dans lequel le conteneur s’exécute.  La balise `Hostname` permet au conteneur de s’authentifier auprès d’autres services dans le domaine à l’aide de l’authentification Kerberos.  Un exemple de spécification des balises `Hostname` et `credspec` du manifeste d’application est indiqué dans l’extrait de code suivant :

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Étapes suivantes

* [Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-get-started-containers.md)
* [Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)
