---
title: "Problème de configuration de l’authentification unique avec mot de passe pour une application non issue de la galerie | Microsoft Docs"
description: "Comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de la configuration d’une authentification unique avec mot de passe pour les applications qui ne figurent pas dans la galerie d’applications Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e5c8562b89b70236d9d65a5f8765ae386d6831b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problème de configuration de l’authentification unique avec mot de passe pour une application non issue de la galerie

Cet article vous aide à comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de la configuration d’une **authentification unique avec mot de passe** pour une application non issue de la galerie.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Comment capturer les champs de connexion d’une application

La capture des champs de connexion est uniquement prise en charge pour les pages de connexion HTML et **n’est pas prise en charge pour les pages de connexion non standard** comme celles qui utilisent Flash ou d’autres technologies non HTML.

Deux options s’offrent à vous pour capturer des champs de connexion dans vos applications personnalisées :

-   La capture de champs de connexion automatique

-   La capture de champs de connexion manuelle

La **capture de champs de connexion automatique** fonctionne avec la plupart des pages de connexion HTML, si celles-ci utilisent des **ID DIV connus pour les champs de saisie du nom d’utilisateur et du mot de passe**. Ce processus décompose le code HTML de la page pour y rechercher des ID DIV qui répondent à certains critères, puis enregistre ces métadonnées pour cette application à des fins de réutilisation ultérieure des mots de passe.

La **capture de champs de connexion manuelle** est utilisable quand le **fournisseur de l’application n’a pas libellé** les champs d’entrée utilisés pour la connexion. La capture de champs de connexion manuelle peut également se révéler utile lorsque le **fournisseur affiche plusieurs champs** non détectables automatiquement. Azure AD peut stocker les données de tous les champs de la page de connexion, sous réserve que vous nous indiquiez l’emplacement de chacun d’eux sur la page.

En règle générale, **si la capture de champs de connexion automatique ne fonctionne pas, essayez l’option manuelle**.

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Comment capturer automatiquement les champs de connexion d’une application

Pour configurer **l’authentification unique avec mot de passe** d’une application à l’aide de la **capture de champs de connexion automatique**, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  Spécifiez **l’URL de connexion**, autrement dit l’URL au niveau de laquelle les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter. **Vérifiez que les champs de connexion sont visibles dans l’URL que vous fournissez**.

10. Cliquez sur le bouton **Enregistrer** .

11. Une fois cette opération effectuée, cette URL est automatiquement décomposée pour afficher les zones de saisie du nom d’utilisateur et du mot de passe. Vous pouvez ensuite utiliser Azure AD pour transmettre en toute sécurité les mots de passe à cette application à l’aide de l’extension de navigateur du volet d’accès.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Comment capturer manuellement les champs de connexion d’une application

