---
title: Azure IoT Suite et Logic Apps | Microsoft Docs
description: "Il s’agit d’un didacticiel expliquant comment raccorder des applications logiques à Azure IoT Suite pour les processus d’entreprise."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Didacticiel : Connecter Logic App à la solution préconfigurée de surveillance à distance IoT Suite
La solution préconfigurée de surveillance à distance [Microsoft Azure IoT Suite][lnk-internetofthings] est un excellent moyen de commencer rapidement avec un ensemble de fonctionnalités de bout en bout illustrant une solution IoT. Ce didacticiel vous familiarise avec l’ajout de Logic App à votre solution préconfigurée de surveillance à distance Microsoft Azure IoT Suite. Ces étapes montrent comment tirer davantage parti de votre solution IoT en la connectant à un processus d’entreprise.

*Si vous recherchez une procédure d’approvisionnement d’une solution préconfigurée de surveillance à distance, consultez [Didacticiel : prise en main des solutions préconfigurées IoT][lnk-getstarted].*

Avant de commencer ce didacticiel, vous devez :

* Configurer la solution préconfigurée de surveillance à distance dans votre abonnement Azure.
* Créez un compte SendGrid pour pouvoir envoyer un courrier électronique qui déclenche le processus d’entreprise. Vous pouvez vous inscrire à un compte d’évaluation gratuit sur [SendGrid](https://sendgrid.com/) en cliquant sur **Essai gratuit**. Une fois que vous avez enregistré votre compte d’évaluation gratuite, vous devez créer une [clé d’API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) dans SendGrid qui accorde les autorisations d’envoi de courrier. Cette clé d’API vous sera nécessaire ultérieurement dans le didacticiel.

Pour suivre ce didacticiel, vous avez besoin de Visual Studio 2015 ou Visual Studio 2017 pour modifier les actions dans le serveur principal de la solution préconfigurée.

En supposant que vous avez déjà approvisionné votre solution préconfigurée de surveillance à distance, accédez au groupe de ressources de cette solution dans le [portail Azure][lnk-azureportal]. Le groupe de ressources a le même nom que celui de la solution choisi lorsque vous avez configuré votre solution de surveillance à distance. Dans le groupe de ressources, vous pouvez voir toutes les ressources Azure approvisionnées pour votre solution. La capture d’écran suivante montre un exemple de panneau **Groupe de ressources** pour une solution préconfigurée de surveillance à distance :

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Pour commencer, configurez l’application logique à utiliser avec la solution préconfigurée.

## <a name="set-up-the-logic-app"></a>Configurer l’application logique
1. Cliquez sur **Ajouter** en haut du panneau de votre groupe de ressources dans le portail Azure.
2. Recherchez **l’application logique**, sélectionnez-la, puis cliquez sur **Créer**.
3. Indiquez le **nom** et utilisez les mêmes **abonnement** et **groupe de ressources** que ceux utilisés lorsque vous avez approvisionné votre solution de surveillance à distance. Cliquez sur **Create**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. Une fois le déploiement terminé, vous verrez que l’application logique est répertoriée en tant que ressource dans votre groupe de ressources.
5. Cliquez sur l’application logique pour accéder au panneau Application logique, puis sélectionnez le modèle **Application logique vide** pour ouvrir le **concepteur d’applications logiques**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Sélectionnez **Requête**. Cette action indique qu’une demande HTTP entrante avec une charge utile au format JSON spécifique agit comme un déclencheur.
7. Collez le code suivant dans le schéma JSON de corps de requête :
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Avant de pouvoir copier l’URL de la requête HTTP POST après avoir enregistré l’application logique, vous devez ajouter une action.
   > 
   > 
8. Cliquez sur **+ Nouvelle étape** sous votre déclencheur manuel. Cliquez ensuite sur **Ajouter une action**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Recherchez **SendGrid - Send email** (SendGrid - Envoyer un e-mail) et cliquez dessus.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Entrez un nom pour la connexion, tel que **SendGridConnection**, entrez la **clé d’API SendGrid** créée lorsque vous configurez votre compte SendGrid, puis cliquez sur **Créer**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Ajoutez les adresses de messagerie que vous possédez aux champs **De** et **À**. Ajoutez **Remote monitoring alert [DeviceId]** (Alerte de surveillance à distance [DeviceId]) au champ **Objet**. Dans le champ **Corps du message**, ajoutez **L’appareil [DeviceId] a renvoyé [measurementName] avec la valeur [measuredValue].** Vous pouvez ajouter **[DeviceId]**, **[measurementName]** et **[measuredValue]** en cliquant dans la section **Vous pouvez insérer des données à partir des étapes précédentes**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Cliquez sur **Enregistrer** dans le menu supérieur.
13. Cliquez sur le déclencheur **Requête** et copiez la valeur **POST HTTP pour cette URL**. Vous aurez besoin de cette URL plus tard dans ce didacticiel.

> [!NOTE]
> Logic Apps vous permet d’exécuter de [nombreux types d’actions différents][lnk-logic-apps-actions], notamment des actions dans Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Configurer la tâche webEventProcessor
Dans cette section, vous connectez votre solution préconfigurée à l’application logique que vous avez créée. Pour effectuer cette tâche, vous ajoutez l’URL permettant de déclencher l’application logique à l’action qui se déclenche lorsqu’une valeur de capteur d’appareil dépasse un certain seuil.

1. Utilisez votre client git pour cloner la dernière version du [référentiel github azure-iot-remote-monitoring][lnk-rmgithub]. Par exemple :
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. Dans Visual Studio, ouvrez **RemoteMonitoring.sln** à partir de la copie locale du référentiel.
3. Ouvrez le fichier **ActionRepository.cs** dans le dossier **Infrastructure\\Repository**.
4. Mettez à jour le dictionnaire **actionIds** avec la valeur **POST HTTP pour cette URL** notée à partir de votre application logique comme indiqué ci-dessous :
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Enregistrez les modifications dans la solution et quittez Visual Studio.

## <a name="deploy-from-the-command-line"></a>Déployer depuis la ligne de commande
Dans cette section, vous déployez votre version mise à jour de la solution de surveillance à distance pour remplacer la version en cours d’exécution dans Azure.

1. Suivez les instructions [de configuration du développement][lnk-devsetup] pour configurer votre environnement en vue du déploiement.
2. Pour déployer en local, suivez les instructions de [déploiement local][lnk-localdeploy].
3. Pour effectuer un déploiement sur le cloud et mettre à jour votre déploiement cloud existant, suivez les instructions relatives au [déploiement cloud][lnk-clouddeploy]. Utilisez le nom de votre déploiement d’origine comme nom de déploiement. Par exemple, si le déploiement d’origine s’appelle **demologicapp**, utilisez la commande suivante :
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Lors de l’exécution du script de création, veillez à utiliser les mêmes compte Azure, abonnement, région et instance Active Directory, que ceux utilisés lorsque vous avez approvisionné la solution.

## <a name="see-your-logic-app-in-action"></a>Voir votre application logique en action
La solution préconfigurée de surveillance à distance comporte deux règles définies par défaut lors de l’approvisionnement d’une solution. Ces deux règles se trouvent sur l’appareil **SampleDevice001** :

* Temperature > 38.00
* Humidity > 48.00

La règle de température déclenche l’action **Raise Alarm** (Déclencher l’alarme), tandis que la règle d’humidité déclenche l’action **SendMessage**. En supposant que vous avez utilisé la même URL pour les actions de la classe **ActionRepository** , votre application logique se déclenche pour chacune de ces règles. Les deux règles utilisent SendGrid pour envoyer à l’adresse **À** un courrier électronique contenant les détails de l’alerte.

> [!NOTE]
> L’application logique continue de se déclencher chaque fois que le seuil est atteint. Pour éviter les e-mails inutiles, vous pouvez désactiver les règles dans le portail de votre solution ou désactiver l’application logique dans le [portail Azure][lnk-azureportal].
> 
> 

En plus de recevoir des e-mails, vous pouvez également voir l’exécution de l’application logique dans le portail :

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez utilisé une application logique pour connecter la solution préconfigurée à un processus d’entreprise, vous pouvez en apprendre plus sur les options de personnalisation de solutions préconfigurées :

* [Utilisation de la télémétrie dynamique avec la solution préconfigurée de surveillance à distance][lnk-dynamic]
* [Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
