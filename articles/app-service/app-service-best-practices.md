---
title: Meilleures pratiques pour Azure App Service
description: "Découvrez les meilleures pratiques et la résolution des problèmes pour Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 251ce238b745734bdfb508b30097304a9a650a8c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="best-practices-for-azure-app-service"></a>Meilleures pratiques pour Azure App Service
Cet article résume les meilleures pratiques dans l’utilisation de [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocalisation
Lorsqu’une base de données et des ressources Azure composant une solution, telle qu’une application web, se trouvent dans des régions différentes, les effets entraînés peuvent être les suivants :

* Une latence plus élevée dans la communication entre les ressources
* Des frais plus élevés pour le transfert de données sortantes entre les régions comme indiqué dans la [page de tarification Azure](https://azure.microsoft.com/pricing/details/data-transfers)

La colocalisation dans la même région est idéale pour les ressources Azure composant une solution telle qu’une application web et un compte de stockage ou de base de données utilisé pour conserver du contenu ou des données. Lors de la création de ressources, assurez-vous que celles-ci se trouvent dans la même région Azure, à moins que des motifs commerciaux ou conceptuels spécifiques entravent cette possibilité. Vous pouvez déplacer une application App Service vers la région dans laquelle se trouve votre base de données en tirant parti de la [fonctionnalité de clonage de l’application App Service](app-service-web-app-cloning.md) , actuellement disponible pour les applications du plan App Service Premium.   

## <a name="memoryresources"></a>Quand les applications consomment plus de mémoire que prévu
Si vous remarquez qu’une application consomme plus de mémoire que prévu, indiqué par des analyses ou des recommandations de service, envisagez d’exécuter la [fonctionnalité auto-curative d’App Service](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Une des options de la fonctionnalité auto-curative consiste à prendre des actions personnalisées, basées sur un seuil de mémoire. Les actions vont des notifications par courrier électronique à la prévention immédiate des risques par le recyclage du processus de travail, en passant par l’examen via le vidage de la mémoire. La fonctionnalité auto-curative peut être configurée via le fichier web.config et une interface utilisateur conviviale, comme le décrit ce billet de blog pour l’ [extension du site de support App Service](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quand les applications consomment plus d’UC que prévu
Si vous remarquez qu’une application consomme plus d’UC que prévu ou qu’elle provoque des pics de consommation d’UC répétés, indiqué par des analyses ou des recommandations de service, envisagez de faire monter ou descendre en puissance le plan App Service. Si votre application est « avec état », la seule option possible est la montée en charge. Si l’application est « sans état », l’augmentation de la taille des instances vous confère plus de souplesse et un potentiel de mise à l’échelle plus élevé. 

Pour plus d’informations sur les applications « sans état » et « avec état », visualisez la vidéo [Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)(Planification d’une application multiniveau complète et évolutive sur l’application web Microsoft Azure). Pour plus d’informations sur les options de montée en charge et de mise à l’échelle automatique, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Quand les ressources de socket sont épuisées
L’utilisation des bibliothèques clientes constitue une raison courante de l’épuisement des connexions TCP sortantes ; ces bibliothèques ne sont pas implémentées pour la réutilisation des connexions TCP ou, dans le cas d’un protocole de niveau supérieur tel que HTTP, la logique de connexion persistante n’est pas utilisée. Veuillez consulter la documentation pour chacune des bibliothèques référencées par les applications dans votre plan App Service afin de vous assurer que celles-ci sont configurées ou accessibles dans votre code pour une réutilisation efficace des connexions sortantes. Suivez également les instructions de la documentation des bibliothèques pour une création et une mise en production appropriées ainsi que pour un nettoyage servant à éviter la fuite des connexions. Lorsque les examens des bibliothèques clientes sont en cours d’exécution, leur impact peut être atténué en augmentant la taille des instances.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js et requêtes http sortantes
Quand vous utilisez Node.js et de nombreuses requêtes http sortantes, il est très important de prendre en compte le protocole HTTP - Keep-Alive. Vous pouvez utiliser le package [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` pour en faciliter l’utilisation dans votre code.

Vous devez toujours gérer la réponse `http`, même si vous n’effectuez aucune opération dans le gestionnaire. Si vous ne gérez pas la réponse correctement, votre application finit par se bloquer, car aucun socket supplémentaire n’est disponible.

Par exemple, quand vous utilisez le package `http` ou `https` :

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Si vous exécutez App Service sur Linux sur un ordinateur avec plusieurs cœurs, une autre bonne pratique consiste à utiliser PM2 pour démarrer plusieurs processus Node.js afin d’exécuter votre application. Pour ce faire, il suffit de spécifier une commande de démarrage à votre conteneur.

Par exemple, pour démarrer quatre instances :

`pm2 start /home/site/wwwroot/app.js --no-daemon -i 4`

## <a name="appbackup"></a>La sauvegarde de votre application échoue
Les deux raisons les plus courantes qui entraînent l'échec de la sauvegarde d'une application sont les suivantes : des paramètres de stockage non valides et une configuration de base de données non valide. Ces échecs se produisent généralement lorsque des modifications ont été appliquées à des ressources de stockage ou de base de données, ou lorsque la méthode d'accès à ces ressources a changé (par exemple en cas de mise à jour des informations d’identification pour accéder à la base de données sélectionnée dans les paramètres de sauvegarde). En général, les sauvegardes s'exécutent de façon planifiée et requièrent l’accès au stockage (pour générer les fichiers sauvegardés) et aux bases de données (pour copier et lire le contenu à inclure dans la sauvegarde). L'incapacité à accéder à l'une de ces ressources entraîne une erreur de sauvegarde permanente. 

Lorsque des échecs de sauvegarde se produisent, veuillez consulter les résultats les plus récents pour comprendre quel type d'échec se produit. Si vous ne parvenez pas à accéder au stockage, examinez et mettez à jour les paramètres de stockage utilisés dans la configuration de la sauvegarde. Si vous ne parvenez pas à accéder à la base de données, examinez et mettez à jour vos chaînes de connexion dans les paramètres de l’application, puis mettez à jour la configuration de votre sauvegarde afin d’inclure correctement les bases de données requises. Pour plus d’informations sur la sauvegarde d’une application, consultez la documentation [Sauvegarder une application web dans Azure App Service](web-sites-backup.md) .

## <a name="nodejs"></a>Lorsque de nouvelles applications Node.js sont déployées sur Azure App Service
La configuration Azure App Service par défaut pour les applications Node.js vise à mieux répondre aux besoins des applications courantes. Si la configuration de votre application Node.js peut bénéficier d’un paramétrage personnalisé pour améliorer les performances ou optimiser l’utilisation des ressources au niveau processeur/mémoire/réseau, vous pouvez consulter nos meilleures pratiques et les étapes de dépannage. Cette documentation décrit les paramètres iisnode, que vous devrez peut-être configurer pour votre application Node.js, présente les différents scénarios ou problèmes que votre application peut rencontrer, et indique comment résoudre ces problèmes : [Meilleures pratiques et guide de dépannage pour les applications Node sur Azure App service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   

