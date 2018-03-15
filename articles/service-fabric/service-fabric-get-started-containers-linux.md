---
title: "Créer une application de conteneur Microsoft Azure Service Fabric sur Linux | Microsoft Docs"
description: "Créez votre première application de conteneur Linux sur Microsoft Azure Service Fabric.  Concevez une image Docker avec votre application, envoyez l’image vers un registre de conteneurs, créez et déployez une application de conteneur Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: 0e7e0f1262ee8c31bc6e71b49e9ef62129887f2c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Créer votre première application de conteneur Service Fabric sur Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

L’exécution d’une application existante dans un conteneur Linux sur un cluster Service Fabric ne nécessite aucune modification de votre application. Cet article vous accompagne dans la création d’une image Docker contenant une application web [Flask](http://flask.pocoo.org/) Python et le déploiement dans un cluster Service Fabric.  Vous allez également partager votre application en conteneur via [Azure Container Registry](/azure/container-registry/).  Cet article suppose une connaissance élémentaire de Docker. Pour en savoir plus sur Docker, consultez la [présentation de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Prérequis
* Un ordinateur de développement exécutant :
  * [Outils et SDK Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE pour Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interface de ligne de commande de Service Fabric](service-fabric-cli.md)

* Un registre dans Azure Container Registry. [Créez un registre de conteneurs](../container-registry/container-registry-get-started-portal.md) dans votre abonnement Azure. 

## <a name="define-the-docker-container"></a>Définir le conteneur Docker
Créez une image basée sur [l’image Python](https://hub.docker.com/_/python/) située sur Docker Hub. 

Définissez votre conteneur Docker dans un fichier Dockerfile. Le fichier Dockerfile contient des instructions pour la configuration de l’environnement à l’intérieur de votre conteneur, le chargement de l’application que vous souhaitez exécuter et le mappage des ports. Le fichier Dockerfile est l’entrée de la commande `docker build`, qui crée l’image. 

Créez un répertoire vide et créez le fichier *Dockerfile* (sans extension de fichier). Ajoutez ce qui suit au fichier *Dockerfile* et enregistrez vos modifications :

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
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

## <a name="build-the-image"></a>Créer l’image
Exécutez la commande `docker build` pour créer l’image qui exécute votre application web. Ouvrez une fenêtre PowerShell et accédez à *c:\temp\helloworldapp*. Exécutez la commande suivante :

```bash
docker build -t helloworldapp .
```

Cette commande crée la nouvelle image en suivant les instructions de votre fichier Dockerfile, et la nomme (balisage -t) « helloworldapp ». La création d’une image extrait l’image de base de Docker Hub et crée une image qui vient s’ajouter à votre application par-dessus l’image de base.  

Une fois la création terminée, exécutez la commande `docker images` pour afficher des informations sur la nouvelle image :

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Exécuter l’application localement
Vérifiez que votre application en conteneur s’exécute en local avant de l’envoyer dans le registre de conteneurs.  

Exécutez l’application, en mappant le port 4000 de votre ordinateur au port 80 exposé du conteneur :

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* donne un nom au conteneur en cours d’exécution (au lieu d’utiliser l’ID de conteneur).

Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web qui pointe vers l’adresse IP renvoyée sur le port 4000, par exemple « http://localhost:4000 ». Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

![Hello World!][hello-world]

Pour arrêter votre conteneur, exécutez :

```bash
docker stop my-web-site
```

Supprimez le conteneur de votre ordinateur de développement :

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Envoyer l’image dans le registre de conteneurs
Après avoir vérifié que l’application s’exécute dans Docker, envoyez l’image à votre registre dans Azure Container Registry.

Exécutez `docker login` pour vous connecter à votre Registre de conteneur à l’aide de vos [informations d’identification du Registre](../container-registry/container-registry-authentication.md).

L’exemple suivant transmet l’ID et le mot de passe d’un [principal du service](../active-directory/active-directory-application-objects.md) Azure Active Directory . Par exemple, vous pouvez avoir affecté un principal du service à votre Registre pour un scénario d’automatisation.  Ou bien, vous pouvez vous connecter à l’aide de votre nom d’utilisateur de registre et mot de passe.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

La commande suivante crée une balise, ou un alias, de l’image, avec un chemin d’accès qualifié complet vers votre registre. Cet exemple place l’image dans l’espace de noms `samples` pour éviter un encombrement à la racine du registre.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Envoyez l’image vers votre registre de conteneurs :

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Placer l’image Docker dans un package avec Yeoman
Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre application et l’ajout d’une image de conteneur. Nous allons utiliser Yeoman pour créer une application avec un seul conteneur Docker appelé *SimpleContainerApp*.

Pour créer une application de conteneur Service Fabric, ouvrez une fenêtre de terminal et exécutez `yo azuresfcontainer`.  

Nommez votre application (par exemple, « mycontainer » et le service d’application (par exemple, « myservice »).

En guise de nom d’image, fournissez l’URL de l’image de conteneurs dans un registre de conteneur (ex : « myregistry.azurecr.io/samples/helloworldapp »). 

Cette image possède un point d’entrée de charge de travail défini, vous n’avez donc pas à spécifier des commandes d’entrée explicitement (les commandes s’exécutent dans le conteneur et garderont le conteneur en exécution après le démarrage). 

Spécifiez un nombre d’instances de « 1 ».

![Générateur Yeoman Service Fabric pour les conteneurs][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Configurer l’authentification de référentiel de conteneur et le mappage de port
Votre service en conteneur a besoin d’un point de terminaison pour la communication.  Ajoutez ensuite le protocole, le port et le type à un `Endpoint` dans le fichier ServiceManifest.xml sous la balise « Ressources ». Dans cet article, le service en conteneur écoute le port 4000 : 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
Fournir `UriScheme` enregistre automatiquement le point de terminaison du conteneur avec le service Service Fabric Naming pour la découverte. Un exemple de fichier ServiceManifest.xml complet est fourni à la fin de cet article. 

Configurez le mappage port/hôte du conteneur en spécifiant une stratégie `PortBinding` dans `ContainerHostPolicies` dans le fichier ApplicationManifest.xml.  Dans cet article, `ContainerPort` correspond à 80 (le conteneur expose le port 80, tel que spécifié dans le fichier Dockerfile) et `EndpointRef` correspond à « myserviceTypeEndpoint » (le point de terminaison défini dans le manifeste de service).  Les demandes entrantes pour le service sur le port 4000 sont mappées au port 80 dans le conteneur.  Si votre conteneur doit s’authentifier auprès d’un référentiel privé, ajoutez `RepositoryCredentials`.  Pour cet article, ajoutez le nom de compte et le mot de passe du registre de conteneurs myregistry.azurecr.io. Assurez-vous que la stratégie est ajoutée sous la balise « ServiceManifestImport » correspondant au package de service approprié.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 
## <a name="configure-docker-healthcheck"></a>Configurer le docker HEALTHCHECK 
En démarrant la version 6.1, Service Fabric intègre automatiquement les événements [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) à son rapport d’intégrité du système. Cela signifie que si **HEALTHCHECK** est activé dans votre conteneur, Service Fabric générera un rapport d’intégrité chaque fois que l’état d’intégrité du conteneur changera comme indiqué par Docker. Un rapport d’intégrité **OK** apparaît dans [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lorsque *health_status* est *intègre* et  **AVERTISSEMENT** s’affiche lorsque *health_status* est *défectueux*. L’instruction **HEALTHCHECK** qui pointe vers la vérification réalisée pour surveiller l’intégrité du conteneur doit être présente dans le fichier **dockerfile** utilisé lors de la génération de l’image de conteneur. 

![HealthCheckHealthy][1]

![HealthCheckUnealthyApp][2]

![HealthCheckUnhealthyDsp][3]

Vous pouvez configurer un comportement **HEALTHCHECK** pour chaque conteneur en spécifiant les options **HealthConfig** dans **ContainerHostPolicies** dans ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Par défaut *IncludeDockerHealthStatusInSystemHealthReport* est défini sur **true** et *RestartContainerOnUnhealthyDockerHealthStatus* est défini sur **false**. Si *RestartContainerOnUnhealthyDockerHealthStatus* est défini sur **true**, un conteneur déclaré défectueux à plusieurs reprises est redémarré (éventuellement sur d’autres nœuds).

Si vous souhaitez désactiver l’intégration **HEALTHCHECK** pour l’ensemble du cluster Service Fabric, vous devez définir [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) sur **false**.

## <a name="build-and-package-the-service-fabric-application"></a>Créer et placer l’application Service Fabric dans un package
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal. Pour générer et placer l’application dans un package, exécutez ce qui suit :

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Déployer l’application
Une fois que l’application est générée, vous pouvez la déployer vers le cluster local à l’aide de l’interface de ligne de commande Service Fabric.

Connectez-vous au cluster Service Fabric local.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Utilisez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

```bash
./install.sh
```

Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://localhost:19080/Explorer (remplacez localhost par l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X). Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web qui pointe vers l’adresse IP renvoyée sur le port 4000, par exemple « http://localhost:4000 ». Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

![Hello World!][hello-world]


## <a name="clean-up"></a>Nettoyer
Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance de l’application du cluster de développement local et annuler l’inscription du type d’application.

```bash
./uninstall.sh
```

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
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Ajout d’autres services à une application existante

Pour ajouter un autre service de conteneur à une application déjà créée à l’aide de Yeoman, procédez comme suit :

1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfcontainer:AddService`.

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurer l’intervalle de temps d’attente avant l’arrêt forcé du conteneur

Vous pouvez configurer un intervalle de temps d’attente pour le runtime avant que le conteneur ne soit supprimé après le début de la suppression du service (ou d’un déplacement vers un autre nœud). Le fait de configurer l’intervalle de temps envoie la commande `docker stop <time in seconds>` au conteneur.   Pour plus d’informations, consultez [Arrêt du docker](https://docs.docker.com/engine/reference/commandline/stop/). L’intervalle de temps d’attente est spécifié dans la section `Hosting`. L’extrait de manifeste de cluster suivant montre comment définir l’intervalle d’attente :


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

L’intervalle de temps par défaut est défini sur 10 secondes. Étant donné que cette configuration est dynamique, une mise à niveau uniquement de la configuration sur un cluster met à jour le délai d’expiration. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurer le runtime pour supprimer les images conteneur inutilisées

Vous pouvez configurer le cluster Service Fabric pour supprimer des images conteneur inutilisées à partir du nœud. Cette configuration permet à l’espace disque d’être rétabli si trop d’images conteneur sont présentes sur le nœud.  Pour activer cette fonctionnalité, mettez à jour la section `Hosting` du manifeste de cluster, comme indiqué dans l’extrait de code suivant : 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Vous pouvez les spécifier les images qui ne doivent pas être supprimées à l’aide du paramètre `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Configurer le temps de téléchargement de l’image de conteneur

Par défaut, le runtime Service Fabric alloue un délai de 20 minutes pour télécharger et extraire les images de conteneur, qui fonctionne pour la majorité des images de conteneur. Pour les images de grande taille, ou lorsque la connexion réseau est lente, il peut être nécessaire d’augmenter le temps d’attente avant d’annuler le téléchargement et l’extraction de l’image. Cela peut être défini à l’aide de l’attribut **ContainerImageDownloadTimeout** dans la section **Hébergement** du manifeste de cluster, comme indiqué dans l’extrait de code suivant :

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Définir la stratégie de rétention de conteneur

Pour aider à diagnostiquer les échecs de démarrage du conteneur, Service Fabric (version 6.1 ou supérieure) prend en charge les conteneurs conservés qui ont terminé ou échoué leur démarrage. Cette stratégie peut être définie dans le fichier **ApplicationManifest.xml** comme indiqué dans l’extrait de code suivant :

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Le paramètre **ContainersRetentionCount** spécifie le nombre de conteneurs à conserver en cas d’échec. Si une valeur négative est spécifiée, tous les conteneurs en échec sont conservés. Quand l’attribut **ContainersRetentionCount** n’est pas spécifié, aucun conteneur n’est conservé. L’attribut **ContainersRetentionCount** prend également en charge les paramètres de l’application. Les utilisateurs peuvent ainsi spécifier des valeurs différentes pour les clusters de test et de production. Il est recommandé d’utiliser des contraintes de placement pour cibler le service de conteneur sur un nœud particulier lors de l’utilisation de cette fonctionnalité pour empêcher le service de conteneur de se déplacer vers d’autres nœuds. Tous les conteneurs conservés à l’aide de cette fonctionnalité doivent être supprimés manuellement.


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-containers-overview.md).
* Consultez le didacticiel [Déployer une application .NET dans un conteneur vers Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
* Consulter les [exemples de code de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) sur GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
