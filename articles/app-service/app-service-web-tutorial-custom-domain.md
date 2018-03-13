---
title: "Mapper un nom DNS personnalisé existant à des applications web Azure | Microsoft Docs"
description: "Découvrez comment ajouter un nom de domaine DNS (domaine personnel) à une application web, au serveur principal d’une application mobile ou à une application API dans Azure App Service."
keywords: "app service, azure app service, mappage de domaine, nom de domaine, domaine existant, nom d'hôte"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9867cc2f8a8d484ca4bfb160c20a07df38790f4d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapper un nom DNS personnalisé existant à des applications web Azure

[Azure Web Apps](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel vous montre comment mapper un nom DNS personnalisé existant à des applications web Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mapper un sous-domaine (par exemple, `www.contoso.com`) à l’aide d’un enregistrement CNAME
> * Mapper un domaine racine (par exemple, `contoso.com`) à l’aide d’un enregistrement A
> * Mapper un domaine générique (par exemple, `*.contoso.com`) à l’aide d’un enregistrement CNAME
> * Automatiser le mappage de domaine à l’aide de scripts

Vous pouvez utiliser un **enregistrement CNAME** ou un **enregistrement A** pour mapper un nom DNS personnalisé à App Service. 

> [!NOTE]
> Nous vous recommandons d’utiliser un enregistrement CNAME pour tous les noms DNS personnalisés, à l’exception d’un domaine racine (par exemple, `contoso.com`).

Pour migrer un site actif et son nom de domaine DNS vers App Service, voir [Migrer un nom DNS actif vers Azure App Service](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>configuration requise

Pour suivre ce didacticiel :

* [Créez une application App Service](/azure/app-service/), ou utilisez une application créée pour un autre didacticiel.
* Achetez un nom de domaine et assurez-vous que vous avez accès au registre DNS pour votre fournisseur de domaines (par exemple, GoDaddy).

  Par exemple, pour ajouter des entrées DNS pour `contoso.com` et `www.contoso.com`, vous devez pouvoir configurer les paramètres DNS du domaine racine `contoso.com`.

  > [!NOTE]
  > Si vous ne disposez d’aucun nom de domaine existant, envisagez d’[acheter un domaine à l’aide du portail Azure](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Préparer l’application

Pour mapper un nom DNS personnalisé à une application web, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) de l’application web doit être un niveau payant (**Partagé**, **De base**, **Standard** ou **Premium**). Au cours de cette étape, vous allez vous assurer que l’application App Service se trouve dans le niveau de tarification pris en charge.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Accéder à l’application dans le portail Azure

Dans le menu de gauche, sélectionnez **App Services**, puis le nom de l’application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

La page de gestion de l’application App Service s’affiche.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation gauche de la page de l’application, faites défiler jusqu’à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)**.

![Menu Monter en puissance](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Le niveau actuel de l’application est encadré d’un rectangle bleu. Vérifiez que l’application ne se trouve pas dans le niveau **Gratuit**. Les DNS personnalisés ne sont pas pris en charge dans le niveau **Gratuit**. 

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Si le plan App Service n’est pas **Gratuit** , fermez la page **Choisir votre niveau de tarification** et passez à [Mapper un enregistrement CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Monter en puissance le plan App Service

Sélectionnez l’un des niveaux payants (**Partagé**, **De base**, **Standard** ou **Premium**). 

Cliquez sur **Sélectionner**.

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Lorsque la notification suivante s’affiche, cela signifie que l’opération est terminée.

![Confirmation d’opération de mise à l’échelle](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mapper un enregistrement CNAME

Dans l’exemple de ce didacticiel, vous ajoutez un enregistrement CNAME pour le sous-domaine `www` (`www.contoso.com`, par exemple).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME pour mapper un sous-domaine au nom d’hôte par défaut de l’application (`<app_name>.azurewebsites.net`, où `<app_name>` est le nom de votre application).

Pour l’exemple de domaine `www.contoso.com`, ajoutez un enregistrement CNAME qui mappe le nom `www` à `<app_name>.azurewebsites.net`.

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Activer le mappage d’enregistrement CNAME dans Azure

Dans le volet de navigation gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Dans la page **Domaines personnalisés** de l’application, ajoutez le nom DNS personnalisé complet (`www.contoso.com`) à la liste.

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Tapez le nom de domaine complet que vous avez ajouté pour un enregistrement CNAME, tel que `www.contoso.com`. 

Sélectionnez **Valider**.

Le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (exemple.com ou tout sous-domaine)**.

Sélectionnez **Ajouter un nom d’hôte**.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Un certain temps peut être nécessaire pour que le nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Enregistrement CNAME ajouté](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Erreur de vérification](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapper un enregistrement A

Dans l’exemple de ce didacticiel, vous ajoutez un enregistrement A pour le domaine racine (par exemple, `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Copier l’adresse IP de l’application

Pour mapper un enregistrement A, vous avez besoin de l’adresse IP externe de l’application. Vous pouvez trouver cette adresse IP sur la page **Domaines personnalisés** de l’application dans le portail Azure.

Dans le volet de navigation gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Dans la page **Domaines personnalisés**, copiez l’adresse IP de l’application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Créer l’enregistrement A

Pour mapper un enregistrement A à une application, App Service a besoin de **deux** enregistrements DNS :

- Un enregistrement **A** pour effectuer un mappage vers l’adresse IP de l’application.
- Un enregistrement **TXT** pour effectuer un mappage vers le nom d’hôte par défaut de l’application `<app_name>.azurewebsites.net`. App Service utilise cet enregistrement uniquement au moment de la configuration, pour vérifier que vous possédez le domaine personnalisé. Une fois votre domaine personnalisé validé et configuré dans App Service, vous pourrez supprimer cet enregistrement TXT. 

Pour l’exemple de domaine `contoso.com`, créez les enregistrements A et TXT d’après le tableau suivant (`@` représente généralement le domaine racine). 

| Type d’enregistrement | Host | Valeur |
| - | - | - |
| A | `@` | Adresse IP de [Copier l’adresse IP de l’application](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Page Enregistrements DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Activer le mappage d’enregistrement A dans l’application

De retour sur la page **Domaines personnalisés** de l’application dans le portail Azure, ajoutez à la liste le nom DNS personnalisé complet (par exemple, `contoso.com`).

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement A, tel que `contoso.com`.

Sélectionnez **Valider**.

Le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)**.

Sélectionnez **Ajouter un nom d’hôte**.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Un certain temps peut être nécessaire pour que le nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Enregistrement A ajouté](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Erreur de vérification](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapper un domaine générique

Dans l’exemple du didacticiel, vous mappez un [nom DNS générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (par exemple, `*.contoso.com`) vers l’application App Service en ajoutant un enregistrement CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME pour mapper un nom générique au nom d’hôte par défaut de l’application (`<app_name>.azurewebsites.net`).

Pour l’exemple de domaine `*.contoso.com`, l’enregistrement CNAME mappera le nom `*` à `<app_name>.azurewebsites.net`.

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Activer le mappage d’enregistrement CNAME dans l’application

Vous pouvez maintenant ajouter à l’application un sous-domaine qui correspond au nom générique (par exemple, `sub1.contoso.com` et `sub2.contoso.com` correspond à `*.contoso.com`). 

Dans le volet de navigation gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Tapez un nom de domaine complet qui correspond au domaine générique (par exemple, `sub1.contoso.com`), puis sélectionnez **Valider**.

Le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que le **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (exemple.com ou tout sous-domaine)**.

Sélectionnez **Ajouter un nom d’hôte**.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Un certain temps peut être nécessaire pour que le nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

Cliquez de nouveau sur l’icône **+** pour ajouter un autre nom d’hôte qui correspond au domaine générique. Par exemple, ajoutez `sub2.contoso.com`.

![Enregistrement CNAME ajouté](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Test dans le navigateur

Dans votre navigateur, accédez aux noms DNS que vous avez configurés précédemment (par exemple, `contoso.com`, `www.contoso.com`,`sub1.contoso.com` et `sub2.contoso.com`).

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Résolution de l'erreur 404 « Site Web introuvable »

Si vous recevez une erreur HTTP 404 (Introuvable) lors de la navigation vers l'URL de votre domaine personnalisé, vérifiez que votre domaine résout l'adresse IP de votre application en utilisant <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Dans le cas contraire, cela peut être dû à l’une des raisons suivantes :

- Un enregistrement A et/ou un enregistrement CNAME est manquant dans le domaine personnalisé configuré.
- Le client du navigateur a mis en cache l'ancienne adresse IP de votre domaine. Effacez le cache et testez à nouveau la résolution DNS. Sur une machine Windows, effacez le cache avec `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Diriger l'URL par défaut vers un répertoire personnalisé

Par défaut, App Service dirige les demandes web au répertoire racine du code de votre application. Cependant, certaines infrastructures web ne démarrent pas dans le répertoire racine. Par exemple, [Laravel](https://laravel.com/) démarre dans le sous-répertoire `public`. Pour continuer l'exemple DNS `contoso.com`, une telle application serait accessible à partir de `http://contoso.com/public`, mais il est préférable de diriger `http://contoso.com` vers le répertoire `public`. Cette étape n'implique pas la résolution DNS, mais la personnalisation du répertoire virtuel.

Pour cela, sélectionnez **Paramètres de l'application** dans la barre de navigation située à gauche de la page de votre application Web. 

En bas de la page, le répertoire virtuel racine `/` pointe par défaut vers `site\wwwroot`, qui correspond au répertoire racine de votre code d'application. Modifiez-le afin qu'il pointe vers `site\wwwroot\public`, par exemple, puis enregistrez vos modifications. 

![Personnalisation du répertoire virtuel](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Une fois l’opération terminée, votre application devrait renvoyer la bonne page sur le chemin racine (par exemple, http://contoso.com).

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des domaines personnalisés à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application web](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Pour plus d’informations, consultez [Attribuer un domaine personnalisé à une application web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Mapper un sous-domaine à l’aide d’un enregistrement CNAME
> * Mapper un domaine racine à l’aide d’un enregistrement A
> * Mapper un domaine générique à l’aide d’un enregistrement CNAME
> * Automatiser le mappage de domaine à l’aide de scripts

Passez au didacticiel suivant pour découvrir comment lier un certificat SSL personnalisé à une application web.

> [!div class="nextstepaction"]
> [Lier un certificat SSL existant à des applications web Azure](app-service-web-tutorial-custom-ssl.md)
