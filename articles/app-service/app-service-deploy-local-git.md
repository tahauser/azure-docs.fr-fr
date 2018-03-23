---
title: Déploiement Git local vers Azure App Service
description: Découvrez comment activer le déploiement Git local vers Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Déploiement Git local vers Azure App Service

Ce guide de procédures vous montre comment déployer votre code sur [Azure App Service](app-service-web-overview.md) depuis un dépôt Git sur votre ordinateur local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Pour suivre les étapes décrites dans ce guide de procédures :

* [Installer Git](http://www.git-scm.com/downloads).
* Créez un dépôt Git local comprenant le code que vous souhaitez déployer.

Pour utiliser un dépôt d’exemples à suivre, exécutez la commande suivante dans la fenêtre de terminal locale :

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Préparer votre dépôt

Vérifiez que la racine de votre dépôt comprend les fichiers nécessaires à votre projet.

| Runtime | Fichiers du répertoire racine |
|-|-|
| ASP.NET (Windows uniquement) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux uniquement) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ avec un script de démarrage |
| Python (Windows uniquement) | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ sous _App\_Data/jobs/continuous_ (pour les WebJobs continus) ou _App\_Data/jobs/triggered_ (pour les WebJobs déclenchés). Pour plus d’informations, consultez la [documentation Kudu relative aux WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Consultez [Déploiement continu pour Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Pour personnaliser votre déploiement, vous pouvez inclure un fichier _.deployment_ dans la racine du dépôt. Pour plus d’informations, consultez [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) et [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Veillez à utiliser `git commit` pour valider toutes les modifications que vous souhaitez déployer.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Activer Git pour votre application

Si vous souhaitez activer le déploiement Git pour une application App Service existante, exécutez [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) dans Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pour créer une application Git, exécutez [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) dans Cloud Shell avec le paramètre `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

La sortie de la commande `az webapp create` doit ressembler à ceci :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="deploy-your-project"></a>Déployer votre projet

De retour dans la _fenêtre du terminal local_, ajoutez un référentiel distant Azure dans votre référentiel Git local. Remplacez _\<url>_ par l’URL du Git distant de la section [Activer Git pour votre application](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous êtes invité à saisir un mot de passe, veillez à utiliser celui que vous avez créé dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

Vous pouvez voir une automation spécifique au runtime dans la sortie, comme MSBuild pour ASP.NET, `npm install` pour Node.js et `pip install` pour Python. 

Une fois le déploiement terminé, un enregistrement de votre `git push` doit se trouver dans la page **Options de déploiement** de votre application dans le portail Azure.

![](./media/app-service-deploy-local-git/deployment_history.png)

Accédez à votre application pour vérifier que le contenu a été déployé.

## <a name="troubleshooting"></a>Résolution de problèmes

Voici les erreurs ou les problèmes couramment rencontrés lors de l’utilisation de Git pour publier une application App Service dans Azure :

---
**Symptôme** : `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Cause** : Cette erreur peut se produire si l’application n’est pas opérationnelle.

**Résolution** : démarrez l’application dans le portail Azure. Le déploiement Git est indisponible quand l’application web est arrêtée.

---
**Symptôme** : `Couldn't resolve host 'hostname'`

**Cause** : Cette erreur peut se produire si les informations d’adresse entrées au moment de la création du dépôt distant « azure » sont incorrectes.

**Résolution** : utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l’URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.

---
**Symptôme** : `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Cause** : Cette erreur peut se produire si vous ne spécifiez pas de branche pendant l’opération `git push`, ou si vous n’avez pas défini la valeur `push.default` dans `.gitconfig`.

**Résolution** : Réexécutez `git push`, en spécifiant la branche maîtresse. Par exemple : 

```bash
git push azure master
```

---
**Symptôme** : `src refspec [branchname] does not match any.`

**Cause** : Cette erreur peut se produire si vous tentez d’effectuer une opération Push sur une autre branche que la branche maîtresse sur le dépôt distant « azure ».

**Résolution** : Réexécutez `git push`, en spécifiant la branche maîtresse. Par exemple : 

```bash
git push azure master
```

---
**Symptôme** : `RPC failed; result=22, HTTP code = 5xx.`

**Cause** : Cette erreur peut se produire si vous essayez d’envoyer (push) un dépôt Git volumineux via HTTPS.

**Résolution** : modifiez la configuration git sur l’ordinateur local pour agrandir le postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Symptôme** : `Error - Changes committed to remote repository but your web app not updated.`

**Cause** : Cette erreur peut se produire si vous déployez une application Node.js contenant un fichier _package.json_ spécifiant des modules obligatoires supplémentaires.

**Résolution** : Des messages supplémentaires contenant « npm ERR! » doivent être journalisés avant cette erreur, et peuvent fournir davantage de contexte sur l’échec. Voici les causes connues de cette erreur et le message « npm ERR! » correspondant :

* **Fichier package.json incorrect**: npm ERR! Couldn’t read dependencies.
* **Native module that does not have a binary distribution for Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Ou
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Documentation du projet Kudu](https://github.com/projectkudu/kudu/wiki)
* [Déploiement continu vers Azure App Service](app-service-continuous-deployment.md)
