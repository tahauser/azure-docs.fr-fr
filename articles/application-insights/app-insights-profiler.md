---
title: Profiler des applications web dynamiques sur Azure avec Application Insights Profiler | Microsoft Docs
description: "Identifiez le chemin réactif dans le code de votre serveur web avec un profileur de faible encombrement."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 80792a82adbb93e80c94b4829b704b70d2a8ed23
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profiler des applications web dynamiques sur Azure avec Application Insights

*Cette fonctionnalité d’Application Insights est généralement disponible pour Azure App Service et est en version préliminaire pour les ressources de calcul Azure.*

Découvrez combien de temps vous consacrez à chaque méthode de votre application web en production quand vous utilisez [Application Insights](app-insights-overview.md). L’outil de profilage Application Insights vous montre les profils détaillés des requêtes dynamiques qui ont été traitées par votre application et met en évidence le *chemin réactif* qui est le plus souvent utilisé. Des requêtes avec divers temps de réponses sont profilées par échantillonnage. La charge pesant sur l’application est réduite au moyen de divers techniques.

Le profileur fonctionne actuellement pour les applications web ASP.NET et ASP.NET Core s’exécutant sur Azure App Service. L’utilisation du profileur nécessite au minimum le niveau de service **De base**.

## <a id="installation"></a> Activer le profileur pour l’application web App Services
Si vous avez déjà publié l’application sur une instance App Services, mais n’avez pas modifié le code source afin d’utiliser Application Insights, accédez au panneau App Services sur le portail Azure, accédez à **Surveillance| Application Insights**, puis suivez les instructions sur le panneau pour créer une ressource ou sélectionner une ressource Application Insights existante dédiée à la surveillance de votre application web.

![Activer App Insights sur le portail App Services][appinsights-in-appservices]

Si vous avez accès au code source de votre projet, [installez Application Insights](app-insights-asp-net.md). S’il est déjà installé, assurez-vous que vous disposez de la version la plus récente. Pour vérifier la version la plus récente, dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur votre projet, puis sélectionnez **Gérer les packages NuGet** > **Mises à jour** > **Mettre à jour tous les packages**. Ensuite, déployez votre application.

Les applications ASP.NET Core nécessitent l’installation du package NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 ou ultérieur pour fonctionner avec le profileur. À compter du 27 juin 2017, les versions antérieures ne sont plus prises en charge.