Pour que vous puissiez capturer manuellement les champs de connexion, l’extension de navigateur du volet d’accès doit être installée et **ne doit pas s’exécuter en mode inPrivate, incognito ou privé**. Pour installer l’extension de navigateur, suivez les étapes de la section [Comment installer l’extension de navigateur du volet d’accès](#i-cannot-manually-detect-sign-in-fields-for-my-application).

Pour configurer **l’authentification unique avec mot de passe** d’une application à l’aide de la **capture de champs de connexion manuelle**, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  Spécifiez **l’URL de connexion**, autrement dit l’URL au niveau de laquelle les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter. **Vérifiez que les champs de connexion sont visibles dans l’URL que vous fournissez**.

10. Cliquez sur le bouton **Enregistrer** .

11. Une fois cette opération effectuée, cette URL est automatiquement décomposée pour afficher les zones de saisie du nom d’utilisateur et du mot de passe. Vous pouvez ensuite utiliser Azure AD pour transmettre en toute sécurité les mots de passe à cette application à l’aide de l’extension de navigateur du volet d’accès. Si l’opération échoue, vous pouvez **modifier le mode de connexion pour qu’il utilise la capture de champs de connexion manuelle** en poursuivant à l’étape 12.

12. Cliquez sur **Configurer les paramètres d’authentification unique par mot de passe de** &lt;nom de l’application&gt;.

13. Sélectionnez l’option de configuration **Détecter manuellement les champs de connexion**.

14. Cliquez sur **OK**.

15. Cliquez sur **Enregistrer**.

16. Suivez les instructions à l’écran pour utiliser le volet d’accès.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Une erreur « Nous n’avons trouvé aucun champ de connexion à cette URL. » s’affiche

Vous obtenez cette erreur en cas d’échec de la détection automatique des champs de connexion. Pour résoudre ce problème, essayez d’utiliser la détection manuelle des champs de connexion en suivant les étapes de la section [Comment capturer manuellement les champs de connexion d’une application](#how-to-manually-capture-sign-in-fields-for-an-application).

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Une erreur « Impossible d’enregistrer la configuration de l’authentification unique » s’affiche

Bien que cela soit rare, il peut arriver que la mise à jour de la configuration de l’authentification unique échoue. Pour résoudre ce problème, essayez d’enregistrer de nouveau la configuration de l’authentification unique.

Si le problème persiste, ouvrez une demande de support et fournissez les informations collectées dans les sections [Comment afficher les détails d’une notification du portail](#i-cannot-manually-detect-sign-in-fields-for-my-application) et [Comment obtenir de l’aide en envoyant les détails de la notification à un ingénieur du support technique](#how-to-get-help-by-sending-notification-details-to-a-support-engineer).

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Impossible de détecter manuellement les champs de connexion de mon application

Voici quelques-uns des comportements que vous pouvez observer lorsque la détection manuelle ne fonctionne pas :

-   Le processus de capture manuelle semble fonctionner, mais les champs capturés ne sont pas corrects.

-   Les champs corrects ne sont pas mis en surbrillance lors de l’exécution du processus de capture.

-   Le processus de capture me redirige comme prévu vers la page de connexion de l’application, mais rien ne se produit.

-   La capture manuelle semble fonctionner, mais l’authentification unique ne se produit pas lorsque mes utilisateurs accèdent à l’application à partir du volet d’accès.

Si vous rencontrez l’un de ces problèmes, vérifiez les points suivants :

-   Assurez-vous que la dernière version de l’extension de navigateur du volet d’accès est **installée** et **activée** en suivant les étapes de la section [Comment installer l’extension de navigateur du volet d’accès](#how-to-install-the-access-panel-browser-extension).

-   Assurez-vous que vous ne tentez pas d’exécuter le processus de capture alors que votre navigateur est en **mode incognito, inPrivate ou privé**. Ces modes ne prennent pas en charge l’extension du volet d’accès.

-   Assurez-vous que vos utilisateurs ne tentent pas de se connecter à l’application à partir du volet d’accès alors qu’ils sont en **mode incognito, inPrivate ou privé**. Ces modes ne prennent pas en charge l’extension du volet d’accès.

-   Tentez une nouvelle fois d’exécuter le processus de capture manuelle en veillant à ce que les marqueurs rouges soient sur les champs appropriés.

-   Si le processus de capture manuelle semble bloqué ou que la page de connexion ne réagit pas (cas 3 ci-dessus), tentez de réexécuter le processus de capture manuelle. Mais cette fois, à la fin du processus, appuyez sur le bouton **F12** pour ouvrir la console de développement de votre navigateur. Ouvrez la **console** et saisissez **window.location=”&lt;entrez l’URL de connexion que vous avez spécifiée lors de la configuration de l’application&gt;”**, puis appuyez sur **Entrée**. Cette manipulation force une redirection de page qui met fin au processus de capture et stocke les champs qui ont été capturés.

Si aucune de ces approches ne fonctionne pour vous, le support technique peut vous aider. Ouvrez une demande de support et fournissez des informations sur les méthodes utilisées pour résoudre le problème, ainsi que les informations collectées dans les sections [Comment afficher les détails d’une notification du portail](#i-cannot-manually-detect-sign-in-fields-for-my-application) et [Comment obtenir de l’aide en envoyant les détails de la notification à un ingénieur du support technique](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) (le cas échéant).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, effectuez les étapes suivantes :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant **qu’utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  Vous êtes redirigé vers le lien de téléchargement selon votre navigateur. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois l’extension installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **démarrer** vos applications à authentification unique avec mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Firefox à partir des liens directs ci-dessous :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Comment afficher les détails d’une notification du portail

Vous pouvez afficher les détails d’une notification provenant du portail en suivant les étapes ci-dessous :

1.  Cliquez sur l’icône **Notifications** (cloche) dans le coin supérieur droit du portail Azure.

2.  Sélectionnez n’importe quelle notification avec l’état **Erreur**, signalée par un (!) rouge.

  >!NOTE] Vous ne pouvez pas cliquer sur les notifications affichant l’état **Réussi** ou **En cours**.
  >
  >

3.  Le volet **Détails de la notification** s’affiche.

4.  Utilisez ces informations pour en savoir plus sur le problème.

5.  Si vous avez encore besoin d’aide, vous pouvez également partager ces informations avec un ingénieur du support technique ou du groupe de produits qui vous assistera pour résoudre votre problème.

6.  Cliquez sur **l’icône** de **copie** à droite de la zone de texte **Erreur de copie** pour copier tous les détails de la notification afin de les partager avec un ingénieur du support technique ou du groupe de produits.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Comment obtenir de l’aide en envoyant les détails de la notification à un ingénieur du support technique

Il est très important que vous partagiez **tous les détails ci-dessous** avec un ingénieur du support technique si vous avez besoin d’aide, car celui-ci peut vous permettre de résoudre rapidement le problème. Vous pouvez **effectuer une capture d’écran** ou cliquer sur **l’icône d’erreur de copie** située à droite de la zone de texte **Erreur de copie**.

## <a name="notification-details-explained"></a>Explication des détails de la notification

Vous trouverez ci-dessous plus d’informations sur la signification de chaque notification ainsi que des exemples de chacune d’elles.

### <a name="essential-notification-items"></a>Éléments essentiels de la notification

-   **Titre** : titre descriptif de la notification

    -   Exemple : **Paramètres de proxy d’application**

-   **Description** : description de ce qui s’est produit suite à l’opération

    -   Exemple : **L’URL interne entrée est déjà utilisée par une autre application**

-   **ID de notification** : ID unique de la notification

    -   Exemple : **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID de demande client** : ID de la demande spécifique effectuée par votre navigateur

    -   Exemple : **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Horodatage UTC** : horodatage de l’affichage de la notification, au format UTC

    -   Exemple : **2017-03-23T19:50:43.7583681Z**

-   **ID de transaction interne** : ID interne permettant de rechercher l’erreur dans nos systèmes

    -   Exemple : **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** : utilisateur qui a effectué l’opération

    -   Exemple : **tperkins@f128.info**

-   **ID de locataire** : ID unique du locataire dont l’utilisateur ayant effectué l’opération était membre

    -   Exemple : **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID de l’objet utilisateur** : ID unique de l’utilisateur ayant effectué l’opération

    -   Exemple : **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Éléments détaillés de la notification

-   **Nom d’affichage** : **(peut être vide)** nom d’affichage plus détaillé de l’erreur

    -   Exemple *: **Paramètres de proxy d’application**

-   **État** : état spécifique de la notification

    -   Exemple *: **Échec**

-   **ID de l’objet** : **(peut être vide)** ID de l’objet sur lequel l’opération a été effectuée

    -   Exemple : **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Détails** : description détaillée de ce qui s’est produit suite à l’opération

    -   Exemple : **l’URL interne 'http://bing.com/' n’est pas valide car elle est déjà en cours d’utilisation**

-   **Erreur de copie** : cliquez sur **l’icône de copie** à droite de la zone de texte **Erreur de copie** pour copier tous les détails de la notification afin de les partager avec un ingénieur du support technique ou du groupe de produits

    -   Exemple : ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)

