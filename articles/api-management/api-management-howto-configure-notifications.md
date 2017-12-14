---
title: "Configurer des notifications et des modèles d’e-mails dans Gestion des API Azure | Microsoft Docs"
description: "Apprenez à configurer des notifications et des modèles de messages électroniques dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ec560bbab3caf4cde090ed3c9a47ccc0afcb2492
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Configuration des notifications et des modèles de messages électroniques dans Gestion des API Azure
Gestion des API Azure permet de configurer les notifications pour des événements spécifiques et de configurer les modèles de courrier électronique utilisés pour communiquer avec les administrateurs et les développeurs de l’instance Gestion des API. Cette rubrique vous présente comment configurer les notifications pour les événements disponibles. Elle offre également un aperçu de la configuration des modèles de messages électroniques utilisés pour ces événements.

## <a name="publisher-notifications"></a>Configuration des notifications de l’éditeur
Pour configurer les notifications, cliquez sur **Portail de publication** dans le portail Azure pour accéder à votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

> [!NOTE] 
> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].

Cliquez sur **Notifications** dans le menu **Gestion des API** à gauche pour voir les notifications disponibles.

![Notifications de l’éditeur][api-management-publisher-notifications]

La liste suivante répertorie les événements pour lesquels il est possible de configurer des notifications.

* **Demandes d'abonnement (approbation nécessaire)** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification pour les demandes d'abonnement aux produits API nécessitant une approbation.
* **Nouveaux abonnements** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification concernant les nouveaux abonnements aux produits API.
* **Demandes de la galerie d'applications** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsque de nouvelles applications sont proposées dans la galerie d'applications.
* **CCI** : les destinataires du message et les utilisateurs spécifiés reçoivent une copie cachée de tous les messages envoyés aux développeurs.
* **Nouveau problème ou commentaire** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsqu'un problème ou un commentaire est envoyé sur le portail des développeurs.
* **Message de fermeture de compte** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsqu'un compte est fermé.
* **Limite du quota d'abonnements bientôt atteint** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsque l'utilisation de l'abonnement approche le quota.

Pour chaque événement, vous pouvez spécifier les destinataires du message via la zone de texte d'adresse. Vous pouvez également sélectionner les utilisateurs dans une liste.

Pour spécifier les adresses à notifier, entrez-les dans la zone de texte d'adresse. Si vous devez entrer plusieurs adresses, séparez-les par des virgules.

![Notification recipients][api-management-email-addresses]

Pour spécifier les utilisateurs à notifier, cliquez sur **Ajouter un destinataire**, activez la case à cocher à côté de l’utilisateur à notifier, puis cliquez sur **OK**.

> [!NOTE] 
> Seuls les administrateurs sont affichés dans la liste.


Une fois les destinataires de la notification configurés, cliquez sur **Enregistrer** pour appliquer les modifications des destinataires.

> [!NOTE] 
> Si vous quittez l’onglet **Notifications de l’éditeur** , le portail de publication vous avertit si des modifications n’ont pas été enregistrées.


## <a name="email-templates"></a>Configuration des modèles de courrier électronique
L'outil Gestion des API contient des modèles de messages électroniques pour les messages envoyés dans le cadre de l'administration et de l'utilisation du service. Les modèles suivants sont fournis.

* Demande d'ajout à la galerie d'applications approuvée
* Lettre d'adieu au développeur
* Notification de la limite du quota d'abonnement bientôt atteinte
* Invitation de l'utilisateur
* Nouveau commentaire ajouté à un problème
* Nouveau problème reçu
* Nouvel abonnement activé
* Confirmation du renouvellement de l'abonnement
* Refus de la demande d'abonnement
* Réception de la demande d'abonnement

Ces modèles peuvent être modifiés comme vous le souhaitez.

Pour voir et configurer les modèles de messages électroniques de votre instance Gestion des API, cliquez sur **Notifications** dans le menu **Gestion des API** à gauche, puis sélectionnez l’onglet **Modèles de messages électroniques**.

![Email templates][api-management-email-templates]

Pour consulter ou modifier un modèle en particulier, sélectionnez-le dans la liste déroulante **Modèles** .

![Email templates list][api-management-email-templates-list]

Pour chaque modèle de message, l'objet est au format texte et le corps au format HTML. Chaque élément peut être personnalisé.

![Email template editor][api-management-email-template]

La liste **Paramètres** contient une liste de paramètres qui, lorsqu'ils sont insérés dans l'objet ou dans le corps du message, sont remplacés par une valeur définie lorsque le message est envoyé. Pour insérer un paramètre, placez le curseur là où vous voulez insérer le paramètre, puis cliquez sur la flèche à gauche du nom du paramètre.

Cliquez sur **Aperçu** ou **Envoyer un test** pour voir à quoi ressemble le message ou bien pour envoyer un message test.

> [!NOTE] 
> Les paramètres ne sont pas remplacés par les valeurs réelles lors de l’aperçu ou du test.

Pour enregistrer les modifications apportées au modèle de message, cliquez sur **Enregistrer**. Pour annuler les modifications, cliquez sur **Annuler**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