Dans [le portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights correspondant à votre application web. Sélectionnez **Performances** > **Activer Application Insights Profiler**.

![Sélectionner la bannière Activer le profileur][enable-profiler-banner]

Sinon, vous pouvez sélectionner la configuration **Profileur** pour afficher l’état du profileur, et l’activer ou le désactiver.

![Sous Performances, sélectionnez la configuration du Profileur.][performance-blade]

Les applications web configurées avec Application Insights sont répertoriées dans le panneau de configuration du **Profileur**. Si vous avez suivi les étapes ci-dessus, l’agent du profileur doit d’ores et déjà être installé. Sélectionnez **Activer le profileur** dans le panneau de configuration du **Profileur**.

Suivez les instructions pour installer l’agent profileur, si nécessaire. Si aucune application web n’a été configurée avec Application Insights, sélectionnez **Ajouter des applications liées**.

![Configurer les options du volet][linked app services]

Contrairement aux applications web hébergées par le biais de plans App Service, les applications hébergées dans des ressources de calcul Azure (par exemple, une machine virtuelle Azure, un groupe identique de machines virtuelles, Azure Service Fabric et les services cloud Azure) ne sont pas gérées directement par Azure. Dans ce cas, il n’y a aucune application web à associer. Au lieu d’établir une liaison avec une application, sélectionnez le bouton **Activer le profileur**.

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>Activer le profileur pour les ressources de calcul Azure (version préliminaire)

Obtenez des informations sur une [version préliminaire du profileur pour les ressources de calcul Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Afficher les données du profileur

**Vérifiez que votre application reçoit le trafic.** Si vous faites une expérience, vous pouvez générer des requêtes pour votre application web à l’aide des [tests de performances Application Insights](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Si vous venez d’activer le profileur, vous pouvez exécuter un bref test de charge durant environ 15 minutes, ce qui doit générer des traces du profileur. Si le profileur est activé depuis déjà un moment, notez qu’il s’exécute de manière aléatoire deux fois par heure pendant une durée de deux minutes. Nous vous suggérons d’exécuter le test de charge sur une durée d’une heure pour vous assurer d’obtenir des échantillons de traces du profileur.

Une fois que votre application reçoit du trafic, accédez au panneau **Performances** > **Prendre des mesures** pour afficher les traces du profileur. Sélectionnez le bouton **Traces du profileur**.

![Aperçu du volet de performances Application Insights - Traces du profileur][performance-blade-v2-examples]

Sélectionnez un exemple pour afficher, au niveau du code, une répartition du temps d’exécution de la requête.

![Explorateur de trace d’Application Insights][trace-explorer]

L’Explorateur de trace affiche les informations suivantes :

* **Afficher le chemin réactif** : ouvre le plus grand nœud feuille, ou du moins un élément qui s’en rapproche. Dans la plupart des cas, ce nœud est adjacent à un goulot d’étranglement.
* **Étiquette** : nom de la fonction ou de l’événement. L’arborescence affiche une combinaison des codes et des événements qui se sont produits (par exemple, des événements SQL et HTTP). L’événement supérieur représente la durée globale de la requête.
* **Écoulé** : intervalle de temps entre le début de l’opération et la fin.
* **Quand** : indique à quel moment la fonction ou l’événement était en cours d’exécution par rapport à d’autres fonctions.

## <a name="how-to-read-performance-data"></a>Comment lire les données de performances

Le profileur de service Microsoft utilise un ensemble de méthodes d’échantillonnage et une instrumentation pour analyser les performances de votre application. Durant la collecte détaillée, le profileur de service échantillonne le pointeur d’instruction de chaque processeur de l’ordinateur à chaque milliseconde. Chaque échantillon capture l’ensemble de la pile des appels du thread en cours d’exécution. Il fournit des informations détaillées sur l’activité de ce thread, à un niveau d’abstraction élevé et faible. Le profileur de service collecte également d’autres événements afin de suivre la corrélation et la causalité des activités, tels que les événements de changement de contexte, les événements TPL (Task Parallel Library) et les événements de pool de threads.

La pile des appels présentée dans l’affichage chronologique est le résultat de l’échantillonnage et de l’instrumentation. Étant donné que chaque échantillon capture l’ensemble de la pile des appels du thread, il inclut le code du Microsoft .NET Framework et des autres frameworks que vous référencez.

### <a id="jitnewobj"></a>Allocation d’objets (clr!JIT\_Nouveau ou clr!JIT\_Newarr1)
**clr!JIT\_Nouveau** et **clr!JIT\_Newarr1** sont des fonctions d’assistance du framework .NET qui allouent la mémoire à partir d’un tas managé. **clr!JIT\_Nouveau** est appelé lorsqu’un objet est alloué. **clr!JIT\_Newarr1** est appelé lorsqu’un tableau d’objet est alloué. Ces deux fonctions sont généralement rapides et s’exécutent en relativement peu de temps. Si vous constatez que **clr!JIT\_Nouveau** ou **clr!JIT\_Newarr1** prend un certain temps dans votre scénario, cela indique que le code alloue peut-être de nombreux objets et qu’il consomme une quantité importante de mémoire.

### <a id="theprestub"></a>Code de chargement (clr!ThePreStub)
**clr!ThePreStub** est une fonction d’assistance intégrée au framework .NET qui prépare le code en vue de sa première exécution. Elle implique en général au moins une compilation JIT (juste à temps). Pour chaque méthode C#, **clr!ThePreStub** doit être appelé au maximum une fois pendant la durée de vie d’un processus.

Si **clr!ThePreStub** prend un certain temps pour traiter une requête, cela indique que la requête est la première à exécuter cette méthode. Le runtime du .NET Framework a besoin de temps pour charger la première méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent, ou encore envisager d’exécuter Native Image Generator (ngen.exe) sur vos assemblys.

### <a id="lockcontention"></a>Contention de verrouillage (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indique que le thread actuel est en attente d’ouverture d’un verrou. Cela se produit généralement lors de l’exécution d’une instruction **LOCK** C#, lors de l’appel de méthode **Monitor.Enter** ou encore lors de l’appel d’une méthode avec l’attribut **MethodImplOptions.Synchronized**. La contention de verrouillage se produit généralement lorsque le thread _A_ acquiert un verrou et que le thread _B_ tente d’acquérir le même verrou avant qu’il ne soit libéré par le thread _A_.

### <a id="ngencold"></a>Code de chargement ([COLD])
Si le nom de la méthode contient **[COLD]**, par exemple **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, cela signifie que le runtime du framework .NET exécute pour la première fois du code qui n’utilise pas une <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimisation guidée par profil</a>. Pour chaque méthode, il doit s’afficher au maximum une fois pendant la durée de vie du processus.

Si le code de chargement prend beaucoup de temps pour traiter une requête, cela indique que la requête est la première à exécuter la partie non optimisée de la méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent.

### <a id="httpclientsend"></a>Envoyer une requête HTTP
Les méthodes telles que **HttpClient.Send** indiquent que le code attend l’exécution d’une requête HTTP.

### <a id="sqlcommand"></a>Opération de base de données
Une méthode telle que **SqlCommand.Execute** indique que le code attend l’exécution d’une opération de base de données.

### <a id="await"></a>En attente (AWAIT\_TIME)
**AWAIT\_TIME** indique que le code attend la fin de l’exécution d’une autre tâche. Cela se produit généralement avec l’instruction C# **AWAIT**. Lorsque le code exécute une instruction C# **AWAIT**, le thread se déroule et redonne le contrôle au pool de threads : aucun thread n’est bloqué en attendant la fin de l’exécution de l’instruction **AWAIT**. Toutefois, le thread qui a exécuté l’instruction **AWAIT** est logiquement « bloqué » en attendant que l’opération se termine. L’instruction **AWAIT\_TIME** indique le temps de blocage en attendant l’exécution de la tâche.

### <a id="block"></a>Temps de blocage
**BLOCKED_TIME** indique que le code attend qu’une autre ressource soit disponible. Il peut, par exemple, attendre un objet de synchronisation, la disponibilité d’un thread ou la fin d’une requête.

### <a id="cpu"></a>Temps processeur
Le processeur est occupé à exécuter les instructions.

### <a id="disk"></a>Temps du disque
L’application effectue les opérations de disque.

### <a id="network"></a>Temps réseau
L’application effectue les opérations réseau.

### <a id="when"></a>Colonne « When » (Quand)
La colonne **When** permet de voir comment les exemples INCLUSIFS collectés pour un nœud varient au fil du temps. La plage totale de la requête est divisée en 32 périodes. Les exemples inclusifs de ce nœud sont regroupés dans ces 32 périodes. Chaque période est représentée sous forme de barre. La hauteur de la barre représente une valeur à l’échelle. Pour les nœuds marqués **CPU_TIME** ou **BLOCKED_TIME**, ou lorsqu’il existe une relation évidente de consommation d’une ressource (processeur, disque, thread), la barre représente la consommation d’une de ces ressources au cours de cette période. Pour ces mesures, il est possible d’obtenir une valeur supérieure à 100 % en consommant plusieurs ressources. Par exemple, si vous utilisez en moyenne deux processeurs sur un intervalle donné, vous obtenez 200 %.

## <a name="limitations"></a>Limites

Par défaut, la durée de rétention des données est de 5 jours. La quantité maximale de données ingérées par jour est de 10 Go.

L’utilisation du service du profileur est gratuite. Pour utiliser le service du profileur, votre application web doit être hébergée au moins au niveau De base d’Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Surcharge et algorithme d’échantillonnage

Le profileur s’exécute de manière aléatoire pendant 2 minutes toutes les heures sur chaque machine virtuelle qui héberge l’application sur laquelle le profileur est activé pour capturer des traces. Lorsque le profileur est en cours d’exécution, il ajoute une surcharge d’UC de 5 à 15 % au serveur.
Plus le nombre de serveurs disponibles pour héberger l’application est important, moins le profileur a d’impact sur les performances globales de l’application. Cela est dû au fait qu’en raison de l’algorithme d’échantillonnage le profileur s’exécute à tout moment sur seulement 5 % des serveurs. Davantage de serveurs sont disponibles pour traiter les demandes web afin de compenser la surcharge du serveur provoquée par l’exécution du profileur.

## <a name="disable-the-profiler"></a>Désactiver le profileur
Pour arrêter ou redémarrer le profileur pour une instance App Service individuelle, accédez à la ressource App Service, sous **Tâches web**. Pour supprimer le profileur, accédez à **Extensions**.

![Désactiver le profileur pour des tâches web][disable-profiler-webjob]

Nous vous recommandons d’activer, dès que possible, le profileur sur toutes vos applications web afin de découvrir d’éventuels problèmes de performance.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier App_Data lors du déploiement. Dans le cas contraire, les fichiers de l’extension du profileur seront supprimés la prochaine fois que vous déploierez l’application web dans Azure.


## <a id="troubleshooting"></a>Résolution des problèmes

### <a name="too-many-active-profiling-sessions"></a>Trop de sessions de profilage actives

Actuellement, vous pouvez activer le profileur sur un maximum de quatre applications web Azure et emplacements de déploiement exécutés sur le même plan de service. Si la tâche web du profileur signale un nombre trop important de sessions de profilage actives, vous devez déplacer certaines applications web vers un autre plan de service.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Comment savoir si Application Insights Profiler est en cours d’exécution ?

Le profileur s’exécute comme une tâche web en continu dans l’application web. Vous pouvez ouvrir la ressource de l’application web dans le [portail Azure](https://portal.azure.com). Dans le volet **WebJobs**, vérifiez l’état de **ApplicationInsightsProfiler**. S’il n’est pas en cours d’exécution, ouvrez les **Journaux** pour en savoir plus.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Pourquoi aucun exemple de pile ne s’affiche même si le profileur est en cours d’exécution ?

Vous pouvez vérifier plusieurs points :

* Assurez-vous que votre plan de service d’application web est au moins au niveau De base.
* Assurez-vous que le SDK Application Insights 2.2 Bêta ou version ultérieure est activé sur votre application web.
* Vérifiez dans votre application web que le paramètre **APPINSIGHTS_INSTRUMENTATIONKEY** utilise la même clé d’instrumentation que celle utilisée par le SDK Application Insights.
* Assurez-vous que votre application web s’exécute sur .Net Framework 4.6.
* Si votre application web est une application ASP.NET Core, vérifiez [les dépendances requises](#aspnetcore).

Après le démarrage du profileur, vous devez vous attendre à une courte période de mise en route au cours de laquelle le profileur collecte activement plusieurs traces de performances. Après cela, le profileur collecte des traces de performances pendant deux minutes, toutes les heures.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>J’utilisais avant l’agent Azure Service Profiler. Qu’est-il devenu ?

Lorsque vous activez Application Insights Profiler, l’agent Azure Service Profiler est désactivé.

### <a id="double-counting"></a>Double comptage dans des threads parallèles

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée de la requête.

Cela peut se produire lorsque deux threads au moins sont associés à une requête et qu’ils s’exécutent en parallèle. Dans ce cas, le temps total de threads est supérieur au temps écoulé. Un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter et d’omettre les attentes sans intérêt, mais se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.

Quand vous voyez des threads parallèles dans vos traces, identifiez les threads en attente pour confirmer le chemin critique pour la requête. Dans la plupart des cas, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a id="issue-loading-trace-in-viewer"></a>Aucune donnée de profilage

Vous pouvez vérifier plusieurs points :

* Si les données que vous essayez d’afficher datent d’il y a plus de deux semaines, essayez de limiter votre filtre de temps puis réessayez.
* Vérifiez que votre proxy ou pare-feu n’a pas bloqué l’accès à https://gateway.azureserviceprofiler.net.
* Vérifiez que la clé d’instrumentation Application Insights que vous utilisez dans votre application est identique à celle de la ressource Application Insights avec laquelle vous avez activé le profilage. La clé se trouve généralement dans le fichier ApplicationInsights.config, mais peut également se trouver dans les fichiers web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Rapport d’erreurs dans la visionneuse de profilage

Soumettez un ticket de support dans le portail. Veillez à indiquer l’ID de corrélation du message d’erreur.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erreur de déploiement : Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Si vous redéployez votre application web vers une ressource App Service avec le profileur activé, un message similaire au message suivant peut s’afficher :

Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Cette erreur se produit si vous exécutez Web Deploy à partir de scripts ou du pipeline de déploiement Visual Studio Team Services. La solution consiste à ajouter les paramètres de déploiement supplémentaires suivants à la tâche Web Deploy :

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ces paramètres suppriment le dossier utilisé par Application Insights Profiler et débloquent le processus de redéploiement. Ils n’affectent pas l’instance de profileur est en cours d’exécution.


## <a name="manual-installation"></a>Installation manuelle

Lorsque vous configurez le profileur, des mises à jour sont appliquées aux paramètres de l’application web. Vous pouvez appliquer les mises à jour manuellement si votre environnement l’exige. C’est par exemple le cas si votre application s’exécute dans un environnement App Service pour PowerApps.

1. Dans le panneau de contrôle de l’application web, ouvrez **Paramètres**.
2. Définissez **Version du .NET Framework** sur **4.6**.
3. Activez l’option **Toujours actif**.
4. Ajoutez le paramètre d’application __APPINSIGHTS_INSTRUMENTATIONKEY__ et définissez la valeur sur la même clé d’instrumentation que celle utilisée par le kit de développement logiciel (SDK).
5. Ouvrez **Outils avancés**.
6. Sélectionnez **Aller à** pour ouvrir le site web Kudu.
7. Sur le site web Kudu, sélectionnez **Extensions de site**.
8. Installez __Application Insights__ à partir de la galerie Azure Web Apps.
9. Redémarrez l’application web.

## <a id="profileondemand"></a> Déclencher manuellement un profileur
Quand nous avons développé le profileur, nous avons ajouté une interface de ligne de commande pour pouvoir tester le profileur sur des services d’application. À l’aide de cette même interface, les utilisateurs peuvent également personnaliser le mode de démarrage du profileur. À un niveau élevé, le profileur utilise le système Kudu d’App Services pour gérer le profilage en arrière-plan. Quand vous installez l’extension Application Insights, nous créons une tâche web continue qui héberge le profileur. Nous allons utiliser cette même technologie pour créer un projet web que vous pouvez personnaliser selon vos besoins.

Cette section explique comment :

1. Créer une tâche web permettant de démarrer le profileur pendant deux minutes en appuyant sur un bouton.
2. Créer une tâche web permettant de planifier l’exécution du profileur.
3. Définissez les arguments du profileur.


### <a name="set-up"></a>Installation
Dans un premier temps, familiarisez-vous avec le tableau de bord de la tâche web. Sous Paramètres, cliquez sur l’onglet Tâches web.

![Panneau Tâches Web](./media/app-insights-profiler/webjobs-blade.png)

Comme vous pouvez l’observer, ce tableau de bord affiche toutes les tâches web actuellement installées sur votre site. Vous pouvez voir la tâche web ApplicationInsightsProfiler2 dont la tâche de profileur est en cours d’exécution. La création de nos tâches web pour le profilage manuel et planifié prendra fin à ce stade.

Dans un premier temps, nous devons obtenir les fichiers binaires nécessaires.

1.  Accédez au site Kudu. Sous l’onglet des outils de développement, cliquez sur l’onglet « Outils avancés » avec le logo Kudu. Cliquez sur « Accéder ». Vous êtes dirigé vers un nouveau site auquel vous êtes automatiquement connecté.
2.  Nous devons ensuite télécharger les fichiers binaires du profileur. Accédez à l’Explorateur de fichiers en sélectionnant Console de débogage -> CMD en haut de la page.
3.  Cliquez sur site -> wwwroot -> App_Data -> jobs -> continuous. Vous devez voir un dossier nommé « ApplicationInsightsProfiler2 ». Cliquez sur l’icône de téléchargement située à gauche du dossier. Le fichier « ApplicationInsightsProfiler2.zip » est téléchargé.
4.  Ce téléchargement comprend tous les fichiers dont vous avez besoin. Je vous recommande de créer un répertoire vide vers lequel déplacer cette archive zip avant de continuer.

### <a name="setting-up-the-web-job-archive"></a>Configuration de l’archive de la tâche web
Quand vous ajoutez une nouvelle tâche web au site web Azure, vous créez en fait une archive zip contenant un fichier run.cmd. Le fichier run.cmd indique au système de tâches web ce qu’il doit faire quand vous exécutez la tâche web.

1.  Pour commencer, créez un dossier. Dans notre exemple, ce dossier est nommé « RunProfiler2Minutes ».
2.  Copiez les fichiers du dossier ApplicationInsightProfiler2 extrait dans ce nouveau dossier.
3.  Créez un nouveau fichier run.cmd (pour des raisons pratiques, vous pouvez ouvrir ce dossier de travail dans VS Code avant de commencer).
4.  Ajoutez la commande `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` et enregistrez le fichier.
a.  La commande `start` ordonne au profileur de démarrer.
b.  `--engine-mode immediate` indique au profileur que nous voulons démarrer immédiatement le profilage.
c.  `--single` indique que l’exécution sera suivie d’un arrêt automatique. d.  `--immediate-profiling-duration 120` indique que le profileur doit s’exécuter pendant 120 secondes (2 minutes).
5.  Enregistrez ce fichier.
6.  Archivez ce dossier. Pour ce faire, vous pouvez cliquer avec le bouton droit sur le dossier et sélectionner Envoyer vers -> Fichier compressé (zippé). Un fichier .zip est créé avec le nom de votre dossier.

![Commande de démarrage du profileur](./media/app-insights-profiler/start-profiler-command.png)

Nous avons à présent un fichier zip de tâche web que nous pouvons utiliser pour configurer des tâches web dans notre site.

### <a name="add-a-new-web-job"></a>Ajouter une nouvelle tâche web
Ajoutons maintenant une nouvelle tâche web dans notre site. Cet exemple explique comment ajouter une tâche web à déclenchement manuel. Une fois que vous savez le faire, le processus est pratiquement identique pour un déclenchement planifié.

1.  Accédez au tableau de bord Tâches web.
2.  Dans la barre d’outils, cliquez sur la commande Ajouter.
3.  Donnez un nom à votre tâche web. Par souci de clarté, il peut être utile d’utiliser le même nom que celui de votre archive et de l’ouvrir pour avoir différentes versions du fichier run.cmd.
4.  Dans la section de chargement de fichiers du formulaire, cliquez sur l’icône Ouvrir un fichier et recherchez le fichier .zip créé précédemment.
5.  Sous Type, sélectionnez l’option Déclenchée.
6.  Sous Déclencheurs, sélectionnez l’option Manuel.
7.  Cliquez sur OK pour enregistrer les paramètres.

![Commande de démarrage du profileur](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>Exécuter le profileur

Maintenant que nous avons une nouvelle tâche web que nous pouvons déclencher manuellement, essayons de l’exécuter.

1. La conception ne permet d’exécuter qu’un seul processus ApplicationInsightsProfiler.exe à la fois sur une machine. Pour commencer, veillez donc à désactiver la tâche web continue de ce tableau de bord. Cliquez sur la ligne correspondante et appuyez sur « Arrêter ». Ensuite, sélectionnez Actualiser sur la barre d’outils et vérifiez que l’état indique que la tâche est arrêtée.
2. Cliquez sur la ligne de la nouvelle tâche web que vous avez ajoutée, puis sur Exécuter.
3. En maintenant la ligne sélectionnée, cliquez sur la commande Journaux dans la barre d’outils pour afficher un tableau de bord des tâches web pour la tâche web que vous avez démarrée. Celui-ci répertorie les dernières exécutions et leurs résultats.
4. Cliquez sur l’instance de l’exécution que vous venez de démarrer.
5. Vous devriez normalement voir des journaux de diagnostic provenant du profileur qui confirment que le profilage a bien démarré.

### <a name="things-to-consider"></a>Points importants à prendre en compte

Bien que cette méthode soit relativement simple, plusieurs aspects sont à prendre en compte.

- Cette méthode n’étant pas gérée par notre service, nous n’avons aucun moyen de mettre à jour les fichiers binaires de l’agent pour votre tâche web. Nous ne disposons actuellement pas de page de téléchargement stable pour nos fichiers binaires. La seule façon d’obtenir le fichier le plus récent est donc de mettre à jour votre extension et de récupérer le fichier à partir du dossier continu, comme nous l’avons fait précédemment.

- Étant donné que cette méthode utilise les arguments de ligne de commande conçus à l’origine pour une utilisation de développeur et non d’utilisateur, ces arguments sont susceptibles d’être modifiés à l’avenir. Tenez-en compte en cas de mise à niveau. Cela ne devrait pas causer de problème particulier, car vous pouvez ajouter une tâche web, l’exécuter et vérifier qu’elle fonctionne correctement. Nous créerons finalement l’interface utilisateur pour ce faire sans le processus manuel.

- La fonctionnalité Tâches web pour App Services est unique dans le sens où durant l’exécution de la tâche web, elle s’assure que votre processus a les mêmes variables d’environnement et paramètres d’application que ceux que votre site web aura. Cela signifie que vous n’avez pas besoin de transmettre la clé d’instrumentation au profileur par le biais de la ligne de commande. La clé d’instrumentation doit simplement être récupérée de l’environnement. Toutefois, si vous souhaitez exécuter le profileur dans votre boîte de développement ou sur une machine en dehors d’App Services, vous devez fournir une clé d’instrumentation. Pour ce faire, vous pouvez transmettre un argument `--ikey <instrumentation-key>`. Cette valeur doit correspondre à la clé d’instrumentation que votre application utilise. La sortie du journal du profileur indique la clé d’instrumentation avec laquelle le profileur a démarré et si une activité a été détectée à partir de cette clé d’instrumentation durant le profilage.

- Les tâches web à déclenchement manuel peuvent de fait être déclenchées via Webhook. Vous pouvez obtenir cette URL en cliquant avec le bouton droit sur la tâche web dans le tableau de bord et en affichant les propriétés. Vous pouvez également choisir les propriétés dans la barre d’outils après avoir sélectionné la tâche web dans la table. Il en résulte un très grand nombre de possibilités. Vous pouvez ainsi déclencher le profileur à partir de votre pipeline d’intégration continue/de déploiement continu (comme VSTS) ou d’un outil tel que Microsoft Flow (https://flow.microsoft.com/en-us/). Cela dépend du niveau de complexité de votre fichier run.cmd (qui peut également être de type run.ps1). Mais comme vous pouvez le constater, les possibilités sont nombreuses.

## <a name="next-steps"></a>étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
