---
title: "FAQ d’Azure App Service sur Linux | Microsoft Docs"
description: "FAQ d’Azure App Service sur Linux."
keywords: azure app service, application web, faq, linux, oss
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: b22d5f3497c388192764aa6b4ee8c95fec568bd8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-app-service-on-linux-faq"></a>FAQ d’Azure App Service sur Linux

Avec la publication d’App Service sur Linux, nous travaillons à l’ajout de fonctionnalités et à l’amélioration de notre plateforme. Cet article fournit des réponses aux questions que nos clients nous ont posées récemment.

Si vous avez une question, commentez l’article ; nous vous répondrons dès que possible.

## <a name="built-in-images"></a>Images prédéfinies

**Je veux répliquer les conteneurs Docker intégrés fournis par la plateforme. Où se trouvent ces fichiers ?**

Vous trouverez tous les fichiers Docker sur [GitHub](https://github.com/azure-app-service). Vous trouverez tous les conteneurs Docker sur [Docker Hub](https://hub.docker.com/u/appsvc/).

**Quelles sont les valeurs attendues de la section Fichier de démarrage lorsque je configure la pile d’exécution ?**

Pour Node.js, vous spécifiez le fichier de configuration PM2 ou votre fichier de script. Pour .NET Core, vous spécifiez le nom de votre DLL compilée. Pour Ruby, vous pouvez spécifier le script Ruby avec lequel initialiser votre application.

## <a name="management"></a>Gestion

**Que se passe-t-il lorsque j’appuie sur le bouton Redémarrer dans le portail Azure ?**

Cette action revient à redémarrer Docker.

**Puis-je utiliser Secure Shell (SSH) pour me connecter à la machine virtuelle du conteneur d’application ?**

Oui, vous pouvez le faire via le site de gestion de contrôle de code source (SCM).

**Comment puis-je créer un plan App Service Linux via un kit de développement ou un modèle Azure Resource Manager ?**

Vous devez définir le champ du service d’application **reserved** sur *true*.

## <a name="continuous-integration-and-deployment"></a>Intégration et déploiement continus

**Mon application web utilise toujours une ancienne image de conteneur Docker après la mise à jour de l’image sur Docker Hub. Prenez-vous en charge l’intégration et le déploiement continus de conteneurs personnalisés ?**

Pour configurer l’intégration/le déploiement continu(e) des images Azure Container Registry ou DockerHub, consultez l’article [Déploiement continu avec Web App pour conteneurs](./app-service-linux-ci-cd.md). Pour les registres privés, vous pouvez actualiser le conteneur en arrêtant puis démarrant votre application web. Vous pouvez également modifier ou ajouter un paramètre d’application factice pour forcer une actualisation de votre conteneur.

**Prenez-vous en charge les environnements intermédiaires ?**

Oui.

**Puis-je utiliser *Web Deploy* pour déployer mon application web ?**

Oui, vous devez définir le paramètre d’application `WEBSITE_WEBDEPLOY_USE_SCM` sur *false*.

**Le déploiement Git de mon application échoue quand j’utilise une application web Linux. Comment puis-je résoudre ce problème ?**

Si le déploiement Git sur votre application web Linux échoue, vous pouvez choisir les options alternatives suivantes pour déployer le code de votre application :

- Utilisez la fonctionnalité de livraison continue (préversion) : vous pouvez stocker le code source de votre application dans un dépôt Git Team Services ou un dépôt GitHub pour utiliser la livraison continue Azure. Pour plus d’informations, consultez [Comment configurer la livraison continue pour une application web Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Utilisez l’[API de déploiement ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) : pour utiliser cette API, [connectez-vous via SSH à votre application web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) et accédez au dossier où vous souhaitez déployer votre code. Exécutez la commande suivante :

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Si vous obtenez une erreur stipulant que la commande `curl` est introuvable, veillez à installer curl à l’aide de `apt-get install curl` avant d’exécuter la commande `curl` précédente.

## <a name="language-support"></a>Support multilingue

**Je souhaite utiliser des websockets dans mon application Node.js. Y a-t-il des configurations ou des paramètres spéciaux à définir ?**

Oui, vous devez désactiver `perMessageDeflate` dans votre code Node.js côté serveur. Par exemple, si vous utilisez socket.io, effectuez les étapes suivantes :
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Prenez-vous en charge les applications .NET Core non compilées ?**

Oui.

**Prenez-vous en charge Composer en tant que gestionnaire de dépendances pour les applications PHP ?**

Oui. Lors d’un déploiement Git, Kudu doit détecter que vous déployez une application PHP (grâce à la présence d’un fichier composer.lock) et déclenchera une installation Composer pour vous.

## <a name="custom-containers"></a>Conteneurs personnalisés

**J’utilise mon propre conteneur personnalisé. Je souhaite que la plateforme monte un partage SMB dans le répertoire `/home/`.**

Vous pouvez le faire en définissant le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE` sur *true* ou en le supprimant complètement. Gardez à l’esprit que cela entraîne le redémarrage du conteneur quand le stockage de la plateforme subit une modification. 

>[!NOTE]
>Si le paramètre `WEBSITES_ENABLE_APP_SERVICE_STORAGE` est *false*, le répertoire `/home/` ne sera plus partagé par les instances d’échelle, et les fichiers qui y sont écrits ne seront pas conservés après un redémarrage.

**Mon conteneur personnalisé met longtemps à démarrer, et la plateforme le redémarre avant qu’il ait terminé.**

Vous pouvez configurer le temps que la plateforme doit attendre avant qu’elle redémarre votre conteneur. Pour ce faire, définissez le paramètre d’application `WEBSITES_CONTAINER_START_TIME_LIMIT` sur la valeur souhaitée. La valeur par défaut est de 230 secondes, la valeur maximale de 600 secondes.

**Quel est le format de l’URL du serveur de registre privé ?**

Vous devez fournir l’URL de registre complète, y compris `http://` ou `https://`.

**Quel est le format du nom d’image dans l’option de registre privé ?**

Ajoutez le nom complet de l’image, comprenant l’URL de registre privé (par exemple, myacr.azurecr.io/dotnet:latest). Les noms d’image qui utilisent un port personnalisé [ne peuvent pas être entrés par le biais du portail](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Pour définir `docker-custom-image-name`, utilisez [l’outil en ligne de commande `az`](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Je veux exposer plusieurs ports sur l’image de mon conteneur personnalisé.**

Nous ne prenons pas en charge actuellement l’exposition de plusieurs ports.

**Puis-je apporter mon propre système de stockage ?**

Nous ne prenons pas en charge actuellement l’utilisation de votre propre stockage.

**Pourquoi est-il impossible de parcourir le système de fichiers de mon conteneur personnalisé à partir du site SCM ?**

Le site SCM s’exécute dans un conteneur distinct. Vous ne pouvez pas vérifier le système de fichiers ou les processus en cours d’exécution du conteneur d’application.

**Mon conteneur personnalisé écoute un autre port que le port 80. Comment puis-je configurer mon application pour acheminer les demandes vers ce port ?**

Nous avons la détection automatique du port. Vous pouvez également spécifier le paramètre d’application *WEBSITES_PORT* et lui attribuer la valeur du numéro de port attendu. Auparavant, la plateforme utilisait le paramètre d’application *PORT*. Nous avons l’intention de déconseiller ce paramètre d’application pour utiliser exclusivement *WEBSITES_PORT*.

**Dois-je implémenter HTTPS dans mon conteneur personnalisé ?**

Non, la plateforme gère l’annulation HTTPS au niveau des serveurs frontaux partagés.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

**À présent que le service est disponible généralement, quels sont les tarifs ?**

Le nombre d’heures d’exécution de votre application vous est facturé, selon les tarifs Azure App Service normaux.

## <a name="other-questions"></a>Autres questions

**Quels sont les caractères pris en charge dans les noms de paramètres d’application ?**

Vous pouvez utiliser uniquement des lettres (A-Z, a-z), des chiffres (0-9) et le trait de soulignement (_) comme paramètres d’application.

**Où puis-je demander de nouvelles fonctionnalités ?**

Vous pouvez proposer votre idée sur le [Forum de commentaires pour Web Apps](https://aka.ms/webapps-uservoice). Ajoutez « [Linux] » au titre de votre idée.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce qu’Azure App Service sur Linux ?](app-service-linux-intro.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu avec Web App pour conteneurs](./app-service-linux-ci-cd.md)
