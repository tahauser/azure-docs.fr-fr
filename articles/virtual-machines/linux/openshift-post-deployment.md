---
title: "OpenShift sur des tâches de post-déploiement Azure | Microsoft Docs"
description: "Tâches supplémentaires après le déploiement d’un cluster OpenShift."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 77c4719b5cee7f5736d73ee10cf6abf12229ea11
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="post-deployment-tasks"></a>Tâches de post-déploiement

Une fois que vous avez déployé un cluster OpenShift, vous pouvez configurer d’autres éléments. Cet article aborde les thèmes suivants :

- Configurer l’authentification unique à l’aide d’Azure Active Directory (Azure AD)
- Configurer Operations Management Suite pour surveiller OpenShift
- Configurer les métriques et la journalisation

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurer l’authentification unique à l’aide d’Azure Active Directory

Pour utiliser Azure Active Directory à des fins d’authentification, vous devez d’abord créer une inscription d’application Azure AD. Ce processus implique deux étapes : création de l’inscription d’application et configuration d’autorisations.

### <a name="create-an-app-registration"></a>Créer une inscription d’application

Ces étapes utilisent Azure CLI pour créer l’inscription d’application et l’interface graphique utilisateur (portail) pour définir les autorisations. Pour créer l’inscription d’application, cinq éléments d’information sont nécessaires :

- Nom complet : nom d’inscription d’application (par exemple : OCPAzureAD)
- Page d’accueil : URL de la console OpenShift (par exemple : https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI de l’identificateur : URL de la console OpenShift (par exemple : https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URL de réponse : URL publique master et nom de l’inscription d’application (p. ex. : https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Mot de passe : mot de passe sécurisé (Utilisez un mot de passe fort.)

L’exemple suivant crée une inscription d’application à l’aide des informations précédentes :

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Si la commande aboutit, vous obtenez une sortie JSON similaire à la suivante :

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Prenez note de la propriété appId renvoyée par la commande. Vous en aurez besoin pour une étape suivante.

Dans le portail Azure :

1.  Sélectionnez **Azure Active Directory** > **Inscription d’application**.
2.  Recherchez votre inscription d’application (par exemple : OCPAzureAD).
3.  Dans les résultats, cliquez sur l’inscription d’application.
4.  Sous **Paramètres**, sélectionnez **Autorisations requises**.
5.  Sous **Autorisations requises**, sélectionnez **Ajouter**.

  ![Inscription d’application](media/openshift-post-deployment/app-registration.png)

6.  Cliquez sur Étape 1 : sélectionnez API, cliquez sur **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Cliquez sur **Sélectionner** en bas.

  ![Inscription d’application - Sélectionner une API](media/openshift-post-deployment/app-registration-select-api.png)

7.  À l’étape 2 : sélectionnez Autorisations, sélectionnez **Activer la connexion et lire le profil utilisateur** sous **Autorisations déléguées**, puis cliquez sur **Sélectionner**.

  ![Accès à l’inscription d’application](media/openshift-post-deployment/app-registration-access.png)

8.  Sélectionnez **Terminé**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurer OpenShift pour l’authentification Azure AD

Pour configurer OpenShift pour utiliser Azure AD en tant que fournisseur d’authentification, le fichier /etc/origin/master/master-config.yaml doit être modifié sur tous les nœuds master.

Trouvez l’ID de locataire à l’aide de la commande d’interface de ligne de commande suivante :

```azurecli
az account show
```

Dans le fichier yaml, recherchez les lignes suivantes :

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Immédiatement après les lignes précédentes, insérez les lignes suivantes :

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Trouvez l’ID de locataire à l’aide de la commande d’interface de ligne de commande suivante : ```az account show```

Redémarrez les services maître OpenShift sur tous les nœuds master :

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) avec plusieurs masters**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform avec un seul master**

```bash
sudo systemctl restart atomic-openshift-master
```

Dans la console OpenShift, vous voyez maintenant deux options pour l’authentification : htpasswd_auth et [Inscription d’application].

## <a name="monitor-openshift-with-operations-management-suite"></a>Surveiller OpenShift avec Operations Management Suite

Pour surveiller OpenShift avec Operations Management Suite (OMS), vous avez deux possibilités : installation de l’agent OMS sur un hôte de machines virtuelles ou un conteneur OMS. Cet article fournit des instructions sur le déploiement du conteneur OMS.

## <a name="create-an-openshift-project-for-operations-management-suite-and-set-user-access"></a>Créer un projet OpenShift pour Operations Management Suite et définir l’accès utilisateur

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Créer un fichier yaml de jeu de démons

Créez un fichier nommé ocp-omsagent.yml :

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Créer un fichier yaml de secret

Pour créer le fichier yaml de secret, deux éléments d’information sont nécessaires : l’ID d’espace de travail OMS et la clé partagée d’espace de travail OMS. 

Voici un exemple de fichier ocp-secret.yml : 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Remplacez wsid_data par l’ID d’espace de travail OMS encodé en Base64. Ensuite, remplacez key_data par la clé partagée d’espace de travail OMS encodée en Base64.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Créer le secret et le jeu de démons

Déployez le fichier de secret :

```bash
oc create -f ocp-secret.yml
```

Déployez le jeu de démons de l’agent OMS :

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurer les métriques et la journalisation

Le modèle Azure Resource Manager pour OpenShift Container Platform fournit des paramètres d’entrée pour l’activation des métriques et de la journalisation. L’offre de la Place de marché d’OpenShift Container Platform et le modèle Resource Manager OpenShift Origin n’en fournissent pas.

Si vous avez utilisé le modèle Resource Manager d’OCP et si les métriques et la journalisation n’étaient pas activées au moment de l’installation, ou si vous avez utilisé l’offre de la Place de marché d’OCP, vous pouvez facilement activer les métriques et la journalisation après coup. Si vous utilisez le modèle Resource Manager OpenShift Origin, un travail préalable est requis.

### <a name="openshift-origin-template-pre-work"></a>Travail préalable du modèle OpenShift Origin

1. Établissez une connexion SSH au premier nœud master en utilisant le port 2200.

   Exemple :

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Modifiez le fichier /etc/ansible/hosts et ajoutez les lignes suivantes après la section du fournisseur d’identité (# Enable HTPasswdPasswordIdentityProvider) :

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. Remplacez $ROUTING par la chaîne utilisée pour l’option openshift_master_default_subdomain dans le même fichier /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Fournisseur de cloud Azure utilisé

Sur le premier nœud master (origine) ou le nœud bastion (OCP), établissez une connexion SSH à l’aide des informations d’identification fournies durant le déploiement. Exécutez la commande suivante :

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Fournisseur de cloud Azure non utilisé

Sur le premier nœud master (origine) ou le nœud bastion (OCP), établissez une connexion SSH à l’aide des informations d’identification fournies durant le déploiement. Exécutez la commande suivante :

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main d’OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Prise en main d’OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
