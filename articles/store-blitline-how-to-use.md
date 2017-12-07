---
title: "Utilisation de Blitline pour le traitement d'image - Guide des fonctionnalités Azure"
description: "Apprenez à utiliser le service Blitline pour traiter les images au sein d'une application Microsoft Azure."
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: cwatson
ms.openlocfilehash: 254af305592ebef755ccfcb3ae4367b27fb0fc4a
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Utilisation de Blitline avec Azure et Azure Storage
Ce guide explique comment accéder aux services Blitline et comment envoyer des tâches à Blitline.

## <a name="what-is-blitline"></a>Présentation de Blitline
Blitline est un service de traitement d'image sur le cloud. Il offre un traitement d'image de niveau entreprise pour un prix dérisoire par rapport à ce qu'il vous en coûterait si vous le génériez vous-même.

Le traitement d'image s'effectue à de très nombreuses reprises. Il est généralement intégralement reconçu pour chaque site Web. Nous sommes bien placés pour le savoir, car nous en avons conçu un nombre incalculable de fois. Un jour, nous avons décidé qu'il était temps de proposer ce service à tout un chacun. Nous savons effectuer cette action, de façon rapide et efficace, afin de faire gagner du temps de travail à tous.

Pour plus d'informations, consultez la page [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Éléments non proposés par Blitline
Pour clarifier l'utilité de Blitline, il est plus simple d'identifier les éléments non proposés avant de poursuivre.

* Blitline ne possède pas de widgets HTML pour le téléchargement d'images. Vous devez disposer d'images disponibles publiquement ou avec des droits d'accès restreints pour que Blitline puisse y accéder.
* Blitline n'offre pas de traitement d'image en direct, contrairement à Aviary.com.
* Blitline n'accepte pas de téléchargements d'images, vous ne pouvez donc pas envoyer vos images à Blitline directement. Vous devez les placer sur Azure Storage ou d'autres emplacements pris en charge par Blitline, puis indiquer à Blitline où aller les récupérer.
* Blitline est massivement parallèle et n'effectue pas de traitement synchrone. Vous devez nous fournir une URL de retour et nous vous préviendrons lorsque nous aurons fini le traitement.

## <a name="create-a-blitline-account"></a>Création d'un compte Blitline
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Création d'une tâche Blitline
Blitline utilise JSON pour définir les actions que vous souhaitez effectuer sur une image. Ce JSON se compose de quelques champs simples.

Vous en trouverez l'exemple le plus simple ci-dessous :

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Dans cet exemple, JSON prendra une image « src » « ...boys.jpeg » et la redimensionnera au format 240 x 140.

Vous trouverez l'ID d'application dans votre onglet **INFORMATIONS DE CONNEXION** ou **GÉRER** sur Azure. Il s'agit de votre identifiant secret qui vous permet d'exécuter des tâches sur Blitline.

Le paramètre « save » identifie les informations relatives à l'endroit où vous souhaitez placer l'image après l'avoir traitée. Dans cet exemple sans importance, nous n'avons pas défini d'endroit. Si aucun endroit n'est défini, Blitline la stockera localement (et temporairement) dans un emplacement de cloud unique. Vous pourrez obtenir cet emplacement grâce au JSON renvoyé par Blitline lorsque vous effectuez le Blitline. L'identificateur « image » est requis et vous est renvoyé pour l'identification de cette image sauvegardée particulière.

Vous trouverez plus d’informations sur les *fonctions* prises en charge à l’adresse suivante : <http://www.blitline.com/docs/functions>

Vous pouvez également trouver de la documentation sur les options de tâche à l’adresse suivante : <http://www.blitline.com/docs/api>

Lorsque vous avez votre JSON, vous devez simplement le **PUBLIER** sur `http://api.blitline.com/job`.

Le JSON renvoyé ressemblera à ceci :

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Ceci vous indique que Blitline a reçu votre requête et qu'il l'a placée dans une file d'attente de traitement. Une fois l'image terminée, elle sera disponible à l'adresse suivante : **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Enregistrement d'une image sur votre compte Azure Storage
Si vous possédez un compte Azure Storage, vous pouvez facilement demander à Blitline d'envoyer les images traitées sur votre conteneur Azure. En ajoutant un paramètre « azure_destination », vous définissez l'emplacement et les autorisations pour l'envoi par Blitline.

Voici un exemple :

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


En remplissant les valeurs EN MAJUSCULE avec vos propres valeurs, vous pouvez envoyer ce JSON à l'adresse http://api.blitline.com/job. L'image « src » sera traitée avec un filtre de flou, puis sera envoyée à votre destination Azure.

### <a name="please-note"></a>Notez ce qui suit :
La SAP doit contenir toute l'adresse SAP, y compris le nom du fichier de destination.

Exemple :

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Vous pouvez également lire la dernière édition des documents Azure Storage de Blitline [ici](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Étapes suivantes
Rendez-vous sur blitline.com pour découvrir toutes nos autres fonctions :

* Documents de point de terminaison d'API Blitline <http://www.blitline.com/docs/api>
* Fonctions d'API Blitline <http://www.blitline.com/docs/functions>
* Exemples d'API Blitline <http://www.blitline.com/docs/examples>
* Bibliothèque Nuget tierce <http://nuget.org/packages/Blitline.Net>

