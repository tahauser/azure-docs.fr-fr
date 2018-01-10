---
title: "En savoir plus sur les stratégies de sécurité des microservices Azure | Microsoft Docs"
description: "Vue d’ensemble de l’exécution d’une application Service Fabric sous des comptes de sécurité système et locaux, incluant le point SetupEntry où une application doit effectuer une action privilégiée avant de démarrer"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: b2ff715d8225bd0a9c7f6108f8804cdfa3189cc8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Configurer les stratégies de sécurité de votre application
Avec Azure Service Fabric, vous pouvez sécuriser les applications en cours d’exécution dans le cluster sous différents comptes d’utilisateur. Service Fabric permet également de sécuriser les ressources utilisées par des applications au moment du déploiement sous les comptes utilisateurs, par exemple les fichiers, les annuaires et les certificats. Ainsi, les applications en cours d’exécution sont plus sécurisées, même dans un environnement hébergé partagé.

Par défaut, les applications Service Fabric s’exécutent sous le compte qui exécute le processus Fabric.exe. Service Fabric permet également d’exécuter des applications sous un compte utilisateur ou système local spécifié dans le manifeste de l’application. Les types de comptes système locaux pris en charge sont **LocalUser**, **NetworkService**, **LocalService** et **LocalSystem**.

 Lorsque vous exécutez Service Fabric sur Windows Server dans votre centre de données à l’aide du programme d’installation autonome, vous pouvez utiliser des comptes de domaine Active Directory, y compris des comptes de service géré de groupe.

Vous pouvez définir et créer des groupes d’utilisateurs de manière à pouvoir ajouter à chaque groupe un ou plusieurs utilisateurs qui seront gérés ensemble. Cela est utile lorsqu’il existe plusieurs utilisateurs pour des points d’entrée de service différents et qu’ils doivent disposer de certains privilèges courants disponibles au niveau du groupe.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurer la stratégie pour un point d’entrée de configuration de service
Comme décrit dans [Manifestes de l’application et du service](service-fabric-application-and-service-manifests.md), le point d’entrée de configuration, **SetupEntryPoint**, est un point d’entrée privilégié qui s’exécute avec les mêmes informations d’identification que Service Fabric (en général, le compte *NetworkService*) avant tout autre point d’entrée. Le fichier exécutable spécifié par **EntryPoint** est généralement l’hôte de service à exécution longue. Ainsi, le fait de disposer d’un point d’entrée de configuration distinct évite d’avoir à lancer l’exécutable d’hôte de service avec des privilèges élevés pendant de longues périodes de temps. Le fichier exécutable spécifié par **EntryPoint** est exécuté une fois que **SetupEntryPoint** s’est terminé correctement. Le processus résultant fait l’objet d’une surveillance et est redémarré (à partir de **SetupEntryPoint**) en cas d’interruption ou de défaillance.

L’exemple suivant est un exemple simple de manifeste de service illustrant SetupEntryPoint et le point d’entrée (EntryPoint) principal du service.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Configurer la stratégie à l’aide d’un compte local
Après avoir configuré le service pour obtenir un point d’entrée de configuration, vous pouvez modifier les autorisations de sécurité sous lesquelles ce point d’entrée s’exécute dans le manifeste d’application. L’exemple suivant montre comment configurer le service pour qu’il s’exécute avec des privilèges de compte d’administrateur de l’utilisateur.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

Tout d’abord, créez une section **Principals** avec un nom d’utilisateur, par exemple SetupAdminUser. Ceci indique que l'utilisateur est membre du groupe des administrateurs du système.

Ensuite, sous la section **ServiceManifestImport**, configurez une stratégie pour appliquer ce principal à **SetupEntryPoint**. Ceci indique à Service Fabric que lorsque le fichier **MySetup.bat** est exécuté, il doit être exécuté en `RunAs` avec des privilèges d’administrateur. Étant donné que vous n’avez *pas* appliqué de stratégie au point d’entrée principal, le code dans **MyServiceHost.exe** s’exécute sous le compte **NetworkService** du système. Il s’agit du compte par défaut avec lequel tous les points d’entrée de service sont exécutés en RunAs.

Nous allons maintenant ajouter le fichier MySetup.bat au projet Visual Studio pour tester les privilèges d’administrateur. Dans Visual Studio, cliquez avec le bouton droit sur le projet de service et ajoutez un nouveau fichier appelé MySetup.bat.

