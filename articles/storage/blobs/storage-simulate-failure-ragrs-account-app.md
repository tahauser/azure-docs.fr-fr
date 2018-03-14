---
title: "Simuler un échec d’accès au stockage redondant avec accès en lecture dans Azure | Microsoft Docs"
description: "Simuler une erreur d’accès au stockage géoredondant avec accès en lecture"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: v-ruogun
ms.openlocfilehash: ea57ba54fefb1942dcbdbde6e68b20d6290d9fd6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simuler un échec d’accès au stockage redondant avec accès en lecture

Ce didacticiel est le deuxième d’une série.  Dans ce didacticiel, vous pouvez utiliser [Fiddler](#simulate-a-failure-with-fiddler) ou le [routage statique](#simulate-a-failure-with-an-invalid-static-route) pour simuler l’échec des demandes d’accès au point de terminaison principal de votre compte de stockage RA-GRS ([géoredondant avec accès en lecture](../common/storage-redundancy.md#read-access-geo-redundant-storage)) et obliger l’application à effectuer la lecture à partir du point de terminaison secondaire.

![Application de scénario](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Pour suivre ce didacticiel, vous devez avoir suivi le didacticiel précédent relatif au stockage : [Rendre vos données d’application hautement disponibles avec le stockage Azure][previous-tutorial].

Dans ce deuxième volet, vous apprenez à :

> [!div class="checklist"]
> * Exécuter et interrompre l’application
> * Simuler une défaillance avec [Fiddler](#simulate-a-failure-with-fiddler) ou [un itinéraire statique non valide](#simulate-a-failure-with-an-invalid-static-route) 
> * Simuler la restauration du point de terminaison principal


## <a name="prerequisites"></a>Prérequis

Pour simuler une défaillance à l’aide de Fiddler : 

* Télécharger et [installer Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simuler une défaillance à l’aide de Fiddler

Pour simuler une défaillance avec Fiddler, vous injectez un échec de réponse vis-à-vis des demandes d’accès au point de terminaison principal de votre compte de stockage RA-GRS.

Suivez les étapes ci-après pour simuler une défaillance ainsi que la restauration du point de terminaison principal avec Fiddler.

### <a name="launch-fiddler"></a>Lancer Fiddler

Ouvrez Fiddler, sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles).

![Personnaliser les règles de Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor se lance en affichant le fichier **SampleRules.js**. Ce fichier sert à personnaliser Fiddler. Collez l’exemple de code suivant dans la fonction `OnBeforeResponse`. Le nouveau code est commenté pour que la logique qu’il crée ne soit pas implémentée immédiatement. Une fois l’opération effectuée, sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Coller la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Démarrer et interrompre l’application

Exécutez l’application dans votre éditeur IDE ou texte. Une fois que l’application a commencé sa lecture du point de terminaison principal, appuyez sur **une touche** dans la fenêtre de console pour mettre en pause l’application.

### <a name="simulate-failure"></a>Simuler une défaillance

Une fois l’application suspendue, vous pouvez supprimer les marques de commentaires de la règle personnalisée que nous avons enregistrée précédemment dans Fiddler. Cet exemple de code recherche les demandes d’accès au compte de stockage RA-GRS. Si le chemin contient le nom de l’image, `HelloWorld`, il retourne le code de réponse `503 - Service Unavailable`.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) -> **Customize Rules** (Personnaliser les règles).  Décommentez les lignes suivantes, remplacez `STORAGEACCOUNTNAME` par le nom de votre compte de stockage. Sélectionnez **File** (Fichier) -> **Save** (Enregistrer) pour enregistrer les changements apportés. 

> [!NOTE]
> Si vous exécutez l’exemple d’application sur Linux, vous devez redémarrer Fiddler chaque fois que vous modifiez le fichier **CustomRule.js** afin que Fiddler puisse installer la logique personnalisée. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**.

Une fois que l’application a repris son exécution, les demandes d’accès au point de terminaison principal n’aboutissent plus. L’application tente de se reconnecter au point de terminaison principal à 5 reprises. Après cinq tentatives non réussies, elle demande l’image au point de terminaison secondaire en lecture seule. Une fois que l’application a réussi à récupérer l’image à 20 reprises à partir du point de terminaison secondaire, elle tente de se connecter au point de terminaison principal. Si le point de terminaison principal est toujours inaccessible, l’application reprend sa lecture du point de terminaison secondaire. Ce modèle correspond au modèle [Disjoncteur](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) décrit dans le didacticiel précédent.

![Coller la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simuler la restauration du point de terminaison principal

Une fois que vous avez défini la règle personnalisée Fiddler à l’étape précédente, les demandes d’accès au point de terminaison principal cessent d’aboutir. Pour simuler le retour à un fonctionnement normal du point de terminaison principal, supprimez la logique d’injection de l’erreur `503`.

Pour interrompre l’application, appuyez sur **une touche**.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles).  Commentez ou supprimez la logique personnalisée dans la fonction `OnBeforeResponse`, en laissant la fonction par défaut. Sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

![Supprimer la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Une fois l’opération effectuée, appuyez sur **une touche** pour permettre à l’application de reprendre son exécution. L’application poursuit sa lecture du point de terminaison principal jusqu’à ce qu’elle atteigne 999 lectures.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simuler une défaillance avec un itinéraire statique non valide 
Vous pouvez créer un itinéraire statique non valide pour toutes les demandes d’accès au point de terminaison principal de votre compte de stockage RA-GRS [(géoredondant avec accès en lecture)](../common/storage-redundancy.md#read-access-geo-redundant-storage). Dans ce didacticiel, l’hôte local sert de passerelle pour acheminer les demandes de routage vers le compte de stockage. Le recours à l’hôte local en tant que passerelle amène toutes les demandes d’accès au point de terminaison principal de votre compte de stockage à effectuer une boucle dans l’hôte, ce qui engendre une défaillance. Suivez les étapes ci-après pour simuler une défaillance ainsi que la restauration du point de terminaison principal avec un itinéraire statique non valide. 

### <a name="start-and-pause-the-application"></a>Démarrer et interrompre l’application

Exécutez l’application dans votre éditeur IDE ou texte. Une fois que l’application a commencé sa lecture du point de terminaison principal, appuyez sur **une touche** dans la fenêtre de console pour mettre en pause l’application. 

### <a name="simulate-failure"></a>Simuler une défaillance

Une fois l’application suspendue, démarrez l’invite de commandes sur Windows en tant qu’administrateur ou exécutez le terminal en tant qu’utilisateur racine sur Linux. Pour obtenir plus d’informations sur le domaine du point de terminaison principal du compte de stockage, entrez la commande suivante dans une invite de commandes ou un terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Remplacez `STORAGEACCOUNTNAME` par le nom de votre compte de stockage. Copiez l’adresse IP de votre compte de stockage dans un éditeur de texte pour une utilisation ultérieure. Pour obtenir l’adresse IP de l’hôte local, entrez `ipconfig` dans l’invite de commandes Windows ou `ifconfig` dans le terminal Linux. 

Pour ajouter un itinéraire statique relatif à un hôte de destination, entrez la commande suivante dans une invite de commandes Windows ou un terminal Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Remplacez `<destination_ip>` par l’adresse IP du compte de stockage, et `<gateway_ip>` par l’adresse IP de l’hôte local. Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**.

Une fois que l’application a repris son exécution, les demandes d’accès au point de terminaison principal n’aboutissent plus. L’application tente de se reconnecter au point de terminaison principal à 5 reprises. Après cinq tentatives non réussies, elle demande l’image au point de terminaison secondaire en lecture seule. Une fois que l’application a réussi à récupérer l’image à 20 reprises à partir du point de terminaison secondaire, elle tente de se connecter au point de terminaison principal. Si le point de terminaison principal est toujours inaccessible, l’application reprend sa lecture du point de terminaison secondaire. Ce modèle correspond au modèle [Disjoncteur](/azure/architecture/patterns/circuit-breaker.md) décrit dans le didacticiel précédent.

### <a name="simulate-primary-endpoint-restoration"></a>Simuler la restauration du point de terminaison principal

Pour simuler la reprise du fonctionnement du point de terminaison principal, supprimez l’itinéraire statique du point de terminaison principal à partir de la table de routage. Ainsi, toutes les demandes d’accès au point de terminaison principal peuvent être acheminées au moyen de la passerelle par défaut. 

Pour supprimer l’itinéraire statique d’un hôte de destination, le compte de stockage, entrez la commande suivante dans une invite de commandes Windows ou un terminal Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**. L’application poursuit sa lecture du point de terminaison principal jusqu’à ce qu’elle atteigne 999 lectures.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>étapes suivantes

Dans ce deuxième volet, vous avez appris à simuler une défaillance pour tester le stockage géoredondant avec accès en lecture. Vous avez notamment appris à :

> [!div class="checklist"]
> * Exécuter et interrompre l’application
> * Simuler une défaillance avec [Fiddler](#simulate-a-failure-with-fiddler) ou [un itinéraire statique non valide](#simulate-a-failure-with-an-invalid-static-route) 
> * Simuler la restauration du point de terminaison principal

Suivez ce lien pour consulter des exemples de stockage préconçus.

> [!div class="nextstepaction"]
> [Exemples de script de stockage Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md