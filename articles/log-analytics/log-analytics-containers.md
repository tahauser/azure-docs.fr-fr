---
title: Solution Container Monitoring d’Azure Log Analytics | Microsoft Docs
description: La solution Container Monitoring de Log Analytics vous aide à afficher et à gérer vos hôtes de conteneur Docker et Windows dans un emplacement unique.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: magoedte
ms.openlocfilehash: 6d2c85225ab74c912183a0bb8d7f100d1354e6c5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Solution Container Monitoring dans Log Analytics

![Symbole Containers](./media/log-analytics-containers/containers-symbol.png)

Cet article explique comment configurer et utiliser la solution Container Monitoring de Log Analytics, qui vous permet d’afficher et de gérer vos hôtes de conteneur Docker et Windows dans un emplacement unique. Docker est un système de virtualisation logicielle utilisé pour créer des conteneurs qui automatisent le déploiement de logiciels dans leur infrastructure informatique.

La solution montre les conteneurs qui sont actuellement exécutés, l’image conteneur qu’ils exécutent et où ils s’exécutent. Vous pouvez afficher des informations d’audit détaillées montrant les commandes utilisées avec les conteneurs. Vous pouvez résoudre des problèmes de conteneurs en consultant des journaux centralisés et en y effectuant des recherches sans devoir afficher à distance les hôtes Docker ou Windows. Vous pouvez rechercher des conteneurs bruyants et consommant des ressources excessives sur un ordinateur hôte. Et vous pouvez consulter des informations centralisées sur le processeur, la mémoire, le stockage ainsi que l’utilisation et les performances du réseau. Sur les ordinateurs exécutant Windows, vous pouvez centraliser et comparer les journaux des conteneurs Windows Server, Hyper-V et Docker. La solution prend en charge les orchestrateurs de conteneur suivants :

- Docker Swarm
- DC/OS
- kubernetes
- Service Fabric
- Red Hat OpenShift


Le schéma suivant illustre les relations entre les différents hôtes de conteneurs et agents dans OMS.

![Schéma des conteneurs](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Configuration requise et plateformes prises en charge

Avant de commencer, prenez connaissance des informations suivantes pour vérifier que les conditions préalables sont remplies.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Prise en charge de solution de surveillance de conteneur pour la plateforme Docker Orchestrator et celle du système d’exploitation
Le tableau suivant présente l’orchestration de Docker et la prise en charge de la surveillance du système d’exploitation pour ce qui est des journaux, de la performance et de l’inventaire du conteneur avec Log Analytics.   

| | ACS | Linux | Windows | Conteneur<br>Inventaire | Image<br>Inventaire | Nœud<br>Inventaire | Conteneur<br>Performances | Conteneur<br>Événement | Événement<br>Journal | Conteneur<br>Journal |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mésosphère<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| de diffusion en continu<br>Structure | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autonome) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serveur Linux<br>(autonome) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Versions de Docker prises en charge sur Linux

- Docker 1.11 à 1.13
- Docker CE et EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Distributions Linux x64 suivantes prises en charge en tant qu’hôtes de conteneur

- Ubuntu 14.04 LTS et 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 et 7.3
- SLES 12
- RHEL 7.2 et 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 et 3.5
- ACS Mesosphere DC/OS 1.7.3 à 1.8.8
- ACS Kubernetes 1.4.5 à 1.6
    - Les événements Kubernetes, l’inventaire Kubernetes et les processus de conteneur sont uniquement pris en charge par l’Agent OMS pour Linux 1.4.1-45 et versions ultérieures.
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Système d’exploitation Windows pris en charge

- Windows Server 2016
- Édition Anniversaire Windows 10 (Professionnel ou Entreprise)

### <a name="docker-versions-supported-on-windows"></a>Versions de docker prises en charge sur Windows

- Docker 1.12 et 1.13
- Docker 17.03.0 et versions ultérieures

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

1. Ajoutez la solution Container Monitoring à votre espace de travail OMS depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

