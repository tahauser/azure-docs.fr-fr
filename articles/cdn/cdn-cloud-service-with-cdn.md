---
title: "Intégration d’un service cloud Azure à Azure CDN | Microsoft Docs"
description: "Découvrez comment déployer un service cloud qui traite le contenu à partir d’un point de terminaison CDN Azure intégré"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f131eb021d85766f12b0fb6cb8b5a07f965f9c97
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="intro"></a> Intégration d’un service cloud à Azure CDN
Vous pouvez intégrer un service cloud à Azure CDN pour distribuer du contenu à partir de l'emplacement du service cloud. Cette approche offre les avantages suivants :

* Facilité de déploiement et de mise à jour des images, scripts et feuilles de style dans les annuaires de projet du service cloud
* Facilité de mise à niveau des packages NuGet dans votre service cloud (par exemple, les versions de jQuery ou de Bootstrap)
* Gestion de votre application web et de votre contenu CDN, le tout depuis la même interface Visual Studio
* Flux de travail unifié pour le déploiement de votre application web et de votre contenu CDN
* Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN

## <a name="what-you-will-learn"></a>Contenu
Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* [Intégration d'un point de terminaison Azure CDN à votre service cloud et traitement du contenu statique dans vos pages Web depuis Azure CDN](#deploy)
* [Configuration des paramètres du cache pour le contenu statique de votre service cloud](#caching)
* [Distribution du contenu à partir d'actions de contrôleur via Azure CDN](#controller)
* [Traitement du contenu regroupé et minimisé via Azure CDN tout en préservant le débogage des scripts dans Visual Studio](#bundling)
* [Configuration de secours de vos scripts et feuilles de style CSS quand votre service Azure CDN est hors connexion](#fallback)

## <a name="what-you-will-build"></a>Ce que vous allez créer
Vous allez déployer un rôle Web de service cloud en utilisant le modèle ASP.NET MVC par défaut, ajouter du code pour distribuer le contenu depuis un service Azure CDN intégré (ex. image, résultats de l'action d'un contrôleur, et fichiers JavaScript et CSS par défaut) et écrire du code pour configurer le mécanisme de secours des lots traités si le service CDN est hors connexion.

## <a name="what-you-will-need"></a>Éléments requis
Ce didacticiel nécessite les éléments suivants :

* Un [compte Microsoft Azure](/account/)
* Visual Studio 2015 avec le [Kit SDK Azure](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :
> 
> * Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu'ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web.
> * Vous pouvez [activer les avantages de l'abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Déploiement d'un service cloud
Dans cette section, vous allez déployer le modèle d'application ASP.NET MVC par défaut dans Visual Studio 2015 dans un rôle web de service cloud, puis l'intégrer à un nouveau point de terminaison CDN. Procédez comme suit :

1. Dans Visual Studio 2015, créez un service cloud Azure à partir de la barre de menus : **Fichier > Nouveau > Projet > Cloud > Azure Cloud Service**. Attribuez-lui un nom et cliquez sur **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Sélectionnez **Rôle Web ASP.NET** et cliquez sur le bouton **>**. Cliquez sur OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Sélectionnez **MVC** et cliquez sur **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Publiez ce rôle web dans un service cloud Azure. Cliquez avec le bouton droit sur le projet de service cloud et sélectionnez **Publier**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Si vous n’êtes pas encore connecté à Microsoft Azure, cliquez sur la liste déroulante **Ajouter un compte** et cliquez sur l’élément de menu **Ajouter un compte**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Dans la page de connexion, connectez-vous au compte Microsoft que vous avez utilisé pour activer votre compte Azure.
7. Une fois que vous êtes connecté, cliquez sur **Suivant**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. En supposant que vous n'avez pas créé de compte de service cloud ou de stockage, Visual Studio vous aide à créer les deux. Dans la boîte de dialogue **Créer un service cloud et un compte** , tapez le nom souhaité pour le service et sélectionnez la région souhaitée. Cliquez sur **Créer**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Dans la page des paramètres de publication, vérifiez la configuration et cliquez sur **Publier**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > La publication des services cloud prend beaucoup de temps. L'option Activer Web Deploy pour tous les rôles accélère notablement le débogage de votre service cloud en fournissant des mises à jour rapides (mais provisoires) à vos rôles web. Pour en savoir plus sur cette option, consultez la page [Publication d'un service cloud en utilisant les Outils Azure](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Quand le **Journal des activités Microsoft Azure** indique que la publication est **Terminée**, vous devez créer un point de terminaison CDN intégré à ce service cloud.
   
   > [!WARNING]
   > Si, après la publication, le service cloud déployé affiche un écran d'erreur, il est probable que le service cloud que vous avez déployé utilise un [système d'exploitation invité qui n'inclut pas .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Vous pouvez contourner ce problème en [déployant .NET 4.5.2 en tant que tâche de démarrage](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Créer un profil CDN
Un profil CDN est une collection de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous pouvez utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine Internet, application web ou d'autres critères.

> [!TIP]
> Si vous disposez déjà d'un profil CDN que vous souhaitez utiliser pour ce didacticiel, passez à la section [Créer un point de terminaison CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un point de terminaison CDN
**Pour créer un point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous l'avez peut-être épinglé au tableau de bord à l'étape précédente.  Dans le cas contraire, vous le trouverez en cliquant sur **Parcourir**, puis **Profils CDN** et en cliquant sur le profil auquel vous voulez ajouter le point de terminaison.
   
    Le panneau du profil CDN s'affiche.
   
    ![Profil CDN][cdn-profile-settings]
2. Cliquez sur le bouton **Ajouter un point de terminaison** .
   
    ![Bouton Ajouter un point de terminaison][cdn-new-endpoint-button]
   
    Le panneau **Ajouter un point de terminaison** s’affiche.
   
    ![Panneau Ajouter un point de terminaison][cdn-add-endpoint]
3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources en cache au niveau du domaine `<EndpointName>.azureedge.net`.
4. Dans la liste déroulante **Type d'origine** , sélectionnez *Service cloud*.  
5. Dans la liste déroulante **Nom d'hôte d'origine** , sélectionnez votre service cloud.
6. Conservez les valeurs par défaut pour le **chemin d’accès d’origine**, l’**en-tête de l’hôte d’origine** et le **port de protocole/origine**.  Vous devez spécifier au moins un protocole (HTTP ou HTTPS).
7. Cliquez sur le bouton **Ajouter** pour créer le point de terminaison.
8. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour le profil. L’affichage sous forme de liste montre l’URL à utiliser pour accéder au contenu mis en cache et le domaine d’origine.
   
    ![Point de terminaison CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > Le point de terminaison ne sera pas immédiatement disponible pour utilisation.  La propagation de l'inscription à travers le réseau CDN peut prendre jusqu'à 90 minutes. Les utilisateurs qui tentent d'utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d'état 404 jusqu'à ce que le contenu soit disponible via le CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Test du point de terminaison CDN
Quand l’état de la publication est **Terminé**, ouvrez une fenêtre de navigateur et accédez à l’adresse **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Dans ma configuration, cette URL est la suivante :

    http://camservice.azureedge.net/Content/bootstrap.css

Elle correspond à l'URL d'origine suivante au niveau du point de terminaison CDN :

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Quand vous atteignez la page **http://*&lt;nom_CDN>*.azureedge.net/Content/bootstrap.css**, en fonction de votre navigateur, vous êtes invité à télécharger ou à ouvrir le fichier bootstrap.css provenant de votre application web publiée.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

De même, vous pouvez accéder à toute URL accessible publiquement à l’adresse **http://*&lt;nom_service>*.cloudapp.net/**, directement à partir de votre point de terminaison CDN. Par exemple : 

* un fichier .js à partir du chemin d'accès /Script ;
* un fichier de contenu à partir du chemin d'accès /Content ;
* un contrôleur/une action ;
* si la chaîne de requête est activée sur le point de terminaison CDN, une URL avec des chaînes de requête.

En effet, avec la configuration précédente, vous pouvez héberger l’ensemble du service cloud depuis **http://*&lt;cdnName>*.azureedge.net/**. Si vous accédez à l’adresse **http://camservice.azureedge.net/** , vous obtenez le résultat de l’action à partir de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Cependant, cela ne signifie pas que ce soit toujours une bonne idée de traiter l’ensemble du service cloud par l’intermédiaire d’Azure CDN. 

Un CDN avec optimisation de la remise statique n’accélère pas forcément la distribution des ressources dynamiques qui ne sont pas censées être mises en cache, ou qui sont mises à jour très fréquemment, étant donné que le CDN doit extraire très souvent une nouvelle version de la ressource à partir du serveur d’origine. Pour ce scénario, vous pouvez activer l’optimisation [Accélération de site dynamique](cdn-dynamic-site-acceleration.md) sur votre point de terminaison CDN, qui utilise diverses techniques pour accélérer la distribution des ressources dynamiques ne pouvant pas être mises en cache. 

Si vous avez un site avec un mélange de contenu statique et dynamique, vous pouvez choisir de traiter votre contenu statique à partir de CDN avec un type d’optimisation statique (par exemple la livraison web générale) et de traiter le contenu dynamique directement à partir du serveur d’origine ou par le biais d’un point de terminaison CDN avec l’optimisation Accélération de site dynamique activée au cas par cas. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu individuels depuis le point de terminaison CDN. Je vais vous montrer comment traiter une action sur un contrôleur donné par le biais d’un point de terminaison CDN spécifique dans la section Distribution de contenu à partir d’actions de contrôleur via Azure CDN.

La solution alternative consiste à déterminer au cas par cas le contenu à traiter à partir d'Azure CDN dans votre service cloud. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu individuels depuis le point de terminaison CDN. Je vais vous montrer comment traiter une action sur un contrôleur donné via le point de terminaison CDN dans la section [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configuration des options de mise en cache des fichiers statiques dans votre service cloud
Avec l'intégration d'Azure CDN à votre service cloud, vous pouvez spécifier comment vous voulez que le contenu statique soit mis en cache dans le point de terminaison CDN. Pour cela, ouvrez *Web.config* à partir de votre projet de rôle web (par exemple, WebRole1) et ajoutez un élément `<staticContent>` à `<system.webServer>`. Le code XML ci-dessous configure l'expiration du cache dans 3 jours.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Lorsque vous faites cela, tous les fichiers statiques de votre service cloud respectent la même règle dans le cache de votre CDN. Pour un contrôle plus granulaire des paramètres du cache, ajoutez un fichier *Web.config* dans un dossier et ajoutez-lui vos paramètres. Exemple : ajoutez un fichier *Web.config* au dossier *\Content* et remplacez son contenu par le code XML suivant :

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Ce paramètre entraîne la mise en cache de tous les fichiers statiques du dossier *\Content* pendant 15 jours.

Pour en savoir plus sur la configuration de l’élément `<clientCache>`, consultez [Cache du client &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Dans la rubrique [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller), je vais également vous montrer comment configurer les paramètres du cache pour les résultats de l'action du contrôleur dans le cache du CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Distribution du contenu à partir d'actions de contrôleur via Azure CDN
Lorsque vous intégrez un rôle web de service cloud à Azure CDN, il est relativement facile de distribuer du contenu à partir d'actions de contrôleur via le réseau de distribution de contenu (CDN) Azure. En dehors du traitement de votre service cloud directement par le biais d’Azure CDN (démontré ci-dessus), [Maarten Balliauw](https://twitter.com/maartenballiauw) vous démontre comment le faire avec le contrôleur MemeGenerator à la page [Reducing latency on the web with the Azure CDN (Réduction de la latence sur Internet avec le réseau de distribution de contenu (CDN) Azure)](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Je reproduis simplement cette page ici.

Supposez que, dans votre service cloud, vous vouliez créer des idées culturelles basées sur une image de Chuck Norris jeune (photo de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) ainsi :

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Une simple action `Index` permet aux clients de spécifier les superlatifs dans l’image, puis de créer l’idée lorsqu’ils publient vers l’action. Comme il s'agit de Chuck Norris, vous espérez que cette page devienne extrêmement populaire dans le monde entier. Cet exemple illustre bien la distribution de contenu semi-dynamique avec Azure CDN.

Procédez comme ci-dessus pour configurer cette action de contrôleur :

1. Dans le dossier *\Controllers*, créez un fichier .cs nommé *MemeGeneratorController.cs* et remplacez son contenu par le code suivant. N'oubliez pas de remplacer la partie en surbrillance par le nom de votre CDN.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Cliquez avec le bouton droit sur l’action `Index()` par défaut et sélectionnez **Ajouter une vue**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Acceptez les paramètres ci-dessous et cliquez sur **Ajouter**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Ouvrez le nouveau fichier *Views\MemeGenerator\Index.cshtml* et remplacez son contenu par le simple code HTML suivant pour envoyer les superlatifs :
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Republiez le service cloud et accédez à l’adresse **http://*&lt;nom_service>*.cloudapp.net/MemeGenerator/Index** dans votre navigateur.

Quand vous envoyez les valeurs du formulaire dans `/MemeGenerator/Index`, la méthode d’action `Index_Post` renvoie un lien vers la méthode d’action `Show` avec l’identificateur d’entrée respectif. Quand vous cliquez sur ce lien, vous accédez au code suivant :  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Si votre débogueur local est lié, vous effectuez un débogage normal avec une redirection locale. S'il est exécuté dans le service cloud, il redirige vers :

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Qui correspond à l'URL d'origine suivante au niveau de votre point de terminaison CDN :

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Vous pouvez alors appliquer l’attribut `OutputCacheAttribute` à la méthode `Generate` pour spécifier la manière dont l’action doit être mise en cache, et qu’Azure CDN respectera. Le code ci-dessous spécifie 1 heure (3 600 secondes) pour l'expiration du cache.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

De même, vous pouvez distribuer le contenu de l'action d'un contrôleur dans votre service cloud via le réseau de distribution de contenu (CDN) Azure avec l'option de mise en cache voulue.

Dans la section suivante, je vais vous montrer comment distribuer les scripts et les feuilles de style CSS regroupés et minimisés via Azure CDN.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN
Les scripts et les feuilles de style CSS, qui ne changent pas fréquemment, sont les principaux candidats pour le cache Azure CDN. La distribution de l'ensemble du rôle web via votre réseau de distribution de contenu (CDN) Azure est la manière la plus facile d'intégrer le regroupement et la minimisation à Azure CDN. Cependant, comme vous ne voudrez peut-être pas faire cela, je vais vous montrer comment le faire en conservant l'approche souhaitée par le développeur concernant le regroupement et la minimisation ASP.NET, par exemple :

* Mode débogage puissant
* Facilité de déploiement
* Mises à jour immédiates à destination des clients lors des mises à niveau des versions des scripts et des feuilles de style CSS
* Mécanisme de secours en cas de défaillance de votre point de terminaison CDN
* Modifications minimales du code

Dans le projet **WebRole1** créé à l’étape [Intégration d’un point de terminaison Azure CDN à votre service cloud et traitement du contenu statique dans vos pages Web depuis Azure CDN](#deploy), ouvrez *App_Start\BundleConfig.cs* et observez les appels de la méthode `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La première instruction `bundles.Add()` ajoute un regroupement de script dans le répertoire virtuel `~/bundles/jquery`. Ouvrez ensuite le fichier *Views\Shared\_Layout.cshtml* pour voir comment la balise de regroupement de script est rendue. Vous devez trouver la ligne de code Razor suivante :

    @Scripts.Render("~/bundles/jquery")

Quand ce code Razor est exécuté dans le rôle web Azure, il restitue une balise `<script>` pour le regroupement du script similaire à ce qui suit :

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Cependant, quand il est exécuté dans Visual Studio en appuyant sur `F5`, il restitue individuellement chaque fichier de script dans le regroupement (dans le cas ci-dessus, un seul fichier de script se trouve dans le regroupement) :

    <script src="/Scripts/jquery-1.10.2.js"></script>

Cela permet de déboguer le code JavaScript dans votre environnement de développement tout en réduisant les connexions clientes simultanées (regroupement) et en améliorant les performances de téléchargement (minimisation) en production. Cette fonctionnalité est très intéressante à conserver avec l'intégration Azure CDN. De plus, du fait que le regroupement restitué contient déjà une chaîne de version générée automatiquement, vous voudrez répliquer cette fonctionnalité de façon que, chaque fois que vous mettrez à jour votre version jQuery via NuGet, il sera possible de la mettre à jour côté client le plus rapidement possible.

Procédez comme suit pour l'intégration du regroupement et de la minimisation ASP.NET à votre point de terminaison CDN.

1. De retour dans *App_Start\BundleConfig.cs*, modifiez les méthodes `bundles.Add()` pour utiliser un autre [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx) qui spécifie une adresse CDN. Pour cela, remplacez la définition de la méthode `RegisterBundles` par le code suivant :  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    N'oubliez pas de remplacer `<yourCDNName>` par le nom de votre réseau de distribution de contenu (CDN) Azure.
   
    Exprimé simplement, vous configurez `bundles.UseCdn = true` et ajoutez une URL CDN correctement formatée à chaque regroupement. Par exemple : le premier constructeur dans le code :
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    est identique à :
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Ce constructeur indique au regroupement et à la minimisation ASP.NET de restituer des fichiers de script individuels lorsqu'ils sont débogués localement, mais d'utiliser l'adresse CDN spécifiée pour accéder au script en question. Cependant, notez deux caractéristiques importantes avec cette URL CDN correctement formatée :
   
   * L'origine de cette URL CDN est `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, qui est en vérité le répertoire virtuel du regroupement de script dans votre service cloud.
   * Comme vous utilisez un constructeur CDN, la balise du script CDN pour le regroupement ne contient plus la chaîne de caractères de la version automatiquement générée dans l'URL restituée. Vous devez créer manuellement une chaîne de version unique à chaque modification du regroupement de script pour forcer une erreur dans le cache de votre réseau de distribution de contenu (CDN) Azure. En même temps, cette chaîne de version unique doit rester constante tout au long du déploiement pour un accès maximal au cache de votre réseau de distribution de contenu (CDN) Azure après le déploiement du regroupement.
   * La chaîne de requête v=<W.X.Y.Z> est extraite de *Properties\AssemblyInfo.cs* dans votre projet de rôle web. Vous pouvez avoir un flux de travail de déploiement qui inclut l'incrémentation de la version de l'assembly chaque fois que vous publiez dans Azure. Vous pouvez également modifier *Properties\AssemblyInfo.cs* dans votre projet pour incrémenter automatiquement la chaîne de caractères de la version à chaque génération en utilisant le caractère générique ’*’. Par exemple : 
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     Toute autre stratégie pour simplifier la génération d'une chaîne unique tout au long d'un déploiement fonctionne dans ce cas.
2. Republiez le service cloud et accédez à la page d'accueil.
3. Affichez le code HTML de la page. Vous devez voir l'URL CDN restituée avec une chaîne de version unique chaque fois que vous republiez des modifications dans votre service cloud. Par exemple :   
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. Dans Visual Studio, tapez `F5`pour déboguer le service cloud.
5. Affichez le code HTML de la page. Vous voyez chaque fichier de script restitué individuellement de façon à effectuer un débogage cohérent dans Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Mécanisme de secours pour les URL CDN
Lorsque le point de terminaison de votre réseau de distribution de contenu (CDN) présente une défaillance pour quelque raison que ce soit, vous voulez que votre page web soit suffisamment bien conçue pour accéder à votre serveur web d'origine comme option de secours pour le chargement de JavaScript ou Bootstrap. C'est une chose de perdre des images sur votre site web à cause d'une indisponibilité CDN, c'en est une autre de perdre dans une page une fonctionnalité vitale fournie par vos scripts et vos feuilles de style.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contient la propriété [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) qui vous permet de configurer le mécanisme de secours en cas de défaillance CDN. Pour utiliser cette propriété, procédez comme suit :

1. Dans votre projet de rôle web, ouvrez *App_Start\BundleConfig.cs*, où vous avez ajouté une URL CDN dans chaque [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx), puis apportez les modifications en surbrillance pour ajouter un mécanisme de secours aux regroupements par défaut :  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Quand `CdnFallbackExpression` n’a pas la valeur null, le script est injecté dans le code HTML pour tester où le regroupement est correctement chargé et, dans le cas contraire, pour accéder au regroupement directement depuis le serveur web d’origine. Cette propriété doit être configurée avec une expression JavaScript qui teste si le regroupement CDN correspondant est correctement chargé. L'expression nécessaire pour tester chaque regroupement est différente en fonction du contenu. Pour les regroupements par défaut ci-dessus :
   
   * `window.jquery` est défini dans jquery-{version}.js
   * `$.validator` est défini dans jquery.validate.js
   * `window.Modernizr` est défini dans modernizer-{version}.js
   * `$.fn.modal` est défini dans bootstrap.js
     
     Vous aurez peut-être remarqué que je n’ai pas configuré CdnFallbackExpression pour le regroupement `~/Cointent/css` . Cela est dû au fait qu’un [bogue dans System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) injecte une balise `<script>` pour le fichier CSS de secours à la place de la balise attendue `<link>`.
     
     Il existe néanmoins un bon [mécanisme de secours pour les styles](https://github.com/EmberConsultingGroup/StyleBundleFallback) proposé par [Ember Consulting Group](https://github.com/EmberConsultingGroup).
2. Pour utiliser cette solution pour le CSS, créez un fichier .cs dans le répertoire *App_Start*, en le nommant *StyleBundleExtensions.cs*, puis remplacez son contenu par du [code issu de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. Dans *App_Start\StyleFundleExtensions.cs*, renommez l’espace de noms avec le nom de votre rôle web (par exemple, **WebRole1**).
4. Revenez à `App_Start\BundleConfig.cs` et remplacez la dernière instruction `bundles.Add` par le code suivant en surbrillance :  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Cette nouvelle méthode d'extension utilise la même idée pour injecter un script dans le code HTML afin de vérifier le DOM pour le nom de classe, le nom de règle et la valeur de règle correspondants dans le regroupement CSS. Elle rétablit le serveur web d'origine si elle ne trouve pas de correspondance.
5. Republiez le service cloud et accédez à la page d'accueil.
6. Affichez le code HTML de la page. Vous devez trouver des scripts injectés similaires à ce qui suit :    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Notez que le script injecté pour le regroupement CSS contient toujours les éléments restants provenant de la propriété `CdnFallbackExpression` dans la ligne :

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mais, du fait que la première partie de l'expression || renvoie toujours la valeur true (dans la ligne juste au-dessus), la fonction document.write() ne s'exécute jamais.

## <a name="more-information"></a>Informations complémentaires
* [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Utilisation d’Azure CDN](cdn-create-new-endpoint.md)
* [Regroupement et minimisation d’ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
