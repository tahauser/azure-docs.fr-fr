---
title: "Analyse de la disponibilité et de la réactivité d’un site Web | Microsoft Docs"
description: "Configurez des tests web dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2018
ms.author: sdash ; mbullwin
ms.openlocfilehash: d8d6c6a242f63ad891a8134657273ff73dfcde18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Analyse de la disponibilité et de la réactivité d'un site Web
Après avoir déployé votre application web ou votre site web sur un serveur, vous pouvez configurer des tests pour surveiller sa disponibilité et sa réactivité. [Azure Application Insights](app-insights-overview.md) envoie des requêtes web à votre application à intervalles réguliers à partir de différents points du monde, et vous alerte si votre application réagit lentement ou pas du tout.

Vous pouvez configurer des tests de disponibilité pour n’importe quel point de terminaison HTTP ou HTTPS accessible à partir du réseau Internet public. Vous n’avez rien à ajouter au site web que vous testez. Il ne faut pas nécessairement qu’il s’agisse de votre site : vous pouvez tester un service API REST dont vous dépendez.

Il existe deux types de tests de disponibilité :

* [Test ping d’URL](#create): un test simple que vous pouvez créer dans le portail Azure.
* [Test web multi-étapes](#multi-step-web-tests) : que vous créez dans Visual Studio Enterprise et que vous chargez sur le portail.

Vous pouvez créer jusqu’à 100 tests de disponibilité par ressource d’application.


> [!NOTE] 
> * Les emplacements de test de disponibilité ont récemment été déplacés vers les centres de données Azure. Ce déplacement permet d’ajouter des emplacements avec le réseau croissant de centres de données Azure.  
> * Vous n’avez pas besoin de mettre à jour les tests. Tous les tests sont migrés et en cours d’exécution à partir de nouveaux emplacements. 
>* Pour plus d’informations, reportez-vous à la [mise à jour de service](https://blogs.msdn.microsoft.com/applicationinsights-status/2018/01/24/application-insights-availability-monitoring-test-locations-updated/).

## <a name="create"></a>Ouvrir une ressource pour vos rapports de test de disponibilité

**Si vous avez déjà configuré Application Insights** pour votre application web, ouvrez sa ressource Application Insights dans le [portail Azure](https://portal.azure.com).

**Ou bien, si vous souhaitez visualiser vos rapports dans une nouvelle ressource,** accédez au [portail Azure](https://portal.azure.com), puis créez une ressource Application Insights.

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Cliquez sur **Toutes les ressources** pour ouvrir le panneau Vue d’ensemble de la nouvelle ressource.

## <a name="setup"></a>Créer un test Ping d’URL
Ouvrez le panneau de disponibilité et ajoutez un test.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **L’URL** peut être n’importe quelle page web que vous souhaitez tester, mais elle doit être visible à partir de l’Internet public. L’URL peut contenir une chaîne de requête. Vous pouvez donc, par exemple, tester un peu votre base de données. Si l’URL correspond à une redirection, nous allons la suivre, jusqu’à 10 redirections.
* **Analyser les demandes dépendantes** : si cette option est activée, le test demande des images, des scripts, des fichiers de style et d’autres fichiers qui font partie de la page web de test. Le temps de réponse enregistré inclut le temps qui a été nécessaire pour obtenir ces fichiers. Le test échoue si toutes ces ressources ne peuvent pas être téléchargées avec succès dans le délai imparti pour l’ensemble du test. 

    Si l’option n’est pas cochée, le test ne demande que le fichier à l’URL spécifiée.

* **Autoriser de nouvelles tentatives**: si cette option est cochée, une nouvelle tentative de test sera effectuée après un court intervalle en cas d’échec du test. L’échec est signalé uniquement après trois tentatives infructueuses. Les tests suivants sont ensuite effectués selon la fréquence de test habituelle. La nouvelle tentative est temporairement suspendue jusqu’à la réussite de la tentative suivante. Cette règle est appliquée indépendamment à chaque emplacement de test. Nous recommandons cette option. En moyenne, environ 80 % des échecs disparaissent lors de la nouvelle tentative.

* **Fréquence de test**: définit la fréquence selon laquelle le test est exécuté à partir de chaque emplacement de test. Avec, par défaut, une fréquence de cinq minutes et cinq emplacements de test, votre site sera testé en moyenne une fois par minute.

* **emplacements de test** sont les lieux d’où nos serveurs envoient des requêtes web à votre URL. Choisissez-en plusieurs de façon à distinguer les problèmes de votre site web des problèmes de réseau. Vous pouvez sélectionner jusqu’à 16 emplacements.

* **Critères de réussite**:

    **Délai d’expiration de test**: diminuez cette valeur pour être averti des réponses lentes. Le test est compté comme une erreur si des réponses de votre site n’ont pas été reçues pendant cette période. Si vous avez sélectionné **Analyser les demandes dépendantes**, l’ensemble des images, fichiers de style, scripts et autres ressources dépendantes ont dû être reçus pendant cette période.

    **Réponse HTTP**: le code d’état retourné est comptabilisé comme un succès. 200 est le code qui indique qu’une page web normale a été retournée.

    **Correspondance de contenu**: une chaîne telle que « Bienvenue ! Nous vérifions qu’une correspondance exacte respectant la casse est présente dans chaque réponse. Il doit s'agir d'une chaîne standard sans caractère générique. N'oubliez pas que si votre contenu change, vous devrez peut-être l'actualiser.
* Des **alertes** vous sont envoyées par défaut, en cas de défaillance dans trois emplacements en cinq minutes. Une défaillance dans un emplacement est susceptible d’être un problème réseau et non un problème relatif à votre site. Cependant, vous pouvez modifier le seuil de manière à ce qu’il soit plus ou moins sensible. Vous pouvez également modifier les destinataires des courriers électroniques.

    Vous pouvez configurer un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) qui est appelé lorsqu’une alerte est déclenchée. (Mais notez qu’à l’heure actuelle, les paramètres de requête ne sont pas transmis en tant que propriétés.)

### <a name="test-more-urls"></a>Test d'autres URL
Ajoutez d’autres tests. Exemple : outre le test de votre page d’accueil, vous pouvez vérifier que votre base de données fonctionne correctement en testant une recherche sur l’URL.


## <a name="monitor"></a>Consulter les résultats des tests de disponibilité

Au bout de quelques minutes, cliquez sur **Actualiser** pour afficher les résultats de test. 

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

Le nuage de points montre des exemples de résultats de test contenant des détails de l’étape de test de diagnostic. Le moteur de test stocke les détails de diagnostic pour les tests qui présentent des erreurs. Pour les tests réussis, les détails de diagnostic sont stockés pour un sous-ensemble des exécutions. Pointez sur les points verts/rouges pour voir l’horodatage, la durée, l’emplacement et le nom du test. Cliquez sur n’importe quel point dans le nuage de points pour afficher les détails du résultat de test.  

Sélectionnez un test ou emplacement spécifique, ou réduisez la période de temps pour voir plus de résultats autour de la période d’intérêt. Utilisez l’Explorateur de recherche pour voir les résultats de toutes les exécutions, ou utilisez les requêtes Analytics pour exécuter des rapports personnalisés sur ces données.

Outre les résultats bruts, il existe deux mesures de disponibilité dans Metrics Explorer : 

1. Disponibilité : pourcentage des tests qui ont réussi, sur toutes les exécutions de test. 
2. Durée du test : durée moyenne du test sur toutes les exécutions de test.

Vous pouvez appliquer des filtres sur le nom du test et sur l’emplacement pour analyser les tendances d’un test et/ou d’un emplacement en particulier.

## <a name="edit"></a>Examiner et modifier des tests

Dans la page de résumé, sélectionnez un test spécifique. Ici, vous pouvez voir ses résultats spécifiques et le modifier ou le désactiver temporairement.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

Vous souhaiterez peut-être désactiver les tests de disponibilité ou les règles d’alerte associées lorsque vous effectuez la maintenance de votre service. 

## <a name="failures"></a>Si vous constatez des erreurs
Cliquez sur un point rouge.

![Click a red dot](./media/app-insights-monitor-web-app-availability/open-instance-3.png)


À partir d’un résultat de test de disponibilité, vous pouvez :

* Vérifier la réponse reçue à partir de votre serveur.
* Diagnostiquer la défaillance à l’aide des données de télémétrie côté serveur collectées pendant le traitement de l’instance de requête en échec.
* Enregistrer un problème ou un élément de travail dans Git ou VSTS pour suivre le problème. Le bogue contient un lien vers cet événement.
* Ouvrir le résultat du test web dans Visual Studio.

*Le résultat semble correct, mais une erreur est signalée ?* Consultez le [FAQ](#qna) pour prendre connaissance des méthodes permettant de réduire le bruit.


> [!TIP]
> Nous vous recommandons un test à partir d’au moins 2 emplacements pour obtenir une analyse fiable.
>

## <a name="multi-step-web-tests"></a>Tests web à plusieurs étapes
Vous pouvez analyser un scénario qui implique une séquence d'URL. Par exemple, si vous analysez un site Web commercial, vous pouvez vérifier que l’ajout d’articles au panier d’achat fonctionne correctement.

> [!NOTE] 
> Les tests web à plusieurs étapes ont un coût. [Mécanisme de tarification](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Pour créer un test à plusieurs étapes, vous enregistrez le scénario à l’aide de Visual Studio Enterprise et téléchargez ensuite l’enregistrement dans Application Insights. Application Insights relit le scénario à intervalles réguliers et vérifie les réponses.

> [!NOTE]
> * Vous ne pouvez pas utiliser de fonctions codées ni de boucles dans vos tests. Le test doit être entièrement contenu dans le script .webtest. Toutefois, vous pouvez utiliser des plug-ins standard.
> * Seuls les caractères anglais sont pris en charge dans les tests web à plusieurs étapes. Si vous utilisez Visual Studio dans d’autres langages, veuillez mettre à jour le fichier de définition de test web pour traduire/exclure les caractères non anglais.
>

#### <a name="1-record-a-scenario"></a>1. Enregistrement d’un scénario
Utilisez Visual Studio Enterprise pour enregistrer une session web.

1. Créez un projet de test de performance web.

    ![Dans Visual Studio édition Enterprise, créez un projet à partir du modèle Projet de test de performance Web et de charge.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Le modèle Projet de test de performance Web et de charge ne s’affiche pas ?* - Fermez Visual Studio Enterprise. Ouvrez **le programme d’installation de Visual Studio** pour modifier votre installation de Visual Studio Enterprise. Sous **Composants individuels**, sélectionnez **Web Performance and load testing tools** (Outils de test de performance Web et de charge).

2. Ouvrez le fichier .webtest et lancez l'enregistrement.

    ![Ouvrez le fichier .webtest et cliquez sur Enregistrer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Effectuez les actions utilisateur que vous voulez simuler lors de votre test : ouvrez votre site web, ajoutez un produit au panier d’achat etc. Ensuite, arrêtez le test.

    ![L’enregistreur de test web s’exécute dans Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Ne créez pas de scénario long. La limite est de 100 étapes et 2 minutes.
4. Modifiez le test pour :

   * ajouter des validations en vue de vérifier le texte reçu et les codes de réponse ;
   * supprimer les interactions superflues. Vous pouvez aussi supprimer les demandes dépendantes d’images ou celles à destination de sites AD ou de suivi.

     Ne perdez pas de vue que vous pouvez modifier uniquement le script de test. Vous ne pouvez pas ajouter de code personnalisé ni appeler d’autres tests web. N’insérez pas de boucles dans le test. Vous pouvez utiliser des plug-ins de test web standard.
5. Exécutez le test dans Visual Studio pour vérifier qu'il fonctionne.

    Le test runner web ouvre un navigateur web et répète les actions enregistrées. Assurez-vous qu’il fonctionne comme prévu.

    ![Dans Visual Studio, ouvrez le fichier .webtest et cliquez sur Exécuter.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Chargement du test web dans Application Insights
1. Dans le portail Application Insights, créez un test web.

    ![Sur le panneau des tests web, choisissez Ajouter.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Sélectionnez le test à plusieurs étapes et téléchargez le fichier .webtest.

    ![Sélectionnez test web multi-étapes.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Définissez les emplacements de test, la fréquence et les paramètres d’alerte comme pour les tests ping.

#### <a name="3-see-the-results"></a>3. Afficher les résultats

Visualisez les résultats de votre test et les échecs éventuels de la même manière que les tests d’URL unique.

En outre, vous pouvez télécharger les résultats des tests pour les afficher dans Visual Studio.

#### <a name="too-many-failures"></a>Trop d’échecs ?

* Un échec est souvent dû à un test trop long. Le test ne doit pas durer plus de deux minutes.

* N’oubliez pas que toutes les ressources d’une page doivent se charger correctement pour que le test réussisse, y compris les scripts, les feuilles de style, les images, etc.

* Le test web doit être entièrement contenu dans le script .webtest : vous ne pouvez pas utiliser de fonctions codées dans le test.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Ajout de plug-ins de temps et de nombres aléatoires à votre test à plusieurs étapes
Supposons que vous testiez un outil qui obtient des données temporelles, telles que des actions à partir d’un flux externe. Lorsque vous enregistrez votre test web, vous devez utiliser des heures spécifiques, mais vous les définissez en tant que paramètres de test, à savoir StartTime et EndTime.

![Un test web avec des paramètres.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Lorsque vous exécutez le test, vous souhaitez que le paramètre EndTime corresponde systématiquement à l’heure actuelle et le paramètre StartTime à l’heure d’il y a 15 minutes.

Les plug-ins de test web vous permettent de paramétrer les heures.

1. Ajoutez un plug-in de test web pour chaque valeur de paramètre variable souhaitée. Dans la barre d’outils de test web, sélectionnez **Ajouter un plug-in de test web**.

    ![Choisissez Ajouter un plug-in de test web et sélectionnez un type.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Dans cet exemple, nous allons utiliser deux instances du plug-in Date Time. Une instance est pour « il y a 15 minutes » et l’autre pour « maintenant ».
2. Ouvrez les propriétés de chaque plug-in. Donnez-lui un nom et configurez-le de manière à utiliser l’heure actuelle. Pour l'un d'eux, définissez Ajouter des minutes = -15.

    ![Définissez le nom, utilisez l’heure actuelle et ajouter des minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Dans les paramètres de test web, utilisez {{nom du plug-in}} pour référencer un nom de plug-in.

    ![Dans le paramètre de test, utilisez {{nom du plug-in}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Maintenant, téléchargez votre test sur le portail. Il utilise les valeurs dynamiques à chaque exécution du test.

## <a name="dealing-with-sign-in"></a>Gestion de la connexion
Si vos utilisateurs se connectent à votre application, vous disposez de différentes options pour simuler la connexion et tester les pages suivant la connexion. L’approche que vous utilisez dépend du type de sécurité fourni par l’application.

Dans tous les cas, vous devez créer un compte dans votre application uniquement à des fins de test. Si possible, limitez les autorisations de ce compte de test afin que les tests web n’affectent aucunement les utilisateurs réels.

### <a name="simple-username-and-password"></a>Nom d’utilisateur et mot de passe simples
Enregistrez un test web de la façon habituelle. Supprimez d’abord les cookies

### <a name="saml-authentication"></a>Authentication SAML
Utilisez le plug-in SAML qui est disponible pour les tests web.

### <a name="client-secret"></a>Clé secrète client
Si votre application présente un mode de connexion impliquant une clé secrète client, utilisez ce mode. Azure Active Directory (AAD) est un exemple de service fournissant une connexion avec clé secrète client. Dans AAD, la clé secrète client est la clé d’application.

Voici un exemple de test web d’une application web Azure à l’aide d’une clé d’application :

![Exemple de clé secrète client](./media/app-insights-monitor-web-app-availability/110.png)

1. Récupérez le jeton d’ADD à l’aide de la clé secrète client (clé d’application).
2. Extrayez le jeton porteur de la réponse.
3. Appelez l’API à l’aide du jeton porteur de l’en-tête d’autorisation.

Assurez-vous que le test web est un client réel, qu’il possède sa propre application dans AAD, puis utilisé son ID client et sa clé d’application. Votre service soumis à un test possède également sa propre application dans AAD : l’URI ID d’application se retrouve dans le champ dédié aux ressources du test web.

### <a name="open-authentication"></a>Authentification ouverte
Comme exemple d’authentification ouverte, citons la connexion avec votre compte Microsoft ou Google. De nombreuses applications utilisant OAuth fournissent l’alternative de la clé secrète client ; commencez donc par rechercher cet élément.

Si votre test doit se connecter à l’aide d’OAuth, l’approche générale est la suivante :

* Utilisez un outil tel que Fiddler pour examiner le trafic entre votre navigateur web, le site d’authentification et votre application.
* Effectuez deux connexions ou plus à l’aide d’ordinateurs ou de navigateurs différents, ou à des intervalles longs (pour que les jetons arrivent à expiration).
* En comparant les différentes sessions, identifiez le jeton retransmis à partir du site d’authentification, qui est ensuite transmis à votre serveur d’application après la connexion.
* Enregistrez un test web à l’aide de Visual Studio.
* Paramétrez les jetons, en définissant le paramètre lorsque le jeton est retourné par l’authentificateur et en l’utilisant dans la requête soumise sur le site.
  (Visual Studio tente de paramétrer le test, mais ne paramètre pas correctement les jetons.)


## <a name="performance-tests"></a>Tests de performance
Vous pouvez effectuer un test de charge sur votre site web. Comme pour le test de disponibilité, vous pouvez envoyer des requêtes uniques ou des requêtes à plusieurs étapes à partir de nos points de présence dans le monde. Contrairement à un test de disponibilité, de nombreuses demandes sont envoyées, afin de simuler la présence de plusieurs utilisateurs simultanés.

Dans le panneau Vue d’ensemble, ouvrez **Paramètres**, **Tests de performance**. Lorsque vous créez un test, vous êtes invité à vous connecter à Visual Studio Team Services ou à créer un compte.

Une fois le test terminé, les temps de réponse et les taux de réussite s’affichent.


![Test de performance](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> Pour observer les effets d’un test de performances, utilisez [Flux temps réel](app-insights-live-stream.md) et [Profileur](app-insights-profiler.md).
>

## <a name="automation"></a>Automatisation
* [Utilisez des scripts PowerShell pour configurer un test de disponibilité](app-insights-powershell.md#add-an-availability-test) automatiquement.
* Configurez un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) qui est appelé lorsqu’une alerte est déclenchée.

## <a name="qna"></a>Des questions ? Des problèmes ?
* *Échecs intermittents des tests avec une erreur de violation de protocole.*

    L’erreur (« violation de protocole... CR doit être suivi par LF ») indique un problème lié au serveur (ou aux dépendances). Il se produit lorsque des en-têtes mal formés sont définis dans la réponse. Ce problème peut être provoqué par des équilibreurs de charge ou des réseaux de distribution de contenu (CDN). Plus précisément, certains en-têtes peuvent ne pas utiliser CRLF pour indiquer la fin de ligne, ce qui enfreint la spécification HTTP et entraîne donc l’échec de la validation au niveau WebRequest .NET. Examinez la réponse pour repérer les en-têtes qui peuvent ne pas être conformes.
    
    Remarque : l’URL peut ne pas être en échec sur les navigateurs qui présentent une validation approximative des en-têtes HTTP. Consultez ce billet de blog pour obtenir une explication détaillée de ce problème : http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
* *Le site vous semble OK, mais vous observez des échecs des tests.*

    * Vérifiez toutes les images, les scripts, les feuilles de style et tout autre fichier chargé par la page. Si l’un d’eux échoue, le test signale une erreur, même si la page html principale se charge correctement. Pour désensibiliser le test à ces échecs de ressource, il vous suffit de décocher la case « Analyser les requêtes dépendantes » dans la configuration du test. 

    * Pour réduire la probabilité de bruit des spots réseau temporaires, etc., vérifiez que la case à cocher de configuration « Permettre les nouvelles tentatives pour les échecs des tests web » est activée. Vous pouvez également procéder aux tests à partir de plusieurs emplacements et gérer le seuil de la règle d’alerte en conséquence afin d’empêcher des problèmes propres aux emplacements provoquant des alertes injustifiées.
    
* *Je ne vois pas les données de télémétrie côté serveur associées pour diagnostiquer les échecs des tests.*
    
    Si Application Insights est défini pour votre application côté serveur, cela peut être en raison d’un [échantillonnage](app-insights-sampling.md) en cours.
* *Puis-je appeler du code à partir de mon test web ?*

    Non. Les étapes du test doivent se trouver dans le fichier .webtest. Et vous ne pouvez pas appeler d’autres tests web ou utiliser des boucles. En revanche, il existe un certain nombre de plug-ins qui peuvent s’avérer utiles.
* *Le protocole HTTPS est-il pris en charge ?*

    Nous prenons en charge TLS 1.1 et TLS 1.2.
* *Quelle est la différence entre les « tests Web » et les « tests de disponibilité » ?*

    Les deux conditions peuvent être référencées indifféremment. « Tests de disponibilité » est un terme plus générique qui inclut les tests ping d’URL uniques en plus des tests web à plusieurs étapes.
* *J’aimerais utiliser les tests de disponibilité sur notre serveur interne qui s’exécute derrière un pare-feu.*

    Il existe deux solutions possibles :
    
    * Configurez votre pare-feu pour autoriser les demandes entrantes provenant des [adresses IP de nos agents de test web](app-insights-ip-addresses.md).
    * Écrivez votre propre code pour tester régulièrement votre serveur interne. Exécutez le code en tant que processus en arrière-plan sur un serveur test derrière votre pare-feu. Le processus de test peut envoyer ses résultats à Application Insights à l’aide de l’API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) dans le package du kit de développement logiciel (SDK) principal. Cela nécessite que votre serveur de test ait un accès sortant au point de terminaison d’ingestion Application Insights, mais ce risque de sécurité est beaucoup plus faible que l’alternative, qui consiste à autoriser les demandes entrantes. Les résultats n’apparaissent pas dans les panneaux de tests web de disponibilité, mais ils s’affichent comme résultats de disponibilité dans Analytics, Search et Metric Explorer.
* *Le chargement d’un test web multi-étapes échoue*

    La limite de taille est de 300 Ko.

    Les boucles ne sont pas prises en charge.

    Les références à d’autres tests web ne sont pas prises en charge.

    Les sources de données ne sont pas prises en charge.
* *Mon test à plusieurs étapes ne se termine pas.*

    Chaque test possède une limite de 100 demandes.

    Le test s’arrête s’il s’exécute pendant plus de deux minutes.
* *Comment puis-je exécuter un test avec des certificats clients ?*

    Désolé, ce n’est pas pris en charge.


## <a name="next"></a>Étapes suivantes
[Recherche des journaux de diagnostic][diagnostic]

[Résolution des problèmes][qna]

[Adresses IP d’agents de test web](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
