---
title: Utilisation de la personnalisation de la langue - Azure AD B2C | Microsoft Docs
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C : utilisation de la personnalisation de la langue

>[!NOTE]
>Cette fonctionnalité est en version préliminaire publique.
>

La personnalisation de la langue permet à votre stratégie de prendre en charge plusieurs langues pour répondre aux besoins de votre client.  Microsoft fournit les traductions en 36 langues (consultez les [informations supplémentaires](#additional-information)), mais vous pouvez également fournir vos propres traductions pour n’importe quelle langue.  Même si votre expérience est disponible dans une seule langue, vous pouvez personnaliser n’importe quel texte sur les pages.  

## <a name="how-does-language-customization-work"></a>Comment fonctionne la personnalisation de la langue ?
La personnalisation de la langue vous permet de sélectionner les langues dans lesquelles votre parcours utilisateur est disponible.  Une fois que la fonctionnalité est activée, vous pouvez fournir le paramètre de chaîne de requête, ui_locales, de votre application.  Lorsque vous appelez dans Azure AD B2C, nous traduisons votre page en fonction du paramètre régional que vous avez indiqué.  Ce type de configuration vous permet de contrôler intégralement les langues de votre parcours utilisateur et ignore les paramètres de langue du navigateur du client. Vous pouvez également ne pas avoir besoin de ce niveau de contrôle sur les langues que votre client voit.  Si vous n’indiquez pas de paramètre ui_locales, l’expérience du client est régie par les paramètres de son navigateur.  Vous pouvez toujours contrôler les langues dans lesquelles votre parcours utilisateur est traduit en les ajoutant en tant que langues prises en charge.  Si le navigateur d’un client est défini pour afficher une langue que vous ne souhaitez pas prendre en charge, c’est la langue sélectionnée par défaut dans les cultures prises en charge qui est affichée à la place.

1. **Langue spécifiée par le paramètre ui_locales** : après avoir activé la personnalisation de la langue, votre parcours utilisateur est traduit dans la langue indiquée ici.
2. **Langue demandée par le navigateur** : si aucun paramètre ui_locales n’a été spécifié, la page est traduite dans la langue demandée par le navigateur, **si elle a été ajoutée en tant que langue prise en charge**.
3. **Langue par défaut de la stratégie** : si le navigateur ne spécifie aucune langue ou s’il en spécifie une qui n’est pas prise en charge, la page est traduite dans la langue par défaut de la stratégie.

>[!NOTE]
>Si vous utilisez des attributs utilisateur personnalisés, vous devez fournir vos propres traductions. Consultez « [Personnaliser vos chaînes](#customize-your-strings) » pour plus d’informations.
>

## <a name="support-uilocales-requested-languages"></a>Prendre en charge les langues demandées par le paramètre ui_locales 
Les stratégies créées avant la disponibilité générale de la personnalisation de la langue devront activer cette fonctionnalité.  La personnalisation de la langue sera activée par défaut dans les stratégies créées après.  En activant la personnalisation de la langue sur une stratégie, vous pouvez désormais contrôler la langue du parcours utilisateur en ajoutant le paramètre ui_locales.
1. [Suivez ces étapes pour accéder à la page des fonctionnalités B2C sur le portail Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Accédez à une stratégie pour laquelle vous souhaitez activer la traduction.
3. Cliquez sur **Personnalisation de la langue**.  
4. Cliquez **Activer la personnalisation de la langue** en haut.
5. Lisez le contenu de la boîte de dialogue et cliquez sur « Oui ».

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Sélectionnez les langues activées dans votre parcours utilisateur. 
Activez un ensemble de langues dans lesquelles votre parcours utilisateur peut être traduit lorsque le paramètre ui_locales n’est pas fourni.
1. Vérifiez que la personnalisation de la langue est activée dans votre stratégie, comme vu précédemment.
2. Dans votre page **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Sélectionnez une langue que vous souhaitez prendre en charge.
4. Dans le volet Propriétés, configurez **Activé** sur Oui.  
5. Cliquez sur **Enregistrer** dans la partie supérieure du volet.

>[!NOTE]
>Si aucun paramètre ui_locales n’est fourni, la page est traduite dans la langue du navigateur de l’utilisateur uniquement si elle est activée.
>

## <a name="customize-your-strings"></a>Personnaliser vos chaînes
La personnalisation de la langue vous permet de personnaliser les chaînes de votre parcours utilisateur.
1. Vérifiez que la personnalisation de la langue est activée dans votre stratégie, comme vu précédemment.
2. Dans votre page **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Sélectionnez la langue que vous voulez personnaliser.
4. Sélectionnez la page que vous souhaitez modifier.
5. Cliquez sur **Télécharger les valeurs par défaut** (ou **Télécharger les valeurs de remplacements** si vous avez déjà modifié cette langue). 

À la suite de ces opérations, vous obtenez un fichier JSON que vous pouvez utiliser pour commencer à modifier vos chaînes.

### <a name="changing-any-string-on-the-page"></a>Modification de chaînes sur la page
1. Ouvrez le fichier JSON téléchargé précédemment dans un éditeur JSON.
2. Recherchez l’élément que vous souhaitez modifier.  Vous pouvez trouver le `StringId` de la chaîne que vous recherchez, ou recherchez la `Value` que vous souhaitez modifier.
3. Mettez à jour l’attribut `Value` avec ce que vous souhaitez afficher.
4. Pour chaque chaîne que vous souhaitez modifier, n’oubliez pas de basculer `Override` sur **True**.
5. Enregistrez le fichier et téléchargez vos modifications (vous pouvez trouver le contrôle de téléchargement au même emplacement que celui d’où vous avez téléchargé le fichier JSON). 

>[!IMPORTANT]
>Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`.  Si la valeur n’est pas modifiée, l’entrée est ignorée. 
>

### <a name="changing-extension-attributes"></a>Modification des attributs d’extension
Si vous souhaitez modifier la chaîne d’un attribut utilisateur personnalisé, ou en ajouter un au fichier JSON, le format suivant est utilisé :
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Remplacez `<ExtensionAttribute>` par le nom de votre attribut utilisateur personnalisé.  

Remplacez `<ExtensionAttributeValue>` par la nouvelle chaîne à afficher.

### <a name="using-localizedcollections"></a>Utilisation de LocalizedCollections
Si vous souhaitez fournir une liste définie de valeurs pour les réponses, vous devez créer un élément `LocalizedCollections`.  Un élément `LocalizedCollections` est un tableau de paires `Name` et `Value`.  Le `Name` est ce qui est affiché et la `Value` est ce qui est renvoyé dans la revendication.  Pour ajouter un élément `LocalizedCollections`, le format utilisé est le suivant :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` est l’attribut utilisateur auquel cet élément `LocalizedCollections` apporte une réponse.
* `Name` est la valeur affichée à l’utilisateur.
* `Value` est ce qui est renvoyé dans la revendication lorsque cette option est sélectionnée.

### <a name="upload-your-changes"></a>Charger vos modifications
1. Une fois que vous avez terminé de modifier votre fichier JSON, accédez à votre client B2C.
2. Dans votre page **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
3. Sélectionnez la langue pour laquelle vous souhaitez fournir des traductions.
4. Sélectionnez la page pour laquelle vous souhaitez fournir des traductions.
5. Cliquez sur l’icône de dossier et sélectionnez le fichier JSON à charger.
6. Ce changement est automatiquement enregistré dans votre stratégie.

## <a name="using-page-ui-customization-with-language-customization"></a>Utilisation de la personnalisation de l’interface utilisateur de la page avec la personnalisation de la langue

Il existe deux façons de traduire votre contenu HTML.  En activant la [« Personnalisation de la langue »](active-directory-b2c-reference-language-customization.md).  L’activation de cette fonctionnalité permet à Azure AD B2C de transmettre le paramètre Open ID Connect, `ui-locales`, à votre point de terminaison.  Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML personnalisées qui sont spécifiques à la langue.

Nous pouvons également extraire le contenu de plusieurs emplacements selon les paramètres régionaux utilisés.  Dans votre point de terminaison avec CORS activé, vous pouvez configurer une structure de dossiers pour héberger du contenu pour des langues spécifiques et vous appellerez la bonne si vous placez la valeur de caractère générique, `{Culture:RFC5646}`.  Par exemple, si j’ai ceci comme URI de page personnalisée :

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Je peux charger ma page dans `fr` et, lorsqu’elle extrait du contenu html et css, celui-ci est extrait depuis :
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Paramètres régionaux personnalisés

Vous pouvez également ajouter des langues pour lesquelles Microsoft ne fournit pas encore de traductions.  Vous devrez fournir les traductions pour toutes les chaînes de la stratégie.

1. Dans votre page **Modifier une stratégie**, sélectionnez **Personnalisation de la langue**.
2. Sélectionnez **Ajouter une langue personnalisée** en haut de la page.
3. Dans le volet contextuel qui s’ouvre, identifiez la langue pour laquelle vous fournissez des traductions en entrant un code de paramètres régionaux valide.
4. Pour chaque page, vous pouvez télécharger un ensemble de remplacements pour l’anglais et travailler sur les traductions.
5. Une fois que vous avez terminé avec les fichiers JSON, vous pouvez les télécharger pour chaque page.
6. Sélectionnez **Activer** et votre stratégie peut désormais afficher cette langue pour votre utilisateur.
7. Pensez à enregistrer votre langue une fois celle-ci activée.

## <a name="additional-information"></a>Informations supplémentaires

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Les étiquettes de personnalisation de l’interface utilisateur de la page sont conservées comme votre premier jeu de remplacements une fois la personnalisation de la langue activée.
Lorsque vous activez la personnalisation de la langue, les modifications précédemment appliquées aux étiquettes via la personnalisation de l’interface utilisateur de la page sont conservées dans un fichier JSON pour l’anglais (en).  Vous pouvez continuer à modifier vos étiquettes et les autres chaînes en chargeant des ressources de langues dans « Personnalisation de la langue ».
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft s’engage à fournir des traductions à jour pour que vous puissiez les utiliser.
Nous ne cesserons d’améliorer nos traductions pour qu’elles soient conformes à vos attentes.  Nous identifierons les bogues et les modifications dans la terminologie globale et créerons les mises à jour qui s’adapteront en toute transparence à votre parcours utilisateur.
### <a name="support-for-right-to-left-languages"></a>Prise en charge des langues s’écrivant de droite à gauche
Actuellement, nous ne fournissons aucune prise en charge des langues de droite à gauche. Si vous avez besoin de cette fonctionnalité, votez pour elle dans la page de [Commentaires Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traductions des fournisseurs d’identité de réseaux sociaux
Nous proposons le paramètre OIDC ui_locales pour les connexions aux réseaux sociaux, mais il n’est pas honoré par certains fournisseurs d’identité de réseaux sociaux, notamment Facebook et Google. 
### <a name="browser-behavior"></a>Comportement du navigateur
Chrome et Firefox exigent tous deux leur langue définie et s’il s’agit d’une langue prise en charge, elle sera affichée avant la valeur par défaut.  Actuellement, Edge n’exige pas de langue particulière et affiche directement la langue par défaut.

### <a name="what-languages-are-supported"></a>Quelles sont les langues prises en charge ?

| Langage              | Code de langue |
|-----------------------|---------------|
| Bangla                | bn            |
| Tchèque                 | cs            |
| Danois                | da            |
| Allemand                | de            |
| Grec                 | el            |
| Français               | en            |
| Espagnol               | es            |
| Finnois               | fi            |
| Français                | fr            |
| Goudjrati              | gu            |
| Hindi                 | hi            |
| Croate              | hr            |
| Hongrois             | hu            |
| Italien               | it            |
| Japonais              | ja            |
| Kannada               | kn            |
| Coréen                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malais                 | ms            |
| Norvégien Bokmål      | nb            |
| Néerlandais                 | nl            |
| Pendjabi               | pa            |
| Polonais                | pl            |
| Portugais - Brésil   | pt-br         |
| Portugais - Portugal | pt-pt         |
| Roumain              | ro            |
| Russe               | ru            |
| Slovaque                | sk            |
| Suédois               | sv            |
| Tamoul                 | ta            |
| Télougou                | te            |
| Thaï                  | th            |
| Turc               | tr            |
| Chinois - simplifié  | zh-hans       |
| Chinois - traditionnel | zh-hant       |