Vérifiez ensuite que le fichier MySetup.bat est inclus dans le package de service. Par défaut, il ne l’est pas. Sélectionnez le fichier, puis cliquez avec le bouton droit pour afficher le menu contextuel et choisissez **Propriétés**. Dans la boîte de dialogue Propriétés, vérifiez que **Copier dans le répertoire de sortie** est défini sur **Copier si plus récent**. Voir la capture d’écran suivante.

![Visual Studio CopyToOutput pour fichier batch SetupEntryPoint][image1]

Ouvrez maintenant le fichier MySetup.bat et ajoutez les commandes suivantes :

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ensuite, générez et déployez la solution vers un cluster de développement local. Une fois que le service a démarré, comme illustré dans Service Fabric Explorer, vous pouvez voir que le fichier MySetup.bat a réussi de deux façons. Ouvrez une invite de commandes PowerShell et entrez :

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Ensuite, notez le nom du nœud sur lequel le service a été déployé et démarré dans Service Fabric Explorer, par exemple, Nœud 2. Accédez au dossier de travail de l’instance d’application pour rechercher le fichier out.txt qui affiche la valeur de **TestVariable**. Par exemple, si ce service a été déployé sur le Nœud 2, vous pouvez accéder à ce chemin pour **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurer la stratégie à l’aide de comptes système locaux
Il est souvent préférable d’exécuter le script de démarrage à l’aide d’un compte système local plutôt que d’un compte d’administrateur. En règle générale, exécuter la stratégie RunAs en tant que membre du groupe Administrateurs ne fonctionne pas correctement, car le contrôle d’accès utilisateur est activé par défaut sur les ordinateurs. Dans ce cas, **nous vous recommandons d’exécuter SetupEntryPoint en tant que LocalSystem plutôt qu’utilisateur local ajouté au groupe Administrateurs**. L’exemple suivant montre comment configurer SetupEntryPoint pour une exécution en tant que LocalSystem :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

Dans le cas des clusters Linux, pour exécuter un service ou la configuration de point d’entrée en tant que **racine**, vous pouvez spécifier le **AccountType** en tant que **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Lancer des commandes PowerShell à partir d’un point d’entrée de configuration
Pour exécuter PowerShell à partir du point **SetupEntryPoint**, vous pouvez exécuter **PowerShell.exe** dans un fichier de commandes qui pointe vers un fichier PowerShell. Tout d’abord, ajoutez un fichier PowerShell au projet de service, par exemple **MySetup.ps1**. N’oubliez pas de définir la propriété *Copier si plus récent* afin que le fichier soit également inclus dans le package de service. L’exemple suivant montre un exemple de fichier de commandes permettant de lancer un fichier PowerShell appelé MySetup.ps1, qui définit une variable d’environnement appelée **TestVariable**.

MySetup.bat pour lancer un fichier PowerShell :

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Dans le fichier PowerShell, ajoutez la commande suivante pour définir une variable d’environnement système :

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Par défaut, quand le fichier de commandes s’exécute, il détermine si le dossier d’application **work** contient des fichiers. Dans ce cas, quand MySetup.bat s’exécute, il doit rechercher le fichier MySetup.ps1 dans ce dossier, qui contient le **package de code** de l’application. À cette fin, définissez le dossier de travail :
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>Utiliser la console de redirection pour le débogage local
Parfois, à des fins de débogage, il est utile de visualiser la sortie de la console générée par l’exécution d’un script. Pour ce faire, vous pouvez définir une stratégie de redirection de console qui écrit la sortie dans un fichier. La sortie du fichier est écrite dans le dossier d’application **log** sur le nœud sur lequel l’application est déployée et exécutée. (Consultez l’exemple précédent pour déterminer son emplacement.)

> [!WARNING]
> N’utilisez jamais la stratégie de redirection de console dans une application déployée dans un environnement de production, car cela peut affecter le basculement de l’application. N’utilisez cette stratégie *que* pour le développement local et à des fins de débogage.  
> 
> 

L’exemple suivant montre comment configurer la redirection de console avec une valeur FileRetentionCount :

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Si vous modifiez le fichier MySetup.ps1 pour écrire une commande **Echo**, le contenu de celle-ci apparaît dans le fichier de sortie à des fins de débogage :

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**Une fois que vous avez débogué votre script, supprimez immédiatement cette stratégie de redirection de console**.

## <a name="configure-a-policy-for-service-code-packages"></a>Configurer une stratégie pour les packages de code de service
Dans les étapes précédentes, vous avez vu comment appliquer une stratégie RunAs à un point SetupEntryPoint. Allons plus loin et voyons comment créer des principaux différents qui peuvent être appliqués en tant que stratégies de service.

### <a name="create-local-user-groups"></a>Création de groupes d'utilisateurs locaux
Vous pouvez définir et créer des groupes d’utilisateurs permettant d’ajouter un ou plusieurs utilisateurs à un groupe. Cela est utile s’il existe plusieurs utilisateurs pour des points d’entrée de service différents et s’ils doivent disposer de certains privilèges courants disponibles au niveau du groupe. L’exemple suivant montre un groupe local appelé **LocalAdminGroup** disposant de privilèges d’administrateur. Deux utilisateurs, Customer1 et Customer2, deviennent membres de ce groupe local.

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

### <a name="create-local-users"></a>Création d'utilisateurs locaux
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l’application. Quand un type de compte **LocalUser** est spécifié dans la section Principals du manifeste d’application, Service Fabric crée des comptes d’utilisateurs locaux sur les ordinateurs où l’application est déployée. Par défaut, ces comptes n’ont pas les mêmes noms que ceux spécifiés dans le manifeste d’application (Customer3 dans l’exemple suivant). Ils sont générés dynamiquement et sont associés à des mots de passe aléatoires.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Si une application nécessite que le compte d’utilisateur et le mot de passe soient identiques sur tous les ordinateurs (par exemple, pour activer l’authentification NTLM), le manifeste de cluster doit définir NTLMAuthenticationEnabled sur « true ». Le manifeste de cluster doit également spécifier un élément NTLMAuthenticationPasswordSecret à utiliser pour générer le même mot de passe sur toutes les machines.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Affectation de stratégies aux packages de code de service
La section **RunAsPolicy** d’un **ServiceManifestImport** spécifie le compte de la section Principals qui doit être utilisé pour exécuter un package de code. Elle associe également des packages de code du manifeste de service à des comptes d’utilisateur dans la section Principals. Vous pouvez spécifier ce paramètre pour les points d’entrée de configuration ou principaux, ou choisir l’option `All` pour appliquer ce paramètre aux deux. L’exemple suivant illustre les différentes stratégies appliquées :

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Si **EntryPointType** n'est pas spécifié, la valeur par défaut est définie sur EntryPointType=« Main ». La spécification d’un **SetupEntryPoint** est particulièrement utile quand vous souhaitez exécuter certaines opérations d’installation à privilèges élevés sous un compte système. Le code de service en tant que tel peut s’exécuter sous un compte à faible privilège.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Application d’une stratégie par défaut à tous les packages de code de service
La section **DefaultRunAsPolicy** permet de spécifier un compte utilisateur par défaut pour tous les packages de code qui n’ont pas de stratégie **RunAsPolicy** spécifique définie. Si la plupart des packages de code spécifiés dans le manifeste de service utilisé par une application doivent s’exécuter sous le même utilisateur, l’application peut définir une stratégie RunAs par défaut avec ce compte utilisateur. L’exemple suivant spécifie que, si un package de code n’a pas de stratégie **RunAsPolicy** spécifiée, le package de code doit être exécuté sous le compte **MyDefaultAccount** spécifié dans la section Principals.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Utiliser un utilisateur ou groupe de domaine Active Directory
Pour une instance de Service Fabric installée sur Windows Server par le biais du programme d’installation autonome, vous pouvez exécuter le service sous les informations d’identification d’un compte de groupe ou d’utilisateur Active Directory. Il s’agit d’Active Directory en local au sein de votre domaine et non avec Azure Active Directory (Azure AD). En utilisant un groupe ou un utilisateur de domaine, vous pouvez accéder à d’autres ressources dans le domaine (par exemple, les partages de fichiers) qui disposent d’autorisations.

L’exemple suivant montre un utilisateur Active Directory appelé *TestUser* avec un mot de passe de domaine chiffré à l’aide d’un certificat nommé *MyCert*. Vous pouvez utiliser la commande PowerShell `Invoke-ServiceFabricEncryptText` pour créer le texte de chiffrement du secret. Consultez [Gestion des secrets dans des applications Service Fabric](service-fabric-application-secret-management.md) pour en savoir plus.

Vous devez déployer la clé privée du certificat pour déchiffrer le mot de passe sur l’ordinateur local à l’aide d’une méthode hors bande (avec Azure Resource Manager dans Azure). Ensuite, lorsque Service Fabric déploie le package de service sur l’ordinateur, il est en mesure de déchiffrer le secret (ainsi que le nom d’utilisateur) et de s’authentifier auprès d’Active Directory pour s’exécuter sous ces informations d’identification.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Utilisez un compte de service géré de groupe.
Pour une instance de Service Fabric installée sur Windows Server par le biais du programme d’installation autonome, vous pouvez exécuter le service en tant que compte de service géré de groupe (gMSA, group Managed Service Account). Remarque : il s’agit d’Active Directory en local au sein de votre domaine et non avec Azure Active Directory (Azure AD). Si vous utilisez un compte gMSA, il n’existe aucun mot de passe ou mot de passe chiffré stocké dans le `Application Manifest`.

L’exemple suivant montre comment créer un compte gMSA nommé *svc-Test$*, comment déployer ce compte de service géré sur les nœuds de cluster et comment configurer le principal de l’utilisateur.

##### <a name="prerequisites"></a>Composants requis.
- Le domaine a besoin d’une clé racine KDS.
- Le domaine doit être au niveau fonctionnel de Windows Server 2012 ou version ultérieure.

##### <a name="example"></a>Exemple
1. Avoir un administrateur de domaine Active Directory qui crée un compte de service géré de groupe à l’aide de la cmdlet `New-ADServiceAccount` et garantit que le `PrincipalsAllowedToRetrieveManagedPassword` inclut tous les nœuds du cluster Service Fabric. Notez que `AccountName`, `DnsHostName` et `ServicePrincipalName` doivent être uniques.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. Sur chacun des nœuds de cluster Service Fabric (par exemple `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), installez et testez le compte gMSA.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Configurez le principal de l’utilisateur et la stratégie RunAsPolicy pour faire référence à l’utilisateur.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Affectation d’une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS
Si vous appliquez une stratégie RunAs à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une stratégie **SecurityAccessPolicy** pour vous assurer que les ports affectés à ces points de terminaison sont bien au format ACL pour le compte utilisateur RunAs sous lequel le service est exécuté. Sinon, **http.sys** n’a pas accès au service et les appels en provenance du client échouent. L’exemple suivant applique le compte Customer1 au point de terminaison **EndpointName**, ce qui lui attribue des droits d’accès complets.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Pour le point de terminaison HTTPS, vous devez également indiquer le nom du certificat à retourner au client. Vous pouvez pour cela utiliser la stratégie **EndpointBindingPolicy**, avec le certificat défini dans une section dédiée aux certificats du manifeste d’application.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Mise à niveau de plusieurs applications avec des points de terminaison https
Vous devez veiller à ne pas utiliser le **même port** pour différentes instances de la même application lors de l’utilisation de http**s**. En effet, Service Fabric ne pourra plus mettre à niveau le certificat pour l’une des instances de l’application. Par exemple, si l’application 1 ou l’application 2 souhaitent mettre à niveau leur certificat 1 vers le certificat 2. Lors de la mise à niveau, Service Fabric peut avoir nettoyé l’inscription de certificat 1 auprès de http.sys même si l’autre application l’utilise toujours. Pour éviter cela, Service Fabric détecte l’existence d’une autre instance d’application inscrite sur le port avec le certificat (en raison de http.sys) et l’opération échoue.

Par conséquent, Service Fabric ne prend pas en charge la mise à niveau de deux services utilisant **le même port** dans des instances d’application différentes. En d’autres termes, vous ne pouvez pas utiliser le même certificat sur les différents services au niveau du même port. Si vous avez besoin d’un certificat partagé sur le même port, vous devez vous assurer que les services sont placés sur des machines différentes avec des contraintes de placement. Vous pouvez aussi envisager d’utiliser le cas échéant les ports dynamiques Service Fabric pour chaque service de chaque instance d’application. 

Si une mise à niveau avec https échoue, un avertissement d’erreur indique que l’API du serveur HTTP Windows ne prend pas en charge plusieurs certificats pour les applications qui partagent le même port.

## <a name="a-complete-application-manifest-example"></a>Exemple complet de manifeste d'application
Le manifeste d’application suivant affiche un grand nombre des différents paramètres :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
