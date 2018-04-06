---
title: Déployer l’infrastructure Nozzle d’Azure Log Analytics pour surveiller le système Cloud Foundry | Microsoft Docs
description: Procédure de déploiement pas à pas du compileur de fichiers log de Cloud Foundry Nozzle pour Azure Log Analytics. Surveillez les mesures de performances et d’intégrité du système Cloud Foundry via l’infrastructure Nozzle.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: timlt
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: b900a42196eedab89af8e55d71a336ed7adc45a4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Deploy Azure Log Analytics for Cloud Foundry Monitoring (Déployer l’infrastructure Nozzle d’Azure Log Analytics pour surveiller le système Cloud Foundry)

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) est un service dans Azure. Il facilite la collecte et l’analyse de données générées depuis votre cloud et vos environnements locaux.

L’infrastructure Nozzle de Microsoft Azure Log Analytics est un composant de Cloud Foundry, qui envoie des mesures du [compileur de fichiers log de Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose à Log Analytics. Avec l’infrastructure Nozzle, vous pouvez collecter, consulter et analyser l’intégrité et les indicateurs de performance de Cloud Foundry et ce, sur plusieurs déploiements.

Dans ce document, vous allez apprendre comment déployer l’infrastructure Nozzle dans votre environnement Cloud Foundry et accéder aux données depuis la console Log Analytics.

## <a name="prerequisites"></a>Prérequis


Les étapes suivantes sont requises pour le déploiement de Nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Déployer un environnement Pivotal Cloud Foundry ou Cloud Foundry dans Azure

Vous pouvez utiliser l’infrastructure Nozzle avec le déploiement d’un déploiement Cloud Foundry open source ou d’un déploiement Pivotal Cloud Foundry.

* [Déployer Cloud Foundry sur Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Déployer Pivotal Cloud Foundry sur Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installer les outils en ligne de commande pour déployer l’infrastructure Nozzle

Nozzle s’exécute en tant qu’application dans un environnement Cloud Foundry. Il vous faut l’interface CLI pour déployer l’application.

Nozzle doit également disposer d’autorisations d’accès au compileur de fichiers log Firehose et à Cloud Controller. Pour créer et configurer l’utilisateur, vous devez installer le service UAA (User Account and Authentication).

* [Installer l’interface de ligne de commande Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installer le client de ligne de commande UAA de Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Avant de configurer le client de ligne de commande UAA, assurez-vous que l’infrastructure Rubygems est installée.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Créer un espace de travail Log Analytics dans Azure

Vous pouvez créer l’espace de travail Log Analytics manuellement ou à l’aide d’un modèle. Une fois le déploiement de Nozzle terminé, chargez les alertes et vues OMS préconfigurées.

Pour créer l’espace de travail manuellement, procédez comme suit :

1. Dans le portail Azure, recherchez le logiciel Log Analytics dans la liste des services de la Place de marché Microsoft Azure, puis sélectionnez-le.
2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

   * **Espace de travail OMS** : saisissez un nom pour votre espace de travail.
   * **Abonnement** : si vous possédez plusieurs abonnements, choisissez celui sur lequel vous avez déployé Cloud Foundry.
   * **Groupe de ressources** : vous pouvez créer un groupe de ressources, ou utiliser celui de l’environnement Cloud Foundry que vous avez déployé.
   * **Emplacement** : indiquez l’emplacement.
   * **Niveau tarifaire** : cliquez sur **OK** pour terminer.

Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

Vous pouvez également créer l’espace de travail Log Analytics via le modèle OMS. Grâce à cette méthode, le modèle charge automatiquement les vues et alertes OMS préconfigurées. Pour en savoir plus, consultez la [solution Azure Log Analytics pour Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Déployer l’infrastructure Nozzle

Il existe deux façons de déployer Nozzle : sous forme de mosaïque PCF ou en tant qu’application Cloud Foundry.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Déployer l’infrastructure Nozzle en tant que vignette Ops Manager sur Pivotal Cloud Foundry

Si vous avez déployé PCF à l’aide d’Operations Manager, suivez les étapes permettant [d’installer et de configurer Nozzle pour PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Nozzle est installé sous forme de mosaïque avec Operations Manager.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>Déployer l’infrastructure Nozzle en tant qu’application Cloud Foundry

Si vous n’utilisez pas Ops Manager pour Pivotal Cloud Foundry, déployez l’infrastructure Nozzle en tant qu’application. Les sections suivantes décrivent ce processus.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Connexion à votre déploiement Cloud Foundry en tant qu’administrateur via l’interface de ligne de commande de Cloud Foundry

Exécutez la commande suivante :
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

« SYSTEM_DOMAIN » correspond au nom de domaine de votre environnement Cloud Foundry. Vous pouvez le récupérer en recherchant « SYSTEM_DOMAIN » dans le fichier du manifeste de déploiement. 

"CF_User" correspond au nom d’administrateur de l’environnement Cloud Foundry. Vous pouvez récupérer le nom et le mot de passe en recherchant dans la section « scim » le nom et « cf_admin_password » dans le fichier du manifeste de déploiement Cloud Foundry.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Créer un utilisateur Cloud Foundry et lui accorder les autorisations nécessaires

Exécutez les commandes suivantes :
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

« SYSTEM_DOMAIN » correspond au nom de domaine de votre environnement Cloud Foundry. Vous pouvez le récupérer en recherchant « SYSTEM_DOMAIN » dans le fichier du manifeste de déploiement.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Télécharger la version la plus récente de l’infrastructure Nozzle d’Azure Log Analytics

Exécutez la commande suivante :
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Définition des variables d'environnement

Vous pouvez désormais définir des variables d’environnement dans le fichier manifest.yml de votre répertoire actuel. Le code suivant illustre le manifeste d’application pour Nozzle. Remplacez les valeurs par des informations sur l’espace de travail Log Analytics spécifique.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Transmettre l’application (push) depuis votre ordinateur de développement

Vérifiez que vous vous trouvez dans le dossier oms-log-analytics-firehose-nozzle. Exécutez la commande suivante :
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Valider l’installation de l’infrastructure Nozzle

### <a name="from-apps-manager-for-pcf"></a>À partir du gestionnaire d’applications (pour Pivotal Cloud Foundry)

1. Connectez-vous à Ops Manager et assurez-vous que la mosaïque est affichée sur le tableau de bord de l’installation.
2. Connectez-vous au gestionnaire d’applications et assurez-vous que l’espace que vous avez créé pour l’infrastructure Nozzle est répertorié dans le rapport d’utilisation. Assurez-vous que l’état est normal.

### <a name="from-your-development-computer"></a>À partir de l’ordinateur de développement

Dans la fenêtre de l’interface CLI Cloud Foundry, saisissez :
```
cf apps
```
Assurez-vous que l’application OMS Nozzle est en cours d’exécution.

## <a name="view-the-data-in-the-oms-portal"></a>Afficher les données dans le portail OMS

### <a name="1-import-the-oms-view"></a>1. Importer la vue OMS

À partir du portail OMS, accédez à la zone **Concepteur de vues** > **Importer** > **Parcourir**, puis sélectionnez l’un des fichiers omsview. Par exemple, sélectionnez *Cloud Foundry.omsview*et enregistrez la vue. Une mosaïque s’affiche alors sur la page **Vue d’ensemble**. Sélectionnez-la pour afficher les mesures visualisées.

Les opérateurs peuvent personnaliser ces vues ou en créer, grâce au **Concepteur de vues**.

Le fichier *« Cloud Foundry.omsview »* est une préversion du modèle de vue OMS de Cloud Foundry. Il s’agit d’un modèle par défaut, entièrement configuré. Pour toute suggestion ou tout commentaire sur le modèle, accédez à la [section relative aux problèmes](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Créer des règles d'alerte

Vous pouvez [créer des alertes](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) et personnaliser les requêtes et les valeurs de seuil, si nécessaire. Voici les alertes recommandées :

| Requête de recherche                                                                  | Générer l’alerte selon | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Nombre de résultats < 1   | La chaîne **bbs.Domain.cf-apps** indique si le domaine cf-apps est à jour. Cela indique que les requêtes des applications Cloud Foundry de Cloud Controller sont synchronisées avec bbs.LRPsDesired (lA souhaitées par Diego) pour l’exécution. Si aucune donnée n’est reçue, le domaine cf-apps n’est pas mis à jour dans la fenêtre de temps indiquée. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Nombre de résultats > 0   | Pour les cellules de Diego, 0 signifie sain, et 1, défectueux. Définissez une alerte au cas où plusieurs cellules défectueuses de Diego sont détectées dans la fenêtre de temps indiquée. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Nombre de résultats > 0 | 1 signifie que le système est sain, et 0 signifie que le système est défectueux. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Nombre de résultats > 0   | Consul envoie périodiquement son état d’intégrité. 0 signifie que le système est sain, et 1 signifie que l’émetteur d’itinéraire détecte que Consul est éteint. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Nombre de résultats > 0 | Nombre d’écarts entre les messages déposés intentionnellement par Doppler en raison d’une régulation de flux. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Nombre de résultats > 0   | Le compileur de fichiers log envoie le message **LGR** pour signaler les problèmes liés à la connexion. Exemple de ce type de problème : lorsque la sortie du message journal est trop élevée. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Nombre de résultats > 0   | Lorsque l’infrastructure Nozzle reçoit une alerte de faible consommation du compileur de fichiers log, elle envoie la valeurmétrique **SlowConsumerAlert** à Log Analytics. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Nombre de résultats > 0   | Si le nombre d’écarts entre les événements perdus atteint un certain seuil, cela peut signifier que l’infrastructure Nozzle a du mal à s’exécuter. |

## <a name="scale"></a>Scale

Vous pouvez faire évoluer Nozzle et le compileur de fichiers log.

### <a name="scale-the-nozzle"></a>Mettre à l’échelle l’infrastructure Nozzle

Nous vous invitons à commencer par un minimum de deux instances de l’infrastructure Nozzle. Firehose distribue la charge de travail sur toutes les instances de l’infrastructure Nozzle.
Pour vous assurer que Nozzle peut suivre le trafic des données à partir de Firehose, configurez l’alerte **slowConsumerAlert** (répertoriée dans la section précédente, « Créer des règles d’alerte »). Une fois que vous avez été alerté, respectez les [conseils à suivre en cas de ralentissement de Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) pour déterminer si la mise à l’échelle est nécessaire.
Pour mettre à l’échelle l’infrastructure Nozzle, utilisez le gestionnaire d’applications ou l’interface de ligne de commande Cloud Foundry pour augmenter le nombre d’instances ou de ressources mémoire ou disque pour l’infrastructure.

### <a name="scale-the-loggregator"></a>Mettre à l’échelle le compileur de fichiers log

Le compileur de fichiers log envoie un message de journal **LGR** pour signaler les problèmes liés à la connexion. Vous pouvez surveiller l’alerte pour déterminer si le compileur de fichiers log doit être mis à l’échelle.
Pour le mettre à l’échelle, augmentez la taille du tampon Doppler ou ajoutez des instances de serveur Doppler supplémentaires dans le manifeste Cloud Foundry. Pour en savoir plus, consultez [les conseils de mise à l’échelle du compileur de fichiers log](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Mettre à jour

Pour mettre à jour l’infrastructure Nozzle vers une version plus récente, téléchargez la nouvelle version de Nozzle, suivez les étapes de la section « Déployer », puis envoyez de nouveau l’application (push).

### <a name="remove-the-nozzle-from-ops-manager"></a>Retrait de l’infrastructure Nozzle à partir d’Operations Manager

1. Connectez-vous à Ops Manager.
2. Recherchez la mosaïque **Infrastructure Nozzle de Microsoft Azure Log Analytics pour Pivotal Cloud Foundry**.
3. Cliquez sur l’icône de poubelle, puis confirmez la suppression.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Retrait de l’infrastructure Nozzle à partir de l’ordinateur de développement

Dans la fenêtre de l’interface CLI de Cloud Foundry, saisissez :
```
cf delete <App Name> -r
```

Les données du portail OMS ne sont pas supprimées automatiquement lorsque vous supprimez Nozzle. Elles arrivent à expiration en fonction de votre paramètre de rétention Log Analytics.

## <a name="support-and-feedback"></a>Support et commentaires

L’infrastructure Nozzle d’Azure Log Analytics est open source. Envoyez vos questions et commentaires à la [section GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Pour ouvrir une demande d’assistance Azure, sélectionnez la « machine virtuelle qui exécute Cloud Foundry » en tant que catégorie de service. 

## <a name="next-step"></a>Étapes suivantes


En plus des mesures de Cloud Foundry gérées par l’infrastructure Nozzle, vous pouvez utiliser un agent OMS pour obtenir des informations sur les données opérationnelles de niveau de la machine virtuelle (Syslog, performances, alertes, inventaire). L’agent OMS est installé en tant que composant additionnel Bosh sur vos machines virtuelles Cloud Foundry.

Pour en savoir plus, voir [Déployer l’agent OMS sur votre déploiement Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
