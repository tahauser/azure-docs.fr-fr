---
title: Gérer des secrets d’applications Azure Service Fabric | Microsoft Docs
description: Découvrez comment sécuriser des valeurs secrètes dans une application Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: 931667509a9aa5e898cd01ad26ff046e30acd3fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Gérer les secrets dans les applications Service Fabric
Ce guide vous guide à travers les étapes de la gestion des secrets dans une application Service Fabric. Les secrets peuvent être des informations sensibles quelconques, notamment des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs qui ne doivent pas être traitées en texte brut.

[Azure Key Vault][key-vault-get-started] est utilisé ici comme un emplacement de stockage sécurisé pour des certificats et comme un moyen d’installer des certificats sur des clusters Service Fabric dans Azure. Si vous ne déployez pas dans Azure, il est inutile d’utiliser le coffre de clés pour gérer les secrets dans des applications Service Fabric. Toutefois, *l’utilisation de* secrets dans une application cloud est indépendante de la plateforme et permet ainsi un déploiement d’applications dans un cluster hébergé à n’importe quel endroit. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obtenir un certificat de chiffrement de données
Un certificat de chiffrement de données est utilisé exclusivement pour le chiffrement et déchiffrement des valeurs de configuration dans le fichier Settings.xml d’un service et n’est pas utilisé à des fins d’authentification ou de signature du texte chiffré. Le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
* L’utilisation d’une clé de certificat doit inclure le chiffrement de données (10) et ne doit pas inclure l’authentification du serveur ou l’authentification du client. 
  
  Par exemple, lorsque vous créez un certificat auto-signé à l’aide de PowerShell, l’indicateur `KeyUsage` doit être défini sur `DataEncipherment` :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installation du certificat dans votre cluster
Ce certificat doit être installé sur chaque nœud du cluster. Il est utilisé lors de l’exécution pour déchiffrer les valeurs stockées dans le fichier Settings.xml d’un service. Consultez la page concernant la [création d’un cluster à l’aide d’Azure Resource Manager][service-fabric-cluster-creation-via-arm] pour obtenir des instructions d’installation. 

## <a name="encrypt-application-secrets"></a>Chiffrement de secrets d’application
Lors du déploiement d’une application, chiffrez les valeurs secrètes avec le certificat et injectez-les dans le fichier de configuration Settings.xml d’un service. Le Kit de développement logiciel (SDK) Service Fabric dispose de fonctions intégrées de chiffrement et déchiffrement de secrets. Les valeurs de clé secrète peuvent être chiffrées au moment de leur génération, puis déchiffrés et lues par programme dans un code de service. 

La commande PowerShell suivante est utilisée pour chiffrer un secret. Cette commande chiffre uniquement la valeur ; elle ne signe **pas** le texte chiffré. Vous devez utiliser le certificat de chiffrement qui est installé dans votre cluster afin de produire le texte chiffré pour les valeurs secrètes :

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

La chaîne encodée en base 64 qui en résulte contient à la fois le texte chiffré secret et plus d’informations sur le certificat qui a été utilisé pour le chiffrement.  La chaîne codée en base 64 peut être insérée dans un paramètre dans le fichier de configuration Settings.xml de votre service avec l’attribut `IsEncrypted` défini sur la valeur `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Injection de secrets d’application dans des instances d’application
Idéalement, un déploiement dans différents environnements doit être aussi automatisé que possible. Cela est possible en effectuant un chiffrement de secret dans un environnement de construction et en fournissant les secrets chiffrés en tant que paramètres lors de la création d’instances d’application.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilisation de paramètres substituables dans Settings.xml
Le fichier de configuration Settings.xml autorise des paramètres substituables qui peuvent être fournis au moment de la création d’une application. Utilisez l’attribut `MustOverride` au lieu de fournir une valeur pour un paramètre :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Pour remplacer des valeurs dans le fichier Settings.xml, déclarez un paramètre de remplacement pour le service dans le fichier ApplicationManifest.xml :

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

À présent, la valeur peut être spécifiée en tant que *paramètre d’application* lors de la création d’une instance de l’application. La création d’une instance d’application peut être scriptée à l’aide de PowerShell ou écrite en C# pour faciliter l’intégration dans un processus de génération.

Avec PowerShell, le paramètre est fourni à la commande `New-ServiceFabricApplication` en tant que [table de hachage](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Avec C#, les paramètres de l’application sont spécifiés dans un `ApplicationDescription` en tant que `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-secrets-from-service-code"></a>Déchiffrement de secrets à partir du code de service
Vous pouvez lire les valeurs chiffrées dans Settings.xml en les déchiffrant avec le certificat de chiffrement utilisé pour chiffrer le secret. Les services dans Service Fabric s’exécutent sous NETWORK SERVICE (service réseau) par défaut sur Windows et n’ont pas accès aux certificats installés sur le nœud sans configuration complémentaire.

Lorsque vous utilisez un certificat de chiffrement de données, vous devez vous assurer que NETWORK SERVICE ou tout compte utilisateur utilisé pour l’exécution du service a accès à la clé privée du certificat. Service Fabric gère l’octroi d’accès pour votre service automatiquement si vous le configurez en conséquence. Cette configuration est possible dans ApplicationManifest.xml en définissant des utilisateurs et des stratégies de sécurité pour les certificats. Dans l’exemple suivant, le compte NETWORK SERVICE reçoit un accès en lecture au certificat défini par son empreinte numérique :

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> lorsque vous copiez une empreinte numérique depuis le composant logiciel enfichable d’un magasin de certificats sous Windows, un caractère invisible est placé au début de la chaîne d’empreinte numérique. Ce caractère invisible peut provoquer une erreur lorsque vous tentez de trouver un certificat par empreinte numérique. Veillez à supprimer ce caractère supplémentaire.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Utilisation de secrets d’application dans le code de service
L’API pour l’accès aux valeurs de configuration depuis Settings.xml dans un package de configuration permet de déchiffrer facilement des valeurs dont l’attribut `IsEncrypted` a la valeur `true`. Le texte chiffré contenant des informations sur le certificat utilisé pour le chiffrement, il est inutile de le rechercher manuellement le certificat. Le certificat doit uniquement être installé sur le nœud sur lequel le service est exécuté. Il vous suffit d’appeler la méthode `DecryptValue()` pour récupérer la valeur de secret d’origine :

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [la sécurité des applications et des services](service-fabric-application-and-service-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