2. Installez et utilisez Docker avec un agent OMS. Pour configurer votre agent, vous pouvez utiliser les méthodes suivantes, selon votre système d’exploitation et votre orchestrateur Docker.
  - Pour les hôtes autonomes :
    - Sur les systèmes d’exploitation Linux pris en charge, installez et exécutez Docker, puis installez et configurez [l’Agent OMS pour Linux](log-analytics-agent-linux.md).  
    - Sur CoreOS, vous ne pouvez pas exécuter l’Agent OMS pour Linux. Au lieu de cela, vous exécutez une version en conteneur de l’Agent OMS pour Linux. Si vous utilisez des conteneurs dans Azure Government Cloud, consultez les sections relatives aux [hôtes de conteneurs Linux, y compris CoreOS](#for-all-linux-container-hosts-including-coreos) ou aux [hôtes de conteneurs Linux Azure Government, y compris CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos).
    - Sur Windows Server 2016 et Windows 10, installez le moteur et le client Docker, puis connectez un agent afin de collecter les données et les transmettre à Log Analytics. Consultez [Installer et configurer des hôtes de conteneur Windows](#install-and-configure-windows-container-hosts) si vous avez un environnement Windows.
  - Pour l’orchestration Docker à plusieurs hôtes :
    - Si vous disposez d’un environnement Red Hat OpenShift, consultez [Configurer un agent OMS pour Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Si vous avez un cluster Kubernetes utilisant Azure Container Service :
       - Voir [Configurer un agent OMS Linux pour Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Voir [Configurer un agent OMS Windows pour Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Voir [Utiliser Helm pour déployer l’agent OMS sur Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Si vous possédez un cluster DC/OS Azure Container Service, consultez l’article [Surveiller un cluster DC/OS Azure Container Service avec Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Si vous avez un environnement en mode Docker Swarm, apprenez en plus en lisant [Configurer un agent OMS pour Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Si vous avez un cluster Service Fabric, découvrez-en plus dans [Surveiller les conteneurs avec OMS Log Analytics](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Examinez l’article relatif au [moteur Docker sur Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) pour en savoir plus sur l’installation et la configuration de vos moteurs Docker sur les ordinateurs exécutant Windows.

> [!IMPORTANT]
> Docker doit être en cours d’exécution **avant** l’installation de l’[Agent OMS pour Linux](log-analytics-agent-linux.md) sur vos hôtes de conteneur. Si vous avez installé l’agent avant d’installer Docker, vous devez réinstaller l’agent OMS pour Linux. Pour plus d’informations sur Docker, voir le [site web Docker](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Installer et configurer des hôtes de conteneur Linux

Après avoir installé Docker, utilisez les paramètres suivants pour votre hôte de conteneur afin de configurer l’agent en vue d’une utilisation avec Docker. Tout d’abord, vous avez besoin de l’ID et de la clé de votre espace de travail OMS, qui se trouvent dans le portail Azure. Dans votre espace de travail, cliquez sur **Démarrage rapide** > **Ordinateurs** pour afficher votre **ID d’espace de travail** et votre **Clé primaire**.  Copiez-collez ces deux valeurs dans votre éditeur favori.

**Pour tous les hôtes de conteneur Linux, à l’exception de CoreOS :**

- Pour plus d’informations sur l’installation de l’agent OMS pour Linux, consultez [Connecter des ordinateurs Linux à Operations Management Suite (OMS)](log-analytics-agent-linux.md).

**Pour tous les hôtes de conteneur Linux, avec CoreOS :**

Démarrez le conteneur OMS que vous souhaitez analyser. Modifiez et utilisez l’exemple suivant :

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pour tous les hôtes de conteneur Linux Azure Government, y compris CoreOS :**

Démarrez le conteneur OMS que vous souhaitez analyser. Modifiez et utilisez l’exemple suivant :

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Passage de l’utilisation d’un agent Linux installé à un agent exécuté dans un conteneur**

Si vous utilisiez précédemment l’agent directement installé et souhaitez désormais utiliser un agent qui s’exécute dans un conteneur, vous devez commencer par supprimer l’agent OMS pour Linux. Pour comprendre comment désinstaller l’agent, consultez [Désinstallation de l’Agent OMS pour Linux](log-analytics-agent-linux.md).  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Configurer un agent OMS pour Docker Swarm

Vous pouvez exécuter l’agent OMS en tant que service global sur Docker Swarm. Utilisez les informations suivantes pour créer un service d’agent OMS. Vous devez insérer l’ID de votre espace de travail OMS et votre clé primaire.

- Exécutez la commande suivante sur le nœud principal.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Sécuriser les secrets pour Docker Swarm

Pour Docker Swarm, une fois que vous avez créé le secret de l’ID de l’espace de travail et de la clé primaire, utilisez les informations suivantes pour créer vos informations confidentielles.

1. Exécutez la commande suivante sur le nœud principal.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Vérifiez que les secrets ont été créés correctement.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Exécutez la commande suivante pour monter les secrets sur l’agent OMS en conteneur.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Configurer un agent OMS pour Red Hat OpenShift
Il existe trois façons d’ajouter l’agent OMS pour Red Hat OpenShift dans le but de démarrer la collecte des données de surveillance des conteneurs.

* [Installer l’agent OMS pour Linux](log-analytics-agent-linux.md) directement sur chaque nœud OpenShift  
* [Activer l’extension de machine virtuelle Log Analytics](log-analytics-azure-vm-extension.md) sur chaque nœud OpenShift résidant dans Azure  
* Installer l’agent OMS comme un daemon-set OpenShift  

Dans cette section, nous allons aborder les étapes nécessaires à l’installation de l’agent OMS comme un daemon-set OpenShift.  

1. Connectez-vous au nœud principal OpenShift, puis copiez le fichier yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) de GitHub vers votre nœud principal. Ensuite, remplacez la valeur par l’ID de votre espace de travail OMS et votre clé primaire.
2. Exécutez les commandes suivantes pour créer un projet OMS et configurer le compte d’utilisateur.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Pour déployer le daemon-set, exécutez la commande suivante :

    `oc create -f ocp-omsagent.yaml`

5. Pour vérifier qu’il est configuré et fonctionne correctement, tapez la commande suivante :

    `oc describe daemonset omsagent`  

    La sortie doit ressembler à ceci :

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Si vous souhaitez utiliser des secrets pour sécuriser l’ID de votre espace de travail OMS et votre clé primaire lorsque vous utilisez le fichier yaml daemon-set de l’agent OMS, effectuez les étapes suivantes :

1. Connectez-vous au nœud principal OpenShift, puis copiez le fichier yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) et le script de génération de secrets [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) à partir de GitHub.  Ce script va générer le fichier yaml de secrets pour l’ID d’espace de travail OMS et la clé primaire afin de sécuriser vos informations secrètes.  
2. Exécutez les commandes suivantes pour créer un projet OMS et configurer le compte d’utilisateur. Le script de génération de secrets vous demande de fournir l’ID d’espace de travail OMS <WSID> et la clé primaire <KEY> afin de créer le fichier ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Déployez le fichier de secret en exécutant la commande suivante :

    `oc create -f ocp-secret.yaml`

5. Vérifiez le déploiement en exécutant la commande suivante :

    `oc describe secret omsagent-secret`  

    La sortie doit ressembler à ceci :  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Déployer le fichier yaml daemon-set de l’agent OMS en exécutant la commande suivante :

    `oc create -f ocp-ds-omsagent.yaml`  

7. Vérifiez le déploiement en exécutant la commande suivante :

    `oc describe ds oms`

    La sortie doit ressembler à ceci :

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Configurer un agent OMS Linux pour Kubernetes

Pour Kubernetes, vous utilisez un script afin de générer le fichier yaml de secrets pour votre ID d’espace de travail et votre clé primaire pour installer l’agent OMS pour Linux. Sur la page [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) (GitHub Kubernetes Docker OMS), il existe des fichiers que vous pouvez utiliser avec ou sans vos informations secrètes.

- Le DaemonSet par défaut de l’agent OMS pour Linux ne comprend pas d’informations secrètes (omsagent.yaml).
- Le fichier yaml DaemonSet de l’agent OMS pour Linux utilise les informations secrètes (omsagent-ds-secrets.yaml) avec des scripts de génération de secrets pour générer le fichier yaml de secrets (omsagentsecret.yaml).

Vous pouvez choisir de créer le DaemonSet de l’agent OMS avec ou sans secrets.

**Fichier yaml DaemonSet par défaut de l’agent OMS sans secrets**

- Pour le fichier yaml par défaut DaemonSet de l’agent OMS, remplacez `<WSID>` et `<KEY>` par votre ID d’espace de travail et clé. Copiez le fichier sur votre nœud principal et exécutez la commande suivante :

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Fichier yaml DaemonSet par défaut de l’agent OMS avec secrets**

1. Pour utiliser le DaemonSet de l’agent OMS à l’aide des informations secrètes, créez d’abord les secrets.
    1. Copiez le fichier de modèle de secret et le script, et assurez-vous qu’ils se trouvent dans le même répertoire.
        - Script de génération de secrets - secret-gen.sh
        - Modèle de secret - secret-template.yaml
    2. Exécutez le script, comme l’exemple suivant. Le script demande l’ID d’espace de travail OMS et la clé primaire, et une fois que vous les entrez, le script crée un fichier .yaml de secrets que vous pouvez exécuter.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Créez le pod de secrets en exécutant la commande suivante :
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Pour vérifier, exécutez la commande suivante :

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        La sortie doit ressembler à :

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        La sortie doit ressembler à :

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Créer votre DaemonsSet de l’agent OMS en exécutant ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Vérifiez que le DaemonSet de l’agent OMS s’exécute, comme ce qui suit :

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Pour Kubernetes, utilisez un script afin de générer le fichier yaml de secrets pour l’ID d’espace de travail et la clé primaire pour l’agent OMS pour Linux. Utilisez les informations de l’exemple suivant avec le [fichier yaml de l’agent OMS](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) pour sécuriser vos informations secrètes.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-an-oms-windows-agent-for-kubernetes"></a>Configurer un agent OMS pour Kubernetes
Pour Windows Kubernetes, vous utilisez un script afin de générer le fichier yaml de secrets pour votre ID d’espace de travail et votre clé primaire pour installer l’agent OMS. Sur la page [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) (GitHub Kubernetes Docker OMS), il existe des fichiers que vous pouvez utiliser avec vos informations secrètes.  Vous devez installer l’agent OMS séparément pour les nœuds principaux et d’agent.  

1. Pour utiliser le DaemonSet de l’agent OMS à l’aide des informations secrètes sur le nœud principal, commencez par vous connecter et par créer les secrets.
    1. Copiez le fichier de modèle de secret et le script, et assurez-vous qu’ils se trouvent dans le même répertoire.
        - Script de génération de secrets - secret-gen.sh
        - Modèle de secret - secret-template.yaml

    2. Exécutez le script, comme l’exemple suivant. Le script demande l’ID d’espace de travail OMS et la clé primaire, et une fois que vous les entrez, le script crée un fichier .yaml de secrets que vous pouvez exécuter.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Créer votre DaemonsSet de l’agent OMS en exécutant ``` kubectl create -f omsagentsecret.yaml ```
    4. Pour vérifier, exécutez la commande suivante :

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        La sortie doit ressembler à :

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Créer votre DaemonsSet de l’agent OMS en exécutant ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Vérifiez que le DaemonSet de l’agent OMS s’exécute, comme ce qui suit :

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Pour installer l’agent sur le nœud Worker qui exécute Windows, suivez les étapes décrites dans la section [Installer et configurer des hôtes de conteneur Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Utiliser Helm pour déployer l’agent OMS sur Linux Kubernetes
Pour utiliser Helm afin de déployer l’agent OMS sur votre environnement Linux Kubernetes, procédez comme suit.

1. Créer votre DaemonsSet de l’agent OMS en exécutant ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Les résultats ressemblent à ce qui suit :

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Vous pouvez vérifier l’état de l’agent OMS en exécutant : ```helm status "omsagent"```. Le résultat doit ressembler à ce qui suit :

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Pour plus d’informations, consultez [Azure Log Analytics Container Monitoring Solution](https://aka.ms/omscontainerhelm) (Solution de surveillance de conteneur Azure Log Analytics).

### <a name="install-and-configure-windows-container-hosts"></a>Installer et configurer des hôtes de conteneur Windows

Utilisez les informations de cette section pour installer et configurer des hôtes de conteneur Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Préparation préalable à l’installation des agents Windows

Avant d’installer les agents sur les ordinateurs exécutant Windows, vous devez configurer le service Docker. La configuration permet à l’agent Windows ou à l’extension de machine virtuelle Log Analytics d’utiliser le socket Docker TCP afin d’autoriser les agents à accéder à distance au démon Docker et de collecter les données pour la surveillance.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Pour démarrer Docker et vérifier sa configuration

Voici les étapes nécessaires à la configuration du canal nommé TCP pour Windows Server :

1. Dans Windows PowerShell, activez les canaux TCP et nommé.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Configurez Docker à l’aide du fichier de configuration pour le canal TCP et le canal nommé. Le fichier de configuration se trouve à l’emplacement suivant : C:\ProgramData\docker\config\daemon.json.

    Dans le fichier daemon.json, vous aurez besoin des éléments suivants :

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Pour plus d’informations sur la configuration du démon Docker utilisée avec les conteneurs de Windows, reportez-vous à [Moteur Docker sur Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Installer les agents Windows

Pour activer la surveillance des conteneurs Windows et Hyper-V, installez Microsoft Monitoring Agent (MMA) sur les ordinateurs Windows qui sont des hôtes de conteneurs. Pour les ordinateurs exécutant Windows dans votre environnement local, consultez la page [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agent.md). Connectez les machines virtuelles exécutées dans Azure à Log Analytics à l’aide de l’[extension de machine virtuelle](log-analytics-azure-vm-extension.md).

Vous pouvez surveiller les conteneurs Windows en cours d’exécution sur Service Fabric. Toutefois, seules les [machines virtuelles qui s’exécutent dans Azure](log-analytics-azure-vm-extension.md) et les [ordinateurs exécutant Windows dans votre environnement local](log-analytics-windows-agent.md) sont actuellement pris en charge pour Service Fabric.

Vous pouvez vérifier que la solution Container Monitoring est correctement configurée pour Windows. Pour vérifier que le pack d’administration a été correctement téléchargé, recherchez *ContainerManagement.xxx*. Les fichiers doivent se trouver dans le dossier C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.


## <a name="solution-components"></a>Composants de la solution

Si vous utilisez des agents Windows, le pack d’administration suivant est installé sur chaque ordinateur où se trouve un agent lorsque vous ajoutez cette solution. Le pack d’administration ne nécessite aucune opération de configuration ou de maintenance.

- *ContainerManagement.xxx* installé dans le dossier C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Détails sur la collecte de données des conteneurs
La solution Container Monitoring collecte diverses métriques de performances et données de journaux à partir des hôtes de conteneur et des conteneurs utilisant les agents que vous avez activés.

Les données sont collectées toutes les trois minutes par les types d’agents suivants.

- [Agent OMS pour Linux](log-analytics-linux-agents.md)
- [Agent Windows](log-analytics-windows-agent.md)
- [Extension de machine virtuelle Log Analytics](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par la solution Container Monitoring, ainsi que les types de données qui s’affichent dans les résultats de recherche des journaux.

| Type de données | Type de données dans Recherche de journaux | Champs |
| --- | --- | --- |
| Performances des hôtes et des conteneurs | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventaire de conteneur | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventaire d’image de conteneur | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Journal de conteneur | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Journal de service de conteneur | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventaire du nœud de conteneur | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaire de Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processus de conteneur | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Événements Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Les étiquettes ajoutées aux types de données *PodLabel* sont vos propres étiquettes personnalisées. Les étiquettes PodLabel ajoutées contenues dans le tableau sont des exemples. Par conséquent, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` seront différentes dans le jeu de données de votre environnement et ressembleront à `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Analyser les conteneurs
Une fois la solution activée dans le portail OMS, vous voyez la vignette **Containers** qui contient des informations récapitulatives sur vos hôtes de conteneur et les conteneurs s’exécutant dans les hôtes.

![Vignette Conteneurs](./media/log-analytics-containers/containers-title.png)

La vignette affiche une vue d’ensemble du nombre de conteneurs présents dans l’environnement, et indique s’ils sont en échec, en cours d’exécution ou arrêtés.

### <a name="using-the-containers-dashboard"></a>Utilisation du tableau de bord Conteneurs
Cliquez sur la vignette **Conteneurs**. À partir de là, vous voyez les vues organisées par :

- **Événements de conteneur** : affiche l’état du conteneur et les ordinateurs dont le conteneur a échoué.
- **Journaux de conteneur** : affiche un graphique montrant les fichiers journaux de conteneur générés jusqu’à maintenant, ainsi que la liste des ordinateurs contenant le plus grand nombre de fichiers journaux.
- **Événements Kubernetes** : affiche un graphique montrant les événements Kubernetes générés jusqu’à maintenant, ainsi que la liste des raisons pour lesquelles les pods ont généré ces événements. *Ce jeu de données est utilisé uniquement dans les environnements Linux.*
- **Inventaire des espaces de noms Kubernetes** : indique le nombre d’espaces de noms et de pods, et montre leur hiérarchie. *Ce jeu de données est utilisé uniquement dans les environnements Linux.*
- **Inventaire des nœuds de conteneur** : affiche le nombre de types d’orchestration utilisés sur les nœuds/hôtes de conteneur. Les nœuds/hôtes d’ordinateur sont également répertoriés avec le nombre de conteneurs. *Ce jeu de données est utilisé uniquement dans les environnements Linux.*
- **Inventaire des images de conteneur** : affiche le nombre total des images de conteneur utilisées, ainsi que le nombre de types d’images. Le nombre d’images est également répertorié avec la balise d’image.
- **État des conteneurs** : affiche le nombre total de nœuds de conteneur/ordinateurs hôtes comprenant des conteneurs en cours d’exécution. Les ordinateurs sont également répertoriés avec le nombre d’ordinateurs hôtes en cours d’exécution.
- **Processus de conteneur** : affiche un graphique en courbes représentant les processus de conteneur exécutés jusqu’à maintenant. Les conteneurs peuvent également être répertoriés en exécutant la commande /process dans les conteneurs. *Ce jeu de données est utilisé uniquement dans les environnements Linux.*
- **Performances de l’UC du conteneur** : affiche un graphique en courbes représentant l’utilisation moyenne de l’UC jusqu’à maintenant pour les nœuds/hôtes d’ordinateur. Répertorie également les nœuds/hôtes d’ordinateur en fonction de l’utilisation moyenne de l’UC.
- **Performances de la mémoire du conteneur** : affiche un graphique en courbes représentant l’utilisation de la mémoire jusqu’à maintenant. Répertorie également l’utilisation de la mémoire par l’ordinateur en fonction du nom de l’instance.
- **Performances de l’ordinateur** : affiche des graphiques en courbes représentant le pourcentage de performance de l’UC, le pourcentage d’utilisation de la mémoire et les mégaoctets d’espace disque libre dans le temps. Vous pouvez pointer sur une ligne du graphique pour afficher plus de détails.


Chaque zone du tableau de bord est une représentation visuelle d’une recherche exécutée sur des données collectées.

![Tableau de bord Conteneurs](./media/log-analytics-containers/containers-dash01.png)

![Tableau de bord Conteneurs](./media/log-analytics-containers/containers-dash02.png)

Dans la zone **État du conteneur**, cliquez sur la zone supérieure, comme illustré ci-dessous.

![État des conteneurs](./media/log-analytics-containers/containers-status.png)

La fenêtre Recherche dans les journaux s’ouvre et affiche des informations sur l’état de vos conteneurs.

![Recherche de journal pour les conteneurs](./media/log-analytics-containers/containers-log-search.png)

À partir d’ici, vous pouvez modifier la requête de recherche de façon à trouver les informations spécifiques qui vous intéressent. Pour plus d’informations sur les recherches dans les journaux, voir [Recherches de journal dans Log Analytics](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Résoudre des problèmes en recherchant un conteneur en échec

Log Analytics marque un conteneur comme étant en **Échec** s’il a été fermé avec un code de sortie autre que zéro. Vous pouvez consulter un aperçu des erreurs et des échecs de l’environnement dans la zone **Conteneurs défectueux**.

### <a name="to-find-failed-containers"></a>Pour rechercher les conteneurs défectueux
1. Cliquez sur la zone **État du conteneur**.  
   ![État des conteneurs](./media/log-analytics-containers/containers-status.png)
2. La fenêtre Recherche dans les journaux s’ouvre et affiche l’état de vos conteneurs, comme ci-dessous.  
   ![État des conteneurs](./media/log-analytics-containers/containers-log-search.png)
3. Ensuite, cliquez sur la valeur agrégée des conteneurs en échec pour afficher des informations supplémentaires. Développez **Afficher plus** pour afficher l’ID de l’image.  
   ![Conteneurs défectueux](./media/log-analytics-containers/containers-state-failed.png)  
4. Ensuite, tapez ce qui suit dans la requête de recherche. `ContainerInventory <ImageID>` pour afficher des détails sur l’image, tels que sa taille ou le nombre d’images arrêtées et en échec.  
   ![Conteneurs défectueux](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Rechercher des données de conteneur dans les journaux
Lorsque vous résolvez une erreur spécifique, il peut être utile de voir l’emplacement où elle se produit dans votre environnement. Les types de journaux suivants vous aident à créer des requêtes qui retournent les informations souhaitées.


- **ContainerImageInventory** : utilisez ce type de journal lorsque vous recherchez des informations organisées par image, et de consulter des informations sur les images, telles que leurs ID ou tailles.
- **ContainerInventory** : utilisez ce type de journal lorsque vous recherchez des informations sur l’emplacement des conteneurs, leurs noms et les images qu’ils exécutent.
- **ContainerLog** : utilisez ce type de journal lorsque vous recherchez des informations et entrées spécifiques du journal des erreurs.
- **ContainerNodeInventory_CL** Utilisez ce type lorsque vous souhaitez obtenir les informations sur le nœud ou l’hôte où résident les conteneurs. Il fournit la version Docker, le type d’orchestration, ainsi que des informations relatives au stockage et au réseau.
- **ContainerProcess_CL** Ce type permet de visualiser rapidement le processus actuellement exécuté dans le conteneur.
- **ContainerServiceLog** : utilisez ce type de journal lorsque vous recherchez des informations de piste d’audit pour le démon Docker, telles que les commandes de démarrage, d’arrêter, de suppression ou d’extraction.
- **KubeEvents_CL** Ce type permet d’afficher les événements Kubernetes.
- **KubePodInventory_CL** Utilisez ce type lorsque vous voulez comprendre les informations de hiérarchie du cluster.


### <a name="to-search-logs-for-container-data"></a>Pour rechercher des données de conteneur dans les journaux
* Choisissez une image qui a échoué récemment et recherchez-la dans les journaux des erreurs. Commencez par rechercher un nom de conteneur exécutant cette image avec une recherche **ContainerInventory**. Par exemple, recherchez `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Recherche de conteneurs Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Notez le nom du conteneur en regard de **Nom**, puis recherchez ces journaux. Dans cet exemple, il s’agit de `ContainerLog | where Name == "cranky_stonebreaker"`.

**Afficher les informations de performances**

Lorsque vous commencez à créer des requêtes, il peut être utile de voir d’abord ce qui est possible. Par exemple, pour afficher toutes les données de performances, essayez d’utiliser une large requête en tapant la requête de recherche suivante.

```
Perf
```

![Performances des conteneurs](./media/log-analytics-containers/containers-perf01.png)

Vous pouvez limiter les données de performances que vous voyez à un conteneur spécifique en tapant le nom de celui-ci à droite de votre requête.

```
Perf <containerName>
```

Cela a pour effet d’afficher la liste des mesures de performances collectées pour un conteneur spécifique.

![Performances des conteneurs](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à votre environnement. Comme point de départ, vous pouvez utiliser la zone **Exemples de requêtes** pour vous aider à créer des requêtes plus avancées.

![Requêtes de conteneurs](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Enregistrement de requêtes de recherche de journal
L’enregistrement des requêtes est une fonctionnalité standard dans Log Analytics. En les enregistrant, vous aurez aisément accès à celles que vous avez trouvées utiles pour une utilisation ultérieure.

Après avoir créé une requête qui vous semble utile, enregistrez-la en cliquant sur **Favorites** en haut de la page Recherche de journal. Vous pourrez ainsi y accéder facilement par la suite à partir de la page **Mon tableau de bord**.

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour consulter des enregistrements de données de conteneur détaillées.
