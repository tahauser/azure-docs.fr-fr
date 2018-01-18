---
title: "Déploiement Git local vers Azure App Service"
description: "Découvrez comment activer le déploiement Git local vers Azure App Service"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 19681564ef48649d4d44da1d0a2a65044f494ce4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Déploiement Git local vers Azure App Service
Ce didacticiel vous montre comment déployer votre application vers [Azure Web Apps](app-service-web-overview.md) depuis un référentiel Git sur votre ordinateur local. App Service prend en charge cette approche avec l'option de déploiement **Git local** dans le [portail Azure].  
La plupart des commandes Git décrites dans cet article sont exécutées automatiquement pendant la création d’une application App Service avec l’[interface de ligne de commande Azure] comme décrit [ici](app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Conditions préalables
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Git. Vous pouvez télécharger le fichier binaire d’installation [ici](http://www.git-scm.com/downloads).  
* Connaissances élémentaires de Git.
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.  
> 
> 

## <a name="Step1"></a>Étape 1 : création d’un référentiel local
Effectuez les tâches suivantes pour créer un nouveau référentiel Git.

1. Lancez un outil de ligne de commande, comme **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal** .
2. Accédez au répertoire où figure le contenu à déployer.
3. Utilisez la commande suivante pour initialiser un nouveau référentiel Git :

```bash  
git init
```

## <a name="Step2"></a>Étape 2 : validation de votre contenu
App Service prend en charge des applications créées dans différents langages de programmation. 

1. Si votre référentiel inclut déjà du contenu, ignorez ce point et passez au point 2 ci-dessous. Si votre référentiel n'inclut pas encore de contenu, remplissez-le simplement avec un fichier .html statique comme suit : 
   
   * À l'aide d'un éditeur de texte, créez un fichier nommé **index.html** à la racine du référentiel Git
   * Ajoutez le texte suivant dans le fichier index.html et enregistrez ce dernier : *Hello Git!*
2. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git. Puis utilisez la commande suivante pour ajouter des fichiers à votre référentiel :

```bash  
git add -A
```
3. Ensuite, validez les modifications apportées au référentiel au moyen de la commande suivante :

```bash  
git commit -m "Hello Azure App Service"
```  

## <a name="Step3"></a>Étape 3 : activation du référentiel de l’application App Service
Pour activer un référentiel Git pour votre application App Service, procédez comme suit.

1. Connectez-vous au [portail Azure].
2. Dans le panneau de votre application App Service, cliquez sur **Paramètres > Source du déploiement**. Cliquez successivement sur **Choisir une source**, **Référentiel Git local** et **OK**.  
   
    ![Référentiel Git local](./media/app-service-deploy-local-git/local_git_selection.png)
3. Si vous configurez un référentiel pour la première fois dans Azure, vous devez créer des informations d’identification de connexion pour ce référentiel. Vous les utiliserez pour vous connecter au référentiel Azure et pour envoyer les modifications depuis votre référentiel Git local. Dans le panneau de votre application, cliquez sur **Déploiement > Informations d’identification du déploiement**, puis configurez le nom d’utilisateur et le mot de passe de votre déploiement. Quand vous avez terminé, cliquez sur **Enregistrer**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Étape 4 : déploiement de votre projet
Pour publier votre application vers App Service à l’aide de Git local, procédez comme suit :

1. Dans le portail Azure, dans le panneau de votre application, cliquez sur **Paramètres > Propriétés** pour l’**URL Git**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **URL Git** est la référence hors programme vers laquelle vous effectuez le déploiement à partir de votre référentiel local. Vous utiliserez cette URL dans la procédure suivante.
2. À l'aide de la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git local.
3. Utilisez `git remote` pour ajouter la référence hors programme répertoriée dans **URL Git** à l’étape 1. Votre commande ressemble à ce qui suit :
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > La commande **remote** ajoute une référence nommée dans un référentiel distant. Dans cet exemple, une référence nommée « azure » est créée pour le référentiel de votre application web.
   > 
   > 
4. Diffusez votre contenu vers App Service à l'aide de la nouvelle référence **azure** distante que vous venez de créer.

```bash  
git push azure master
```
    You will be prompted for the password you created earlier when you reset your deployment credentials in the Azure Portal. Enter the password (note that Gitbash does not echo asterisks to the console as you type your password). 
5. Revenez à votre application dans le portail Azure. Une entrée de journal de votre dernière diffusion push doit apparaître dans le panneau **Déploiements** . 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Cliquez sur le bouton **Parcourir** en haut du panneau de l'application pour vérifier que le contenu a été déployé. 

## <a name="Step5"></a>Résolution des problèmes
Voici les erreurs ou les problèmes rencontrés couramment lors de l’utilisation de Git pour publier vers une application App Service dans Azure :

- - -
**Symptôme**: Impossible d'accéder à '[URL_du_site]' : Impossible de se connecter à [Adresse_scm]

**Cause**: Cette erreur peut se produire si l'application n'est pas opérationnelle.

**Résolution**: démarrez l’application dans le portail Azure. Le déploiement Git ne fonctionne pas tant que l’application n’est pas en cours d’exécution. 

- - -
**Symptôme**: Impossible de résoudre l'hôte « nom_hôte »

**Cause**: Cette erreur peut se produire si les informations d'adresse entrées au moment de la création du référentiel distant « azure » sont incorrectes.

**Résolution** : utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l’URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.

- - -
**Symptôme**: aucune référence en commun, ni spécifiée ; aucune action effectuée. Perhaps you should specify a branch such as 'master'.

**Cause**: Cette erreur peut se produire si vous ne spécifiez pas de branche au moment de l'exécution d'une opération git push et que vous n'avez pas défini la valeur push.default utilisée par Git.

**Résolution**: Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple : 

```bash  
git push azure master
```
- - -
**Symptôme**: src refspec [nom_branche] ne correspond à aucun élément.

**Cause**: Cette erreur peut se produire si vous tentez d'effectuer une opération Push sur une autre branche que la branche principale sur le référentiel distant « azure ».

**Résolution**: Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple : 

```bash  
git push azure master
```
- - -
**Symptôme** : échec RPC ; résultat=22, Code HTTP = 502.

**Cause** : cette erreur peut se produire si vous essayez de transmettre un dépôt Git volumineux via HTTPS.

**Résolution** : modifiez la configuration git sur l’ordinateur local pour agrandir le postBuffer.

```bash  
git config --global http.postBuffer 524288000
```
- - -
**Symptôme**: erreur : des modifications ont été validées dans le référentiel distant, mais votre application web n’a pas été mise à jour.

**Cause**: Cette erreur peut se produire si vous déployez une application Node.js contenant un fichier package.json spécifiant des modules obligatoires supplémentaires.

**Résolution**: Des messages supplémentaires contenant « npm ERR! » doivent être consignés avant cette erreur et peuvent fournir davantage de contexte sur la défaillance. Voici les causes connues de cette erreur et le message « npm ERR! » correspondant :

* **Fichier package.json incorrect**: npm ERR! Couldn’t read dependencies.
* **Native module that does not have a binary distribution for Windows**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
      Ou
  * npm ERR! [modulename@version] preinstall: \`make || gmake\`

## <a name="additional-resources"></a>Ressources supplémentaires
* [Documentation Git](http://git-scm.com/documentation)
* [Documentation du projet Kudu](https://github.com/projectkudu/kudu/wiki)
* [Déploiement continu vers Azure App Service](app-service-continuous-deployment.md)
* [Comment utiliser PowerShell pour Azure](/powershell/azure/overview)
* [Utilisation des outils en ligne de commande Azure](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[portail Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[interface de ligne de commande Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
