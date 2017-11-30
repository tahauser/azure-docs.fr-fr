---
title: "Guide d’utilisation de SendGrid dans Azure Functions | Microsoft Docs"
description: "Cet article décrit l’utilisation de SendGrid dans Azure Functions"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Guide d’utilisation de SendGrid dans Azure Functions

## <a name="sendgrid-overview"></a>Présentation de SendGrid

Azure Functions prend en charge les liaisons de SendGrid, afin de permettre à vos fonctions d’envoyer des messages électroniques avec quelques lignes de codes et un compte SendGrid.

Pour utiliser l’API SendGrid dans une fonction d’Azure, vous devez avoir un [compte SendGrid](http://SendGrid.com). En outre, vous devez disposer d’une clé d’API SendGrid. Connectez-vous à votre compte SendGrid et cliquez sur **Paramètres** puis **Clé API** pour générer une clé API. Gardez cette clé sous la main, car vous l’utiliserez dans une étape à venir.

Vous êtes maintenant prêt à créer une application Azure Function.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function 

Les applications Azure Function sont des conteneurs pour une ou plusieurs fonctions Azure. Les fonctions Azure sont exactement cela : des fonctions. Chaque fonction Azure est liée à un déclencheur, qui est un événement qui lance la fonction.
Chaque fonction peut contenir un nombre quelconque de liaisons d’entrée ou de sortie. Les liaisons sont des services que vous pouvez utiliser dans une fonction. SendGrid est une liaison de sortie que vous pouvez utiliser pour envoyer des messages électroniques. 

1. Connectez-vous au portail Azure et [créez une Azure Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) ou ouvrez-en une existante. 
2. Créez une fonction Azure. Pour faire simple, choisissez un déclencheur manuel et C#. 

 ![Création d’une fonction Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Configuration de SendGrid pour une utilisation dans une application Azure Function

Vous devez stocker votre clé d’API SendGrid comme paramètre d’application pour l’utiliser dans une fonction. Le champ ApiKey n’est pas votre clé d’API SendGrid réelle, mais un paramètre d’application que vous définissez et qui représente votre clé d’API réelle. Votre clé est stockée de cette façon à des fins de sécurité, car elle est séparée de tout code et tous fichiers qui peuvent être consultés avec le contrôle de code source.

- Créez une clé **AppSettings** dans les **Paramètres de l’application** de votre application de fonction.

 ![Création d’une fonction Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Configuration des liaisons de sortie SendGrid

SendGrid est disponible en tant que liaison de sortie de fonction Azure. Pour créer une liaison de sortie SendGrid :

1. Accédez à l’onglet **Intégrer** de la fonction dans le portail Azure.
2. Cliquez sur **Nouvelle sortie** pour créer une liaison de sortie SendGrid.
3. Renseignez les propriétés **Clé API** et **Nom du paramètre de message**. Si vous le souhaitez, vous pouvez entrer d’autres propriétés maintenant, ou les coder à la place. Ces paramètres peuvent être utilisés comme valeurs par défaut.

 ![Configuration des liaisons de sortie SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

L’ajout d’une liaison à une fonction crée un fichier appelé **function.json** dans le dossier de votre fonction. Ce fichier contient les mêmes informations que celles que vous voyez dans l’onglet **Intégrer** de la fonction Azure, mais au format Json. Le réglage des champs **clé API**, **message**, et **de** crée les entrées suivantes dans le fichier **function.json** : 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Si vous préférez, vous pouvez modifier ce fichier vous-même directement.

Maintenant que vous avez créé et configuré Function App et la fonction, vous pouvez écrire le code pour envoyer un message électronique.

## <a name="write-code-that-creates-and-sends-email"></a>Écriture du code qui crée et envoie un message électronique

L’API SendGrid contient toutes les commandes dont vous avez besoin pour créer et envoyer un message électronique.  

- Remplacez le code dans la fonction par le code suivant :

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Notez que la première ligne contient la directive ```#r``` qui fait référence à l’assembly SendGrid. Après cela, vous pouvez utiliser une instruction ```using``` pour accéder plus facilement aux objets dans cet espace de noms. Dans le code, créez des instances des objets ```Mail```, ```Personalization``` et ```Content``` qui composent le message électronique à partir de l’API SendGrid. Lorsque vous renvoyez le message, SendGrid le livre. 

La signature de la fonction contient également un paramètre de sortie supplémentaire de type ```Mail``` nommé ```message```. Les liaisons d’entrée et de sortie s’expriment elles-mêmes en tant que paramètres de fonction dans le code. 

2. Testez votre code en cliquant sur **Test** et en entrant un message dans le champ **Corps de la requête**, puis en cliquant sur le bouton **Exécuter**.

 ![Test de votre code](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Consultez votre messagerie électronique pour vérifier que SendGrid a envoyé le message électronique. Il devrait être livré à l’adresse dans le code de l’étape 1 et contenir le message du **Corps de la requête**.

## <a name="next-steps"></a>Étapes suivantes
Cet article a montré comment utiliser le service SendGrid pour créer et envoyer du courrier électronique. Pour plus d’informations sur l’utilisation d’Azure Functions dans vos applications, consultez les rubriques suivantes : 

- [Meilleures pratiques pour Azure Functions](functions-best-practices.md) répertorie les meilleures pratiques à utiliser lors de la création de fonctions d’Azure.

- [Référence du développeur Azure Functions](functions-reference.md) Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.

- Le didacticiel [Test d’Azure Functions](functions-test-a-function.md) décrit plusieurs outils et techniques permettant de tester vos fonctions.