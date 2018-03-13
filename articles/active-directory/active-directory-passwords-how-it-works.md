---
title: "Fonctionnement de la réinitialisation de mot de passe libre-service - Azure Active Directory"
description: "Découverte approfondie de la réinitialisation du mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 0cf26846a8f42238de09727a03dc6b50dff746b6
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Découverte approfondie de la réinitialisation de mot de passe libre-service dans Azure AD

Comment fonctionne la réinitialisation de mot de passe libre-service (SSPR) ? Que signifie cette option dans l’interface ? Poursuivez la lecture pour en savoir plus sur SSPR Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Fonctionnement du portail de réinitialisation de mot de passe

Quand un utilisateur accède au portail de réinitialisation du mot de passe, un flux de travail est lancé pour déterminer les éléments suivants :

   * Comment la page doit être localisée ?
   * Le compte d’utilisateur est-il valide ?
   * À quelle organisation l’utilisateur appartient-il ?
   * Où le mot de passe de l’utilisateur est-il géré ?
   * L’utilisateur a-t-il une licence pour utiliser la fonctionnalité ?

Lisez les étapes suivantes pour en savoir plus sur la logique sous-jacente à la page de réinitialisation de mot de passe :

1. L’utilisateur sélectionne le lien **Vous ne parvenez pas à accéder à votre compte ?** ou il accède directement à [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Selon les paramètres régionaux du navigateur, l’expérience est proposée dans la langue appropriée. L’expérience de réinitialisation du mot de passe est localisée dans les mêmes langues que celles prises en charge par Office 365.
2. L’utilisateur entre un ID utilisateur et passe un test CAPTCHA.
3. Azure AD vérifie que l’utilisateur peut utiliser cette fonctionnalité en procédant aux vérifications suivantes :
   * Il vérifie que cette fonctionnalité est activée pour l’utilisateur et qu’il possède une licence Azure AD.
     * Si ce n’est pas le cas, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Vérifie que l’utilisateur a défini les données de test appropriées sur son compte, conformément à la stratégie de l’administrateur.
     * Si la stratégie n’exige qu’un seul test, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins un des tests activés par la stratégie de l’administrateur.
       * Si le test de l’utilisateur n’est pas configuré, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
     * Si la stratégie exige deux tests, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins deux des tests activés par la stratégie de l’administrateur.
       * Si le test de l’utilisateur n’est pas configuré, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Il vérifie si le mot de passe de l’utilisateur est géré localement (fédération ou synchronisation de hachage de mot de passe).
     * Si la réécriture est déployée et que le mot de passe est géré localement, l’utilisateur est autorisé à s’authentifier et à réinitialiser son mot de passe.
     * Si la réécriture n’est pas déployée et que le mot de passe est géré localement, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4. S’il est établi que l’utilisateur est en mesure de réinitialiser son mot de passe, il reçoit des instructions pour mener à bien le processus de réinitialisation.

## <a name="authentication-methods"></a>Méthodes d’authentification

Si SSPR est activé, vous devez sélectionner au moins l’une des options suivantes pour les méthodes d’authentification. Ces options sont parfois appelées « portails ». Nous vous recommandons vivement de choisir au moins deux méthodes d’authentification pour offrir davantage de flexibilité à vos utilisateurs.

* Email
* Téléphone mobile
* Téléphone de bureau
* Questions de sécurité

![Authentification][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Quels sont les champs utilisés dans le répertoire pour l’authentification des données ?

* **Téléphone professionnel** : correspond au numéro de téléphone professionnel.
    * Les utilisateurs ne peuvent pas définir ce champ eux-mêmes. Il doit être défini par un administrateur.
* **Téléphone mobile**  : correspond au téléphone d’authentification (non visible publiquement) ou au téléphone mobile (visible publiquement).
    * Le service commence par rechercher le téléphone d’authentification, puis revient au téléphone mobile si le téléphone d’authentification n’est pas présent.
* **Autre adresse de messagerie** : correspond à l’e-mail d’authentification (non visible publiquement) ou à l’autre adresse de messagerie.
    * Le service commence par rechercher l’e-mail d’authentification, puis revient à l’autre adresse de messagerie.

Par défaut, seuls les attributs cloud de téléphone professionnel et de téléphone mobile sont synchronisés dans votre répertoire cloud à partir de votre répertoire local pour les données d’authentification.

Les utilisateurs peuvent uniquement réinitialiser leur mot de passe s’ils ont des données présentes dans les méthodes d’authentification que l’administrateur a activées et exige.

Si les utilisateurs ne souhaitent pas que leur numéro de téléphone mobile soit visible dans le répertoire, tout en ayant la possibilité de l’utiliser pour la réinitialisation du mot de passe, les administrateurs ne doivent pas le renseigner dans le répertoire. Les utilisateurs doivent ensuite renseigner leur attribut **Téléphone d’authentification** par le biais du [portail d’inscription à la réinitialisation du mot de passe](https://aka.ms/ssprsetup). Les administrateurs peuvent consulter ces informations dans le profil de l’utilisateur, mais elles ne sont pas publiées ailleurs.

### <a name="the-number-of-authentication-methods-required"></a>Nombre de méthodes d’authentification requises

Cette option détermine le nombre minimal de méthodes d’authentification ou de portails disponibles que l’utilisateur doit appliquer pour réinitialiser ou débloquer son mot de passe. Elle peut être définie sur un ou deux.

Les utilisateurs peuvent choisir de fournir plusieurs méthodes d’authentification si l’administrateur les active.

Si un utilisateur n’a pas le nombre minimal requis de méthodes inscrites, une page d’erreur s’affiche et lui indique de contacter un administrateur pour que ce dernier réinitialise son mot de passe.

#### <a name="change-authentication-methods"></a>Changer les méthodes d’authentification

Si vous démarrez avec une stratégie qui présente une seule méthode d’authentification requise pour la réinitialisation ou le déverrouillage et que vous optez ensuite pour deux méthodes, que se passe-t-il ?

| Nombre de méthodes inscrites | Nombre de méthodes requises | Résultat |
| :---: | :---: | :---: |
| 1 ou plus | 1 | Réinitialisation ou déverrouillage **possible** |
| 1 | 2 | Réinitialisation ou déverrouillage **impossible** |
| 2 ou plus | 2 | Réinitialisation ou déverrouillage **possible** |

Si vous changez les types de méthodes d’authentification qu’un utilisateur peut employer, vous risquez d’empêcher par inadvertance les utilisateurs d’employer SSPR s’ils ne disposent pas de la quantité minimale de données.

Exemple : 
1. La stratégie d’origine est configurée avec deux méthodes d’authentification requises. Elle utilise uniquement le numéro de téléphone professionnel et les questions de sécurité. 
2. L’administrateur change la stratégie pour ne plus utiliser les questions de sécurité, mais permet l’utilisation d’un téléphone mobile et d’une autre adresse de messagerie.
3. Les utilisateurs dont les champs de téléphone mobile et d’autre adresse de messagerie ne sont pas renseignés ne peuvent pas réinitialiser leur mot de passe.

### <a name="how-secure-are-my-security-questions"></a>Comment sécuriser mes questions de sécurité ?

Si vous utilisez des questions de sécurité, nous vous recommandons de les utiliser conjointement avec une autre méthode. Les questions de sécurité peuvent être moins sécurisées que d’autres méthodes, car certaines personnes peuvent connaître les réponses aux questions d’un autre utilisateur.

> [!NOTE] 
> Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription. L’administrateur ne peut pas lire ni modifier les questions ou les réponses des utilisateurs.
>

### <a name="security-question-localization"></a>Localisation de la question de sécurité

Toutes les questions prédéfinies ci-après sont localisées dans l’ensemble des langues Office 365 et sont basées sur les paramètres régionaux du navigateur de l’utilisateur :

* Dans quelle ville avez-vous rencontré votre premier(ère) époux/épouse ou compagnon/compagne ?
* Dans quelle ville vos parents se sont-ils rencontrés ?
* Dans quelle ville vit votre frère ou sœur le/la plus proche ?
* Dans quelle ville votre père est-il né ?
* Dans quelle ville était votre premier travail ?
* Dans quelle ville votre mère est-elle née ?
* Dans quelle ville étiez-vous pour le Nouvel an de l’année 2000 ?
* Quel était le nom de votre professeur préféré au lycée ?
* Quel est le nom d’un établissement d’enseignement supérieur auquel vous avez postulé mais auquel vous n’êtes finalement pas allé ?
* Quel est le nom de l’endroit où vous avez organisé la réception de votre premier mariage ?
* Quel est le deuxième prénom de votre père ?
* Quel est votre aliment préféré ?
* Quels sont les nom et prénom de votre grand-mère maternelle ?
* Quel est le deuxième prénom de votre mère ?
* Quel est le mois et l’année de naissance de votre frère/sœur aîné(e) ? (par exemple, novembre 1985)
* Quel est le deuxième prénom de votre frère/sœur aîné(e) ?
* Quels sont les nom et prénom de votre grand-père paternel ?
* Quel est le deuxième prénom de votre frère/sœur cadet(te) ?
* Où avez-vous effectué votre dernière année de l’école primaire ?
* Quels étaient les nom et prénom de votre meilleur ami quand vous étiez enfant ?
* Quels étaient les nom et prénom de votre premier(ère) petit(e) ami(e) ?
* Quel était le nom de votre instituteur préféré à l’école primaire ?
* Quels étaient la marque et le modèle de votre première voiture ou moto ?
* Quel était le nom de votre toute première école ?
* Quel était le nom de l’hôpital ou de la clinique où vous êtes né ?
* Quel était le nom de la rue de la première maison de votre enfance ?
* Quel était le nom de votre héros d’enfance ?
* Quel était le nom de votre peluche préférée ?
* Quel était le nom de votre premier animal domestique ?
* Quel était votre surnom quand vous étiez enfant ?
* Quel était votre sport préféré au lycée ?
* Quel a été votre premier travail ?
* Quels étaient les quatre derniers chiffres de votre numéro de téléphone quand vous étiez enfant ?
* Quand vous étiez petit, qu’auriez-vous voulu être ?
* Quelle est la personne la plus célèbre que vous ayez jamais rencontrée ?

### <a name="custom-security-questions"></a>Questions de sécurité personnalisées

Les questions de sécurité personnalisées ne sont pas localisées pour tous les paramètres régionaux. Toutes les questions personnalisées s’affichent dans la même langue que celle dans laquelle elles sont entrées dans l’interface utilisateur d’administration, même si les paramètres régionaux du navigateur de l’utilisateur sont différents. Si vous avez besoin de questions localisées, vous devez utiliser les questions prédéfinies.

La longueur maximale d’une question de sécurité personnalisée est de 200 caractères.

### <a name="security-question-requirements"></a>Conditions relatives aux questions de sécurité

* La limite minimale du nombre de caractères par réponse est de trois caractères.
* La limite maximale du nombre de caractères par réponse est de 40 caractères.
* L’utilisateur ne peut pas répondre plusieurs fois à la même question.
* L’utilisateur ne peut pas fournir la même réponse à plusieurs questions.
* N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses, y compris les caractères Unicode.
* Le nombre de questions défini doit être supérieur ou égal au nombre de questions requises pour l’inscription.

## <a name="registration"></a>Inscription

### <a name="require-users-to-register-when-they-sign-in"></a>Obliger les utilisateurs à s’inscrire durant la connexion

Pour activer cette option, un utilisateur pouvant demander la réinitialisation du mot de passe doit s’inscrire à la réinitialisation du mot de passe s’il se connecte à des applications en utilisant Azure AD : Les éléments suivants sont inclus :

* Office 365
* Portail Azure
* Volet d'accès
* Applications fédérées
* Applications personnalisées en utilisant Azure AD

Quand l’obligation d’inscription est désactivée, les utilisateurs peuvent inscrire manuellement leurs informations de contact. Ils peuvent visiter le site [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou sélectionner le lien **Réinitialiser mon mot de passe** sous l’onglet **Profil** dans le Panneau d’accès.

> [!NOTE]
> Les utilisateurs peuvent ignorer le portail d’inscription à la réinitialisation du mot de passe en sélectionnant **Annuler** ou en fermant la fenêtre. Dans ce cas, tant qu’ils ne procèdent pas à leur inscription, ils sont invités à s’inscrire chaque fois qu’ils se connectent.
>
> La connexion des utilisateurs n’est pas interrompue s’ils sont déjà connectés.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Définir le nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification

Cette option détermine la période de temps entre la configuration et la reconfirmation des informations d’authentification, et n’est disponible que si vous activez l’option **Obliger les utilisateurs à s’inscrire durant la connexion ?**.

Les valeurs valides sont comprises entre 0 et 730 jours, « 0 » signifiant que les utilisateurs ne sont jamais invités à reconfirmer leurs informations d’authentification.

## <a name="notifications"></a>Notifications

### <a name="notify-users-on-password-resets"></a>Notifier les utilisateurs lors des réinitialisations de mot de passe ?

Si cette option est définie sur **Oui**, l’utilisateur qui réinitialise son mot de passe reçoit un e-mail l’avertissant que son mot de passe a été changé. L’e-mail est envoyé par le biais du portail SSPR à son adresse de messagerie principale et à son autre adresse de messagerie sur fichier dans Azure AD. Personne n’est informé de cet événement de réinitialisation.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ?

Si cette option est définie sur **Oui**, *tous les administrateurs* reçoivent un e-mail sur leur adresse de messagerie principale sur fichier dans Azure AD. Cet e-mail les informe qu’un autre administrateur a changé son mot de passe à l’aide de SSPR.

Exemple : quatre administrateurs font partie d’un environnement. L’administrateur A réinitialise son mot de passe à l’aide de SSPR. Les administrateurs B, C et D reçoivent un e-mail les informant de la réinitialisation de mot de passe.

## <a name="on-premises-integration"></a>Intégration locale

Si vous installez, configurez et activez Azure AD Connect, vous disposez des options supplémentaires suivantes pour les intégrations locales. Si ces options sont grisées, la réécriture n’a pas été correctement configurée. Pour plus d’informations, consultez [Configuration de la réécriture du mot de passe](active-directory-passwords-writeback.md#configure-password-writeback).

![Écriture différée][Writeback]

Cette page fournit un état rapide du client d’écriture différée local. L’un des messages suivants s’affiche en fonction de la configuration actuelle :

* Votre client de réécriture local est opérationnel.
* Azure AD Connect est en ligne et connecté à votre client de réécriture local. Cependant, il semble que la version installée d’Azure AD Connect est obsolète. Pensez à [mettre à niveau Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) pour vous assurer que vous disposez des dernières fonctionnalités de connectivité et des correctifs de bogues importants.
* Malheureusement, nous ne pouvons pas vérifier l’état de votre client de réécriture local, car la version installée d’Azure AD Connect est obsolète. [Mettez à niveau Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) pour être en mesure de vérifier l’état de votre connexion.
* Malheureusement, nous ne sommes pas en mesure de nous connecter à votre client d’écriture différée local pour le moment. [Résolvez les problèmes avec Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) pour restaurer la connexion.
* Malheureusement, nous ne pouvons pas nous connecter à votre client de réécriture local, car la réécriture du mot de passe n’a pas été configurée correctement. [Configurez la réécriture du mot de passe](active-directory-passwords-writeback.md#configure-password-writeback) pour restaurer la connexion.
* Malheureusement, nous ne sommes pas en mesure de nous connecter à votre client d’écriture différée local pour le moment. Cela peut être dû à des problèmes temporaires de notre côté. Si le problème persiste, [résolvez les problèmes avec Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) pour restaurer la connexion.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Réécriture du mot de passe dans votre répertoire local

Ce contrôle détermine si la réécriture du mot de passe est activée pour ce répertoire. Si c’est le cas, il indique l’état du service de réécriture local. Cela peut être utile si vous voulez désactiver temporairement la réécriture du mot de passe sans devoir reconfigurer Azure AD Connect.

* Si le commutateur est défini sur **Oui**, la réécriture est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe peuvent réinitialiser leur mot de passe.
* Si le commutateur est défini sur **Non**, la réécriture est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne peuvent pas réinitialiser leur mot de passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe

Ce contrôle indique si les utilisateurs qui visitent le portail de réinitialisation de mot de passe doivent avoir la possibilité de déverrouiller leurs comptes Active Directory locaux sans devoir réinitialiser leur mot de passe. Par défaut, Azure AD déverrouille les comptes quand il effectue une réinitialisation de mot de passe. Ce paramètre vous permet de séparer ces deux opérations. 

* Si la valeur est **Oui**, les utilisateurs peuvent réinitialiser leur mot de passe et déverrouiller le compte ou déverrouiller celui-ci sans devoir réinitialiser le mot de passe.
* Si la valeur est **Non**, les utilisateurs doivent réinitialiser leur mot de passe quand ils déverrouillent leur compte.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Comment fonctionne la réinitialisation du mot de passe pour les utilisateurs B2B ?
La réinitialisation et la modification du mot de passe sont totalement prises en charge sur toutes les configurations B2B. La réinitialisation du mot de passe utilisateur B2B est prise en charge dans les trois cas suivants :

   * **Utilisateurs d’une organisation partenaire disposant d’un locataire Azure AD** : si l’organisation avec laquelle vous avez un partenariat dispose d’un locataire Azure AD, nous *respectons les stratégies de réinitialisation du mot de passe activées sur ce locataire*. Pour que la réinitialisation de mot de passe fonctionne, l’organisation partenaire doit simplement vérifier qu’Azure AD SSPR est activé. Aucun frais supplémentaire n’est appliqué pour les clients Office 365, et l’utilisateur peut activer cette fonctionnalité en suivant les étapes décrites dans notre guide [Bien démarrer avec la gestion des mots de passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
   * **Utilisateurs qui s’inscrivent par le biais de l’inscription libre-service** : si l’organisation avec laquelle vous avez un partenariat a utilisé la fonctionnalité [d’inscription en libre-service](active-directory-self-service-signup.md) pour accéder à un locataire, nous l’autorisons à réinitialiser le mot de passe en indiquant l’adresse e-mail qu’elle a utilisée pour l’inscription.
   * **Utilisateurs B2B** : tous les utilisateurs B2B créés à l’aide des nouvelles [fonctionnalités B2B d’Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) peuvent réinitialiser leur mot de passe en indiquant l’adresse e-mail utilisée pour l’inscription.

Pour tester ce scénario avec l’un des utilisateurs partenaires, consultez la page http://passwordreset.microsoftonline.com. Si ces utilisateurs disposent d’une autre adresse de messagerie ou d’un e-mail d’authentification, la réinitialisation du mot de passe fonctionne comme prévu.

> [!NOTE]
> Les comptes Microsoft qui disposent d’un accès invité à votre locataire Azure AD, tels que les comptes Hotmail.com, Outlook.com ou correspondant à d’autres adresses e-mail personnelles, ne peuvent pas utiliser Azure AD SSPR. Les utilisateurs de ces comptes doivent réinitialiser leur mot de passe à l’aide des informations fournies dans l’article [Lorsque vous n’avez pas accès à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD :

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialiser ou modifier votre mot de passe](active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"
[Writeback]: ./media/active-directory-passwords-how-it-works/troubleshoot-writeback-running.png "Intégration locale - Configuration de la réécriture de mot de passe et informations de dépannage"
