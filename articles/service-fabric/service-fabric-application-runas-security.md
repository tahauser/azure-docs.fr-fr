---
title: Exécuter un service Azure Service Fabric sous des comptes système et de sécurité locale | Microsoft Docs
description: Découvrez comment exécuter une application Service Fabric sous des comptes système et de sécurité locale.  Créez des principaux de sécurité et appliquez la stratégie Run-As afin d’exécuter en toute sécurité vos services.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Exécuter un service en tant que compte d’utilisateur local ou compte système local
Avec Azure Service Fabric, vous pouvez sécuriser les applications en cours d’exécution dans le cluster sous différents comptes d’utilisateur. Par défaut, les applications Service Fabric s’exécutent sous le compte qui exécute le processus Fabric.exe. Service Fabric permet également d’exécuter des applications sous un compte utilisateur local ou système local en spécifiant une stratégie Run-As dans le manifeste de l’application. Les types de comptes système locaux pris en charge sont **LocalUser**, **NetworkService**, **LocalService** et **LocalSystem**.

Vous pouvez définir et créer des groupes d’utilisateurs de manière à pouvoir ajouter à chaque groupe un ou plusieurs utilisateurs qui seront managés ensemble. Cela est utile lorsqu’il existe plusieurs utilisateurs pour des points d’entrée de service différents et qu’ils doivent disposer de certains privilèges courants disponibles au niveau du groupe.

> [!NOTE] 
> Si vous appliquez une stratégie Run-As à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une **SecurityAccessPolicy**.  Pour plus d’informations, consultez [Assigner une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Création de groupes d'utilisateurs locaux
Vous pouvez définir et créer des groupes d’utilisateurs permettant d’ajouter un ou plusieurs utilisateurs à un groupe. Cela est utile s’il existe plusieurs utilisateurs pour des points d’entrée de service différents et s’ils doivent disposer de certains privilèges courants disponibles au niveau du groupe. L’exemple suivant montre un groupe local appelé **LocalAdminGroup** disposant de privilèges d’administrateur. Deux utilisateurs, Client1 et Client2, deviennent membres de ce groupe local dans cet exemple de manifeste de l’application :

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>Création d'utilisateurs locaux
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l’application. Quand un type de compte **LocalUser** est spécifié dans la section Principals du manifeste d’application, Service Fabric crée des comptes d’utilisateurs locaux sur les ordinateurs où l’application est déployée. Par défaut, ces comptes n’ont pas les mêmes noms que ceux spécifiés dans le manifeste de l’application (par exemple, Client3 dans l’exemple du manifeste de l’application suivant). Ils sont générés dynamiquement et sont associés à des mots de passe aléatoires.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Si une application nécessite que le compte d’utilisateur et le mot de passe soient identiques sur toutes les machines (par exemple, pour activer l’authentification NTLM), le manifeste de cluster doit définir **NTLMAuthenticationEnabled** sur true. Le manifeste de cluster doit également spécifier un **NTLMAuthenticationPasswordSecret** utilisé pour générer le même mot de passe sur toutes les machines.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Affectation de stratégies aux packages de code de service
La section **RunAsPolicy** d’un **ServiceManifestImport** spécifie le compte de la section Principals qui doit être utilisé pour exécuter un package de code. Elle associe également des packages de code du manifeste de service à des comptes d’utilisateur dans la section Principals. Vous pouvez spécifier ce paramètre pour les points d’entrée de configuration ou principaux, ou choisir l’option `All` pour appliquer ce paramètre aux deux. L’exemple suivant illustre les différentes stratégies appliquées :

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Si **EntryPointType** n'est pas spécifié, la valeur par défaut est définie sur `EntryPointType=”Main”`. La spécification d’un **SetupEntryPoint** est particulièrement utile quand vous souhaitez exécuter certaines opérations d’installation à privilèges élevés sous un compte système. Pour plus d’informations, consultez [Exécuter un script de démarrage du service en tant qu’utilisateur ou compte système local](service-fabric-run-script-at-service-startup.md). Le code de service en tant que tel peut s’exécuter sous un compte à faible privilège.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Application d’une stratégie par défaut à tous les packages de code de service
La section **DefaultRunAsPolicy** permet de spécifier un compte utilisateur par défaut pour tous les packages de code qui n’ont pas de stratégie **RunAsPolicy** spécifique définie. Si la plupart des packages de code spécifiés dans le manifeste de service utilisé par une application doivent s’exécuter sous le même utilisateur, l’application peut définir une stratégie RunAs par défaut avec ce compte utilisateur. L’exemple suivant spécifie que, si un package de code n’a pas de stratégie **RunAsPolicy** spécifiée, le package de code doit être exécuté sous le compte **MyDefaultAccount** spécifié dans la section Principals.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
