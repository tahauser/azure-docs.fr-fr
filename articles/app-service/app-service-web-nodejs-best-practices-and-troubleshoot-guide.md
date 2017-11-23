---
title: "Meilleures pratiques et guide de dépannage pour les applications node sur Azure Web Apps"
description: "Découvrez les meilleures pratiques et les procédures de dépannage pour les applications node sur Azure Web Apps."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: f7f3186ba93670e566a10f97dde86a977101e8fc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Meilleures pratiques et guide de dépannage pour les applications node sur Azure Web Apps
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Cet article présente des bonnes pratiques et des procédures de dépannage pour les [applications node](app-service-web-get-started-nodejs.md) exécutées dans Azure Web Apps (avec [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Soyez vigilant lorsque vous appliquez ces étapes de dépannage sur votre site de production. Il est recommandé de résoudre les problèmes de votre application sur une installation hors production (par exemple, votre emplacement intermédiaire) et, une fois le problème résolu, de remplacer votre emplacement intermédiaire par votre emplacement de production.
> 
> 

## <a name="iisnode-configuration"></a>Configuration de IISNODE
Ce [fichier de schéma](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) affiche tous les paramètres que vous pouvez configurer pour iisnode. Voici quelques paramètres qui peuvent être utiles pour votre application :

* nodeProcessCountPerApplication
  
    Ce paramètre contrôle le nombre de processus de nœud qui seront lancés pour chaque application IIS. La valeur par défaut est 1. Vous pouvez lancer autant de fichiers node.exe que votre nombre de processeurs virtuels en définissant ce paramètre sur 0. La valeur recommandée est 0 pour la plupart des applications, ce qui signifie que vous pouvez utiliser tous les processeurs virtuels de votre machine virtuelle. Node.exe étant de type monothread, un seul node.exe consomme 1 processeur virtuel maximum. Pour optimiser le niveau de performance de votre application node, l’idéal est d’utiliser tous les processeurs virtuels.
* nodeProcessCommandLine
  
    Ce paramètre contrôle le chemin d’accès à node.exe. Vous pouvez définir cette valeur pour pointer vers votre version node.exe.
* maxConcurrentRequestsPerProcess
  
    Ce paramètre contrôle le nombre maximal de demandes simultanées envoyées par iisnode pour chaque node.exe. Dans Azure Web Apps, la valeur par défaut est « Infinite ». Vous n’avez pas à vous soucier de ce paramètre. En dehors d’Azure Web Apps, la valeur par défaut est 1024. Vous pouvez configurer ce paramètre en fonction du nombre de requêtes dirigées vers votre application et de la vitesse à laquelle votre application traite chaque requête.
* maxNamedPipeConnectionRetry
  
    Ce paramètre contrôle le nombre maximal de tentatives de connexion effectuées par iisnode sur le canal nommé pour envoyer la requête à node.exe. Ce paramètre, utilisé parallèlement au paramètre namedPipeConnectionRetryDelay, permet de déterminer le délai d’attente total de chaque requête dans iisnode. La valeur par défaut est 200 sur Azure Web Apps. Délai total d’expiration en secondes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000
* namedPipeConnectionRetryDelay
  
    Ce paramètre contrôle la durée (en ms) d’attente que respecte iisnode entre chaque tentative d’envoi de la requête à node.exe via le canal nommé. La valeur par défaut est 250 ms.
    Délai total d’expiration en secondes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000
  
    Sur Azure Web Apps, le délai total d’expiration dans iisnode est de 200 \* 250 ms = 50 secondes par défaut.
* logDirectory
  
    Ce paramètre contrôle le répertoire dans lequel iisnode enregistre stdout/stderr. La valeur par défaut est iisnode ; cela se rapporte au répertoire de script principal, c’est-à-dire au répertoire qui contient le fichier server.js principal
* debuggerExtensionDll
  
    Ce paramètre contrôle la version de node inspector qu’utilise iisnode durant le débogage de votre application node. Les versions iisnode-inspector-0.7.3.dll et iisnode-inspector.dll sont actuellement les deux seules valeurs valides pour ce paramètre. La valeur par défaut est iisnode-inspector 0.7.3.dll. La version iisnode-inspector-0.7.3.dll utilise node-inspector-0.7.3 et des websockets. Vous devez activer les websockets sur votre application web Azure pour utiliser cette version. Consultez <http://www.ranjithr.com/?p=98> pour plus d’informations sur la manière de configurer iisnode pour utiliser le nouveau node-inspector.
* flushResponse
  
    Par défaut, IIS met en mémoire tampon les données de réponse jusqu’à une limite de 4 Mo avant le vidage de la mémoire ou jusqu’à la fin de la réponse, selon la première occurrence. iisnode offre un paramètre de configuration permettant de remplacer ce comportement : pour vider un fragment du corps d’entité réponse dès qu’iisnode le reçoit de node.exe, vous devez définir l’attribut iisnode/@flushResponse dans le fichier web.config sur « true » :
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    L’activation du vidage de chaque fragment du corps de l’entité de réponse entraîne une dégradation des performances qui réduit le débit du système de l’ordre de ~5 % (à partir de la v0.1.13). Il est donc préférable de limiter ce paramètre uniquement aux points de terminaison qui requièrent un streaming des réponses (par exemple, à l’aide de l’élément <location> dans le fichier web.config).
  
    En outre, pour les applications de streaming, vous devez également définir le paramètre responseBufferLimit de votre gestionnaire iisnode sur 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Liste de fichiers séparés par des points-virgules dont les modifications font l’objet d’une surveillance. Une modification apportée à un fichier entraînera un recyclage de l’application. Chaque entrée se compose d’un nom de répertoire facultatif, auquel s’ajoute un nom de fichier obligatoire. Ces noms dépendent du répertoire où se trouve le point d’entrée principal de l’application. Les caractères génériques sont autorisés uniquement dans la partie de nom de fichier. La valeur par défaut est « \*. js;web.config ».
* recycleSignalEnabled
  
    La valeur par défaut est false. Si ce paramètre est activé, votre application node peut se connecter à un canal nommé (variable d’environnement IISNODE\_CONTROL\_PIPE) et envoyer un message de « recyclage ». Le w3wp est alors recyclé normalement.
* idlePageOutTimePeriod
  
    La valeur par défaut est 0, ce qui signifie que cette fonctionnalité est désactivée. Si ce paramètre est défini sur une valeur supérieure à 0, iisnode effectuera un page-out sur tous ses processus enfants toutes les « idlePageOutTimePeriod » millisecondes. Consultez cette [documentation](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) pour mieux comprendre le concept de page-out. Ce paramètre est pour les applications qui consomment une grande quantité de mémoire et qui cherchent à déplacer occasionnellement des données de la mémoire vers le disque (page-out) pour libérer de la mémoire RAM.

> [!WARNING]
> Soyez vigilant lorsque vous activez les paramètres de configuration suivants sur les applications de production. Nous vous recommandons de ne pas les activer sur les applications de production en direct.
> 
> 

* debugHeaderEnabled
  
    La valeur par défaut est false. Si la valeur true est définie, iisnode ajoute un en-tête de réponse HTTP iisnode-debug pour chaque réponse HTTP envoyée. La valeur d’en-tête iisnode-debug est une URL. Vous pouvez examiner le fragment d’URL pour obtenir des éléments d’informations de diagnostic, mais le mieux est d’ouvrir l’URL dans un navigateur.
* loggingEnabled
  
    Ce paramètre contrôle la journalisation de stdout et stderr par iisnode. Iisnode récupère stdout/stderr à partir des processus node qu’il exécute et écrit dans le répertoire spécifié dans le paramètre « logDirectory ». Une fois ce paramètre activé, votre application écrit les fichiers journaux dans le système de fichiers. Le niveau de journalisation exécuté par l’application peut avoir des conséquences sur les performances.
* devErrorsEnabled
  
   La valeur par défaut est false. Quand la valeur est définie sur true, iisnode affiche le code d’état HTTP et le code d’erreur Win32 sur votre navigateur. Le code win32 est utile durant le débogage de certains types de problèmes.
* debuggingEnabled (ne pas activer ce paramètre sur un site de production en direct)
  
    Ce paramètre contrôle la fonctionnalité de débogage. iisnode est intégré à node-inspector. En activant ce paramètre, vous activez le débogage de votre application node. Une fois ce paramètre activé, iisnode place les fichiers node-inspector nécessaires dans le répertoire « debuggerVirtualDir » à la première demande de débogage transmise à votre application node. Vous pouvez charger le fichier node-inspector en envoyant une demande à l’adresse http://yoursite/server.js/debug. Vous pouvez contrôler le segment d’URL de débogage à l’aide du paramètre « debuggerPathSegment ». Par défaut, debuggerPathSegment=’debug’. Vous pouvez définir ce paramètre sur un GUID par exemple, afin qu’il soit plus difficilement découvrable par les autres utilisateurs.
  
    Consultez ce [lien](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) pour plus d’informations sur le débogage.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénarios et recommandations/résolution des pannes
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Mon application node émet trop d’appels sortants.
La plupart des applications cherchent à établir des connexions sortantes dans le cadre de leur fonctionnement normal. Par exemple, à l’arrivée d’une requête, votre application node va chercher à contacter une API REST à un autre emplacement et à obtenir des informations afin de traiter cette requête. Vous pouvez utiliser un agent keep alive lors des appels http ou https. Par exemple, vous pouvez utiliser le module agentkeepalive en tant qu’agent keep alive lors de l’exécution de ces appels sortants. Cela permet de s’assurer que les sockets seront réutilisés sur votre machine virtuelle Azure Webapps, tout en réduisant la charge liée à la création de sockets pour chaque requête sortante. En outre, cela vous permet d’utiliser moins de sockets pour exécuter de nombreuses requêtes sortantes, et donc ne pas dépasser la valeur maxSockets allouée par machine virtuelle. Sur Azure Web Apps, il est recommandé de définir la valeur maxSockets du module agentKeepAlive sur un total de 160 sockets par machine virtuelle. Autrement dit, si vous avez quatre node.exe en cours d’exécution sur votre machine virtuelle, vous pouvez définir le paramètre maxSockets du module agentKeepAlive sur 40 par node.exe, soit un total de 160 par machine virtuelle.

Exemple de configuration [d’agentKeepALive](https://www.npmjs.com/package/agentkeepalive) :

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Cet exemple suppose que 4 node.exe sont en cours d’exécution sur votre machine virtuelle. Si vous avez un nombre différent de node.exe en cours d’exécution sur la machine virtuelle, vous devez modifier le paramètre maxSockets en conséquence.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Mon application node consomme trop de ressources processeur.
Azure Web Apps vous enverra probablement une recommandation sur votre portail concernant le niveau de consommation processeur considéré comme élevé. Vous pouvez également configurer des analyses pour surveiller certaines [métriques](web-sites-monitor.md). Pendant la vérification de l’utilisation du processeur sur le [tableau de bord du Portail Azure](../application-insights/app-insights-web-monitor-performance.md), vérifiez les valeurs MAX de processeur afin d’éviter de passer à côté des pics de valeur.
Si vous pensez que votre application node consomme trop de ressources processeur, vous pouvez en rechercher la cause en la profilant.

### 
#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Profilage de votre application node sur Azure Web Apps à l’aide de V8-Profiler
Par exemple, supposons que vous disposez d’une application hello world que vous souhaitez profiler comme suit :

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Accédez à votre site SCM : https://yoursite.scm.azurewebsites.net/DebugConsole

Vous accédez à une invite de commandes, comme indiqué ci-dessous. Allez dans votre répertoire site/wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Exécutez la commande « npm install v8-profiler ».

Le programme v8-profiler et toutes ses dépendances devraient s’installer sous le répertoire node\_modules.
À présent, modifiez votre fichier server.js pour profiler votre application.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Le code précédent profile la fonction WriteConsoleLog, puis écrit la sortie du profil dans le fichier « profile.cpuprofile » sous le répertoire wwwroot de votre site. Envoyez une requête à votre application. Vous voyez qu’un fichier « profile.cpuprofile » a été créé sous le répertoire wwwroot de votre site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Téléchargez ce fichier et ouvrez-le avec la fonctionnalité Outils de Chrome (F12). Appuyez sur F12 dans Chrome, puis choisissez l’onglet **Profils**. Choisissez le bouton **Charger**. Sélectionnez le fichier profile.cpuprofile que vous avez téléchargé. Cliquez sur le profil que vous venez de télécharger.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Vous pouvez voir que 95 % du temps a été consommé par la fonction WriteConsoleLog. Cela vous montre également les numéros de ligne et les fichiers source qui sont à l’origine du problème.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Mon application node consomme trop de mémoire
Si votre application consomme trop de mémoire, votre portail affiche une notification d’Azure Web Apps indiquant une consommation de mémoire élevée. Vous pouvez configurer des analyses pour surveiller certaines [métriques](web-sites-monitor.md). Pendant la vérification de l’utilisation de mémoire sur le [tableau de bord du Portail Azure](../application-insights/app-insights-web-monitor-performance.md), vérifiez les valeurs MAX de la mémoire afin d’éviter de passer à côté des pics de valeur.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Détection des fuites et procédure de heap diffing pour node.js
Vous pouvez utiliser [node-memwatch](https://github.com/lloyd/node-memwatch) pour vous aider à identifier les fuites de mémoire.
Vous pouvez installer memwatch en suivant la même procédure que pour v8-profiler et modifier votre code pour capturer et comparer les tas afin d’identifier les fuites de mémoire dans votre application.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Mes fichiers node.exe sont arrêtés de façon aléatoire
Cette situation peut avoir diverses raisons :

1. Votre application lève des exceptions non interceptées ; consultez le fichier d:\\home\\LogFiles\\Application\\logging-errors.txt pour obtenir plus d’informations sur l’exception levée. Ce fichier contient l’arborescence des appels de procédure qui vous permet de corriger votre application.
2. Votre application consomme trop de mémoire, ce qui affecte le démarrage des autres processus. Si la mémoire totale de la machine virtuelle est proche de 100 %, votre node.exe risque d’être arrêté par le Gestionnaire de processus afin de laisser aux autres processus l’occasion d’exécuter une tâche. Pour résoudre ce problème, vérifiez l’absence de fuite de mémoire au niveau de votre application OU, si votre application a besoin d’une très grande quantité de mémoire, basculez vers une plus grande machine virtuelle comportant une capacité de RAM supérieure.

### <a name="my-node-application-does-not-start"></a>Mon application node ne démarre pas
Si votre application retourne des erreurs 500 au démarrage, plusieurs raisons peuvent expliquer ce phénomène :

1. Le fichier node.exe ne se trouve pas à l’emplacement correct. Vérifiez le paramètre nodeProcessCommandLine.
2. Le fichier de script principal ne se trouve pas à l’emplacement correct. Vérifiez le fichier web.config et assurez-vous que le nom du fichier de script principal dans la section des gestionnaires correspond au fichier de script principal.
3. La configuration du fichier web.config est incorrecte : vérifiez les noms et les valeurs des paramètres.
4. Démarrage à froid : votre application prend trop de temps à démarrer. Si votre application met plus de (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 secondes à démarrer, iisnode retourne une erreur 500. Augmentez les valeurs de ces paramètres pour correspondre à l’heure de début de votre application afin d’éviter qu’iisnode dépasse le délai d’expiration et retourne une erreur 500.

### <a name="my-node-application-crashed"></a>Mon application node a planté
Votre application lève des exceptions non interceptées ; consultez le fichier d:\\home\\LogFiles\\Application\\logging-errors.txt pour obtenir plus d’informations sur l’exception levée. Ce fichier contient l’arborescence des appels de procédure qui vous permet de corriger votre application.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Mon application node prend trop de temps au démarrage (démarrage à froid)
En règle générale, le temps de démarrage excessif d’une application est dû à la présence d’un nombre élevé de fichiers dans le node\_modules. L’application tente de charger la plupart de ces fichiers au démarrage. Par défaut, puisque vos fichiers résident sur le partage réseau dans Azure Web Apps, le chargement d’un grand nombre de fichiers peut prendre du temps.
Voici quelques solutions qui pourront vous aider à accélérer le processus :

1. Utilisez npm3 pour installer vos modules de manière à avoir une structure de dépendances plate et à éviter toute dépendance en double.
2. Essayez de charger progressivement votre node\_modules plutôt que de charger tous les modules au démarrage de l’application. Cela signifie que vous devez appeler require('module') quand vous en avez réellement besoin dans la fonction que vous testez pendant l’utilisation du module.
3. Azure Web Apps offre une fonctionnalité dite de « cache local ». Cette fonctionnalité copie le contenu du partage réseau sur le disque local de la machine virtuelle. Étant donné que les fichiers sont en local, le temps de chargement du node\_modules est beaucoup plus rapide.

## <a name="iisnode-http-status-and-substatus"></a>État et sous-état http IISNODE
Ce [fichier source](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) répertorie toutes les combinaisons d’état/sous-état qu’iisnode peut retourner en cas d’erreur.

Activez FREB pour votre application afin de voir le code d’erreur win32 (pour des raisons de performances, veillez à activer FREB uniquement sur les sites de test).

| État HTTP | Sous-état HTTP | Raison possible ? |
| --- | --- | --- |
| 500 |1 000 |Un problème est survenu au moment de l’envoi de la requête à IISNODE : vérifiez si node.exe a bien été démarré. Node.exe peut avoir été bloqué au démarrage. Vérifiez la configuration de votre fichier web.config afin d’identifier d’éventuelles erreurs. |
| 500 |1001 |-Win32Error 0 x 2 - L’application ne répond pas à l’URL. Vérifiez les règles de réécriture d’URL ou assurez-vous que les routes définies pour votre application sont correctes. -Win32Error 0x6d – le canal nommé est occupé : node.exe n’accepte pas les requêtes car le canal est occupé. Vérifiez l’utilisation élevée du processeur. -Autres erreurs : vérifiez si node.exe a planté. |
| 500 |1002 |node.exe a planté : consultez l’arborescence des appels de procédure sous d:\\home\\LogFiles\\logging-errors.txt. |
| 500 |1003 |Problème de configuration du canal : cet état ne devrait jamais apparaître, mais dans le cas contraire, cela signifie que la configuration du canal nommé est incorrecte. |
| 500 |1004-1018 |Une erreur s’est produite lors de l’envoi de la requête ou du traitement de la réponse depuis/vers node.exe. Vérifiez si node.exe a planté. Consultez l’arborescence des appels de procédure sous d:\\home\\LogFiles\\logging-errors.txt. |
| 503 |1 000 |Mémoire insuffisante pour allouer davantage de connexions de canal nommé. Vérifiez pourquoi votre application consomme une grande quantité de mémoire. Vérifiez la valeur du paramètre maxConcurrentRequestsPerProcess. Si ce paramètre n’est pas défini sur « infinite » et que vous avez un grand nombre de requêtes, augmentez cette valeur pour éviter cette erreur. |
| 503 |1001 |Impossible de distribuer la requête à node.exe en raison du recyclage de l’application. Une fois que l’application est recyclée, les requêtes devraient être traitées normalement. |
| 503 |1002 |Vérifiez le code d’erreur win32 pour connaître la raison : impossible de distribuer la requête à un node.exe. |
| 503 |1003 |Le canal nommé est trop occupé : vérifiez si nœud consomme trop de ressources processeur |

NODE.exe contient un paramètre appelé NODE\_PENDING\_PIPE\_INSTANCES. Par défaut, cette valeur est définie sur 4 en dehors d’Azure Web Apps. Cela signifie que node.exe ne peut accepter que quatre requêtes à la fois sur le canal nommé. Sur Azure Web Apps, cette valeur est définie sur 5000. Elle doit être suffisante pour la plupart des applications node exécutées sur Azure Web Apps. Vous ne devriez pas rencontrer l’état 503.1003 sur Azure Web Apps, car le paramètre NODE\_PENDING\_PIPE\_INSTANCES est défini sur une valeur élevée.  |

## <a name="more-resources"></a>Autres ressources
Cliquez sur ces liens pour en savoir plus sur les applications node.js dans Azure App Service.

* [Prise en main des applications web Node.js dans Azure App Service](app-service-web-get-started-nodejs.md)
* [Débogage d’une application web Node.js dans Azure Web Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps : Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centre de développement Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Exploration de la console de débogage Kudu Super Secret)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

