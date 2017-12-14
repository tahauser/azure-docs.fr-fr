---
title: "Simuler un échec d’accès au stockage redondant avec accès en lecture dans Azure | Microsoft Docs"
description: "Simuler une erreur d’accès au stockage géoredondant avec accès en lecture"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: f709e216f9308c4405776b25ca44b0aaddd3d3f8
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simuler un échec d’accès au stockage redondant avec accès en lecture

Ce didacticiel est le deuxième d’une série. Dans ce didacticiel, vous injectez un échec de réponse à l’aide de Fiddler pour les demandes d’accès à un compte de stockage RA-GRS ([géoredondant avec accès en lecture](../common/storage-redundancy.md#read-access-geo-redundant-storage)) afin de simuler une défaillance et d’obliger l’application à effectuer la lecture à partir du point de terminaison secondaire.

![Application de scénario](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Dans ce deuxième volet, vous apprenez à :

> [!div class="checklist"]
> * Exécuter et interrompre l’application
> * Simuler une défaillance
> * Simuler la restauration du point de terminaison principal

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

* Téléchargez et installez [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Pour suivre ce didacticiel, vous devez avoir suivi le didacticiel précédent relatif au stockage : [Rendre vos données d’application hautement disponibles avec le stockage Azure][previous-tutorial].

## <a name="launch-fiddler"></a>Lancer Fiddler

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

## <a name="start-and-pause-the-application"></a>Démarrer et interrompre l’application

Dans Visual Studio, appuyez sur **F5** ou sélectionnez **Démarrer** pour commencer le débogage de l’application. Une fois que l’application a commencé sa lecture du point de terminaison principal, appuyez sur **une touche** dans la fenêtre de console pour mettre en pause l’application.

## <a name="simulate-failure"></a>Simuler une défaillance

Une fois l’application en pause, vous pouvez désormais décommenter la règle personnalisée que nous avons enregistrée précédemment dans Fiddler. Cet exemple de code recherche les demandes d’accès au compte de stockage RA-GRS. Si le chemin contient le nom de l’image, `HelloWorld`, il retourne le code de réponse `503 - Service Unavailable`.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) -> **Customize Rules** (Personnaliser les règles).  Décommentez les lignes suivantes, remplacez `STORAGEACCOUNTNAME` par le nom de votre compte de stockage. Sélectionnez **File** (Fichier) -> **Save** (Enregistrer) pour enregistrer les changements apportés.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**.

Une fois que l’application a repris son exécution, les demandes d’accès au point de terminaison principal n’aboutissent plus. L’application tente de se reconnecter au point de terminaison principal à 5 reprises. Après cinq tentatives non réussies, elle demande l’image au point de terminaison secondaire en lecture seule. Une fois que l’application a réussi à récupérer l’image à 20 reprises à partir du point de terminaison secondaire, elle tente de se connecter au point de terminaison principal. Si le point de terminaison principal est toujours inaccessible, l’application reprend sa lecture du point de terminaison secondaire. Ce modèle correspond au modèle [Disjoncteur](/azure/architecture/patterns/circuit-breaker.md) décrit dans le didacticiel précédent.

![Coller la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simuler la restauration du point de terminaison principal

Une fois que vous avez défini la règle personnalisée Fiddler à l’étape précédente, les demandes d’accès au point de terminaison principal cessent d’aboutir. Pour simuler le retour à un fonctionnement normal du point de terminaison principal, supprimez la logique d’injection de l’erreur `503`.

Pour interrompre l’application, appuyez sur **une touche**.

### <a name="remove-the-custom-rule"></a>Supprimer la règle personnalisée

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles).  Commentez ou supprimez la logique personnalisée dans la fonction `OnBeforeResponse`, en laissant la fonction par défaut. Sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

![Supprimer la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Une fois l’opération effectuée, appuyez sur **une touche** pour permettre à l’application de reprendre son exécution. L’application poursuit sa lecture du point de terminaison principal jusqu’à ce qu’elle atteigne 999 lectures.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce deuxième volet, vous avez appris à simuler une défaillance pour tester le stockage géoredondant avec accès en lecture. Vous avez notamment appris à :

> [!div class="checklist"]
> * Exécuter et interrompre l’application
> * Simuler une défaillance
> * Simuler la restauration du point de terminaison principal

Suivez ce lien pour consulter des exemples de stockage préconçus.

> [!div class="nextstepaction"]
> [Exemples de script de stockage Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md