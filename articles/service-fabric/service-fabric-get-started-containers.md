---
title: "Créer une application de conteneur Microsoft Azure Service Fabric | Microsoft Docs"
description: "Créez votre première application de conteneur Windows sur Microsoft Azure Service Fabric.  Concevez une image Docker avec une application Python, envoyez l’image vers un registre de conteneurs, créez et déployez une application de conteneur Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi
ms.openlocfilehash: 23e8b1023aebd5381fc89535ce265883d6a8fceb
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Créer votre première application de conteneur Service Fabric sur Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

L’exécution d’une application existante dans un conteneur Windows sur un cluster Service Fabric ne nécessite aucune modification de votre application. Cet article vous accompagne dans la création d’une image Docker contenant une application web [Flask](http://flask.pocoo.org/) Python et le déploiement dans un cluster Service Fabric.  Vous allez également partager votre application en conteneur via [Azure Container Registry](/azure/container-registry/).  Cet article suppose une connaissance élémentaire de Docker. Pour en savoir plus sur Docker, consultez la [présentation de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Composants requis
Un ordinateur de développement exécutant :
* Visual Studio 2015 ou Visual Studio 2017.
* [Outils et SDK Service Fabric](service-fabric-get-started.md).
*  Docker pour Windows.  [Obtenez Docker CE pour Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Après l’installation et le démarrage de Docker, cliquez avec le bouton droit de la souris sur l’icône de la barre d’état, puis sélectionnez **Switch to Windows containers** (Basculer vers les conteneurs Windows). Cette action est nécessaire pour exécuter des images Docker basées sur Windows.

Un cluster Windows avec au moins trois nœuds s’exécutant sur Windows Server 2016 avec conteneurs. [Créez un cluster](service-fabric-cluster-creation-via-portal.md) ou [essayez gratuitement Service Fabric](https://aka.ms/tryservicefabric).

Un registre dans Azure Container Registry. [Créez un registre de conteneurs](../container-registry/container-registry-get-started-portal.md) dans votre abonnement Azure.

## <a name="define-the-docker-container"></a>Définir le conteneur Docker
Créez une image basée sur [l’image Python](https://hub.docker.com/_/python/) située sur Docker Hub.

Définissez votre conteneur Docker dans un fichier Dockerfile. Le fichier Dockerfile contient des instructions pour la configuration de l’environnement à l’intérieur de votre conteneur, le chargement de l’application que vous souhaitez exécuter et le mappage des ports. Le fichier Dockerfile est l’entrée de la commande `docker build`, qui crée l’image.

Créez un répertoire vide et créez le fichier *Dockerfile* (sans extension de fichier). Ajoutez ce qui suit au fichier *Dockerfile* et enregistrez vos modifications :

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Pour plus d'informations, consultez les [références Dockerfile](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-simple-web-application"></a>Créer une application web simple
Créez une application web Flask écoutant le port 80 qui renvoie « Hello World! ».  Dans le même répertoire, créez le fichier *requirements.txt*.  Ajoutez ce qui suit et enregistrez vos modifications :
```
Flask
```

Créez également le fichier *app.py* et ajoutez ce qui suit :

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():

    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Créer l’image
Exécutez la commande `docker build` pour créer l’image qui exécute votre application web. Ouvrez une fenêtre PowerShell et accédez au répertoire contenant le fichier Dockerfile. Exécutez la commande suivante :

```
docker build -t helloworldapp .
```

Cette commande crée la nouvelle image en suivant les instructions de votre fichier Dockerfile, et la nomme (balisage -t) « helloworldapp ». La création d’une image extrait l’image de base de Docker Hub et crée une image qui vient s’ajouter à votre application par-dessus l’image de base.  

Une fois la création terminée, exécutez la commande `docker images` pour afficher des informations sur la nouvelle image :

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Exécution locale de l'application
Vérifiez votre image en local avant de l’envoyer dans le registre de conteneurs.  

Exécutez l'application :

```
docker run -d --name my-web-site helloworldapp
```

*name* donne un nom au conteneur en cours d’exécution (au lieu d’utiliser l’ID de conteneur).

Une fois le conteneur démarré, recherchez son adresse IP pour vous connecter à votre conteneur en cours d’exécution à partir d’un navigateur :
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web qui pointe vers l’adresse IP renvoyée, par exemple « http://172.31.194.61 ». Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

Pour arrêter votre conteneur, exécutez :

```
docker stop my-web-site
```

Supprimez le conteneur de votre ordinateur de développement :

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Envoyer l’image dans le registre de conteneurs
Après avoir vérifié que le conteneur s’exécute sur votre ordinateur de développement, envoyez l’image à votre registre dans Azure Container Registry.

Exécutez ``docker login`` pour vous connecter à votre Registre de conteneur à l’aide de vos [informations d’identification du Registre](../container-registry/container-registry-authentication.md).

L’exemple suivant transmet l’ID et le mot de passe d’un [principal du service](../active-directory/active-directory-application-objects.md) Azure Active Directory . Par exemple, vous pouvez avoir affecté un principal du service à votre Registre pour un scénario d’automatisation. Ou bien, vous pouvez vous connecter à l’aide de votre nom d’utilisateur de registre et mot de passe.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

La commande suivante crée une balise, ou un alias, de l’image, avec un chemin d’accès qualifié complet vers votre registre. Cet exemple place l’image dans l’espace de noms ```samples``` pour éviter un encombrement à la racine du registre.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Envoyez l’image vers votre registre de conteneurs :

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Créer le service en conteneur dans Visual Studio
Le kit de développement logiciel Service Fabric fournit un modèle de service pour vous aider à créer une application en conteneur.

1. Démarrez Visual Studio.  Sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sélectionnez **Service Fabric application** (Application Service Fabric), nommez-la « MyFirstContainer », puis cliquez sur **OK**.
3. Sélectionnez **Container** (Conteneur) dans la liste des **modèles de service**.
4. Sous **Nom de l’image**, entrez « myregistry.azurecr.io/samples/helloworldapp », c’est-à-dire l’image que vous avez envoyée à votre référentiel de conteneurs.
5. Donnez un nom à votre service et cliquez sur **OK**.

## <a name="configure-communication"></a>Configurer la communication
Le service en conteneur a besoin d’un point de terminaison pour la communication. Ajoutez un élément `Endpoint` ainsi que le protocole, le port et le type au fichier ServiceManifest.xml. Dans cet article, le service en conteneur écoute le port 8081 :  Dans cet exemple, un port fixe 8081 est utilisé.  Si aucun port n’est spécifié, un port aléatoire de la plage de ports de l’application est choisi.  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

En définissant un point de terminaison, Service Fabric publie le point de terminaison sur le service d’affectation de noms.  D’autres services qui s’exécutent dans le cluster peuvent résoudre ce conteneur.  Vous pouvez également effectuer la communication de conteneur à conteneur à l’aide du [proxy inversé](service-fabric-reverseproxy.md).  Pour établir la communication, vous devez fournir le port d’écoute HTTP associé au proxy inversé et le nom des services avec lesquels vous souhaitez communiquer en tant que variables d’environnement.

## <a name="configure-and-set-environment-variables"></a>Configurer et définir des variables d’environnement
Il est possible de spécifier des variables d’environnement pour chaque package de code dans le manifeste de service. Cette fonctionnalité est disponible pour tous les services, qu’ils soient déployés sous forme de conteneurs, de processus ou d’exécutables invités. Vous pouvez remplacer les valeurs de variable d’environnement dans le manifeste de l’application, ou les spécifier lors du déploiement, en tant que paramètres d’application.

L’extrait de code XML du manifeste de service suivant illustre la méthode à suivre pour spécifier des variables d’environnement pour un package de code :
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Ces variables d’environnement peuvent être remplacées dans le manifeste de l’application :

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configuration du mappage des ports de conteneur aux ports hôtes et de la détection de conteneur à conteneur
Configurez un port de l’hôte utilisé pour communiquer avec le conteneur. La liaison de port mappe le port sur lequel le service écoute, à l’intérieur du conteneur, à un port sur l’hôte. Ajoutez un élément `PortBinding` dans l’élément `ContainerHostPolicies` du fichier ApplicationManifest.xml.  Dans cet article, `ContainerPort` est 80 (le conteneur expose le port 80, tel que spécifié dans le fichier Dockerfile) et `EndpointRef` est « Guest1TypeEndpoint » (le point de terminaison défini précédemment dans le manifeste de service).  Les demandes entrantes pour le service sur le port 8081 sont mappées au port 80 dans le conteneur.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>Configurer l’authentification au registre de conteneurs Azure
Configurez l’authentification au registre de conteneurs en ajoutant l’élément `RepositoryCredentials` à l’élément `ContainerHostPolicies` du fichier ApplicationManifest.xml. Ajoutez le compte et le mot de passe pour le registre de conteneurs myregistry.azurecr.io. Cela permet au service de télécharger l’image de conteneur à partir du référentiel.

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

Nous vous recommandons de chiffrer le mot de passe du référentiel à l’aide d’un certificat de chiffrement qui est déployé sur tous les nœuds du cluster. Lorsque Service Fabric déploie le package de service sur le cluster, le certificat de chiffrement est utilisé pour déchiffrer le texte chiffré.  Le cmdlet Invoke-ServiceFabricEncryptText est utilisé pour créer le texte chiffré du mot de passe, qui est ensuite ajouté au fichier ApplicationManifest.xml.

Le script suivant crée un nouveau certificat auto-signé et l’exporte vers un fichier PFX.  Le certificat est importé dans un coffre de clés existant, puis est déployé sur le cluster Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Chiffrez le mot de passe à l’aide du cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Remplacez le mot de passe avec le texte chiffré renvoyé par le cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) et définissez `PasswordEncrypted` sur « true ».

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>Configurer le mode d’isolation
Windows prend en charge deux modes d’isolation pour les conteneurs : Processus et Hyper-V. Avec le mode d’isolation Processus, tous les conteneurs s’exécutant sur le même hôte partagent le noyau avec l’hôte. Avec le mode d’isolation Hyper-V, les noyaux sont isolés entre chaque conteneur Hyper-V et l’hôte du conteneur. Le mode d’isolation est défini dans l’élément `ContainerHostPolicies` dans le fichier manifeste de l’application. Les modes d’isolation qui peuvent être définis sont `process`, `hyperv` et `default`. Par défaut, le mode d’isolation est défini sur `process` sur les hôtes Windows Server, et sur `hyperv` sur les hôtes Windows 10. L’extrait de code suivant montre comment le mode d’isolation est spécifié dans le fichier manifeste de l’application.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Le mode d’isolation Hyper-V est disponible pour les références SKU Azure Ev3 et Dv3 qui prennent en charge la virtualisation imbriquée. Assurez-vous que le rôle Hyper-V est installé sur les hôtes. Pour le vérifier, connectez-vous à ces derniers.
   >

## <a name="configure-resource-governance"></a>Configurer la gouvernance des ressources
La [gouvernance des ressources](service-fabric-resource-governance.md) limite les ressources que le conteneur peut utiliser sur l’hôte. L’élément `ResourceGovernancePolicy`, spécifié dans le manifeste de l’application, est utilisé pour déclarer des limites relatives aux ressources pour un package de code de service. Des limites de ressources peuvent être définies pour les ressources suivantes : mémoire, MemorySwap, CpuShares (poids relatif du processeur), MemoryReservationInMB, BlkioWeight (poids relatif de l’élément BlockIO).  Dans cet exemple, le package de service Guest1Pkg obtient un cœur sur les nœuds de cluster où il est placé.  Les limites de mémoire sont absolues, ce qui signifie que le package de code est limité à 1024 Mo de mémoire (avec une garantie de réservation identique). Les packages de code (conteneurs ou processus) ne sont pas en mesure d’allouer plus de mémoire que cette limite. Toute tentative en ce sens conduit à une exception de mémoire insuffisante. Pour pouvoir appliquer la limite de ressources, des limites de mémoire doivent être spécifiées pour tous les packages de code au sein d’un package de service.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```

## <a name="deploy-the-container-application"></a>Déployer l’application de conteneur
Enregistrez toutes les modifications et générez l’application. Pour publier votre application, cliquez avec le bouton droit de la souris sur **MyFirstContainer** dans l’Explorateur de solutions, puis sélectionnez **Publier**.

Dans **Point de terminaison de connexion**, entrez le point de terminaison de gestion pour le cluster.  Par exemple : « containercluster.westus2.cloudapp.azure.com:19000 ». Vous trouverez le point de terminaison de connexion client dans le panneau d’aperçu de votre cluster dans le [portail Azure](https://portal.azure.com).

Cliquez sur **Publier**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) est un outil web dédié à l’inspection et à la gestion d’applications et de nœuds dans un cluster Service Fabric. Ouvrez un navigateur et accédez à http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/, puis suivez le déploiement de l’application.  L’application se déploie mais se trouve dans un état d’erreur jusqu’à ce que l’image soit téléchargée sur les nœuds du cluster (ce qui peut prendre du temps, selon la taille de l’image) : ![Erreur][1]

L’application est prête lorsqu’elle est à l’état ```Ready``` : ![Prête][2]

Ouvrez un navigateur et accédez à http://containercluster.westus2.cloudapp.azure.com:8081. Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

## <a name="clean-up"></a>Nettoyer
Vous continuez à être facturé tant que le cluster est en cours d’exécution. Pensez à [supprimer votre cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md#clean-up-resources).  Les [clusters d’essai](https://try.servicefabric.azure.com/) sont automatiquement supprimés après quelques heures.

Une fois l’image publiée dans le registre de conteneurs, vous pouvez supprimer l’image locale de votre ordinateur de développement :

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemples complets de manifestes d’application et de service Service Fabric
Voici les manifestes d’application et de service complets utilisés dans cet article.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurer l’intervalle de temps d’attente avant l’arrêt forcé du conteneur

Vous pouvez configurer un intervalle de temps d’attente pour le runtime avant que le conteneur ne soit supprimé après le début de la suppression du service (ou d’un déplacement vers un autre nœud). Le fait de configurer l’intervalle de temps envoie la commande `docker stop <time in seconds>` au conteneur.   Pour plus d’informations, consultez [Arrêt du docker](https://docs.docker.com/engine/reference/commandline/stop/). L’intervalle de temps d’attente est spécifié dans la section `Hosting`. L’extrait de manifeste de cluster suivant montre comment définir l’intervalle d’attente :

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
L’intervalle de temps par défaut est défini sur 10 secondes. Étant donné que cette configuration est dynamique, une mise à niveau uniquement de la configuration sur un cluster met à jour le délai d’expiration. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurer le runtime pour supprimer les images conteneur inutilisées

Vous pouvez configurer le cluster Service Fabric pour supprimer des images conteneur inutilisées à partir du nœud. Cette configuration permet à l’espace disque d’être rétabli si trop d’images conteneur sont présentes sur le nœud.  Pour activer cette fonctionnalité, mettez à jour la section `Hosting` du manifeste de cluster, comme indiqué dans l’extrait de code suivant : 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Vous pouvez les spécifier les images qui ne doivent pas être supprimées à l’aide du paramètre `ContainerImagesToSkip`. 



## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-containers-overview.md).
* Consultez le didacticiel [Déployer une application .NET dans un conteneur vers Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
* Consulter les [exemples de code de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) sur GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
