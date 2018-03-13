---
title: "Messages X12 pour l’intégration d’entreprise B2B - Azure Logic Apps | Microsoft Docs"
description: "Échangez des messages X12 au format EDI dans le cadre d’une intégration d’entreprise B2B avec Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: c644dd98d468a8c99625c45bad3f06031ff22b4e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Échangez des messages X12 dans le cadre d’une intégration d’entreprise avec Logic Apps

Avant de pouvoir échanger des messages X12 pour Azure Logic Apps, vous devez créer un contrat X12 et le stocker dans votre compte d’intégration. Voici les étapes de création d’un contrat X12.

> [!NOTE]
> Cette page traite des fonctionnalités X12 d’Azure Logic Apps. Pour plus d’informations, consultez [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure
* Au moins deux [partenaires](../logic-apps/logic-apps-enterprise-integration-partners.md) définis dans votre compte d’intégration et configurés avec l’identifiant X12 sous **Identités d’entreprise**    
* Un [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) obligatoire que vous pouvez charger dans votre compte d’intégration

Une fois que vous avez [créé un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [ajouté des partenaires](logic-apps-enterprise-integration-partners.md) et défini un [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) à utiliser, vous pouvez créer un contrat X12 en procédant comme suit.

## <a name="create-an-x12-agreement"></a>Créer un contrat X12

1. Connectez-vous au [portail Azure](http://portal.azure.com "portail Azure"). 

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « intégration », puis sélectionnez **Comptes d’intégration**.  

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Si l’option **Tous les services** n’est pas visible, vous devez peut-être d’abord développer le menu. En haut du menu réduit, sélectionnez **Afficher le menu**.

3. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter le contrat.

   ![Sélectionnez le compte d’intégration dans lequel vous souhaitez créer le contrat](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Sélectionnez **Vue d’ensemble**, puis cliquez sur la mosaïque **Contrats**. Si vous ne voyez pas la mosaïque Contrats, commencez par ajouter la mosaïque. 

   ![Choisissez la mosaïque « Contrats »](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. Sous **Contrats**, choisissez **Ajouter**.

   ![Choisir « Ajouter »](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. Sous **Ajouter**, entrez le **nom** de votre contrat. Pour le type de contrat, sélectionnez **X12**. Sélectionnez le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité** et **l’identité de l’invité** pour votre contrat. Pour plus de détails sur les propriétés, consultez le tableau de cette étape.

    ![Renseigner les détails relatifs au contrat](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriété | Description |
    | --- | --- |
    | NOM |Nom du contrat. |
    | Type de contrat | Doit être X12. |
    | Partenaire hôte |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat. |
    | Identité de l’hôte |Identificateur du partenaire hôte. |
    | Partenaire invité |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
    | identité de l’invité |Identificateur du partenaire invité. |
    | Paramètres de réception |Ces propriétés s’appliquent à tous les messages reçus par un contrat. |
    | Paramètres d’envoi |Ces propriétés s’appliquent à tous les messages envoyés par un contrat. |  

  > [!NOTE]
  > La résolution du contrat X12 dépend de la mise en correspondance des qualificateurs et des identificateurs de l’expéditeur et du destinataire définis dans le partenaire et le message entrant. Si ces valeurs changent pour votre partenaire, mettez également à jour le contrat.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configuration du traitement des messages reçus

Maintenant que vous avez défini les propriétés du contrat, vous pouvez configurer la manière dont ce contrat identifie et traite les messages entrants reçus par votre partenaire par l’intermédiaire de ce contrat.

1.  Sous **Ajouter**, sélectionnez **Paramètres de réception**.
Configurez ces propriétés selon le contrat conclu avec le partenaire qui échange des messages avec vous. Pour obtenir les descriptions des propriétés, consultez les tableaux de cette section.

    L’option **Paramètres de réception** est organisée en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, validations et paramètres internes.

2. Une fois que vous avez terminé, cliquez sur **OK** pour enregistrer vos paramètres.

Votre contrat est maintenant prêt à traiter les messages entrants qui sont conformes aux paramètres que vous avez sélectionnés.

### <a name="identifiers"></a>Identificateurs

![Définir les propriétés de l’identificateur](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriété | Description |
| --- | --- |
| ISA1 (qualificateur d’autorisation) |Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation. |
| ISA2 |facultatif. Entrez la valeur d’autorisation. Si la valeur entrée pour ISA1 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |
| ISA3 (qualificateur de sécurité) |Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité. |
| ISA4 |facultatif. Entrez la valeur de sécurité. Si la valeur entrée pour ISA3 est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |

### <a name="acknowledgment"></a>Accusé de réception

![Définir les propriétés de l’accusé de réception](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriété | Description |
| --- | --- |
| TA1 attendu |Renvoie un accusé de réception technique à l’expéditeur de l’échange. |
| FA attendu |Renvoie un accusé de réception fonctionnel à l’expéditeur de l’échange. Indiquez ensuite si vous souhaitez utiliser les accusés de réception 997 ou 999, selon la version du schéma. |
| Inclure une boucle AK2/IK2 |Permet la génération de boucles AK2 dans les accusés de réception fonctionnels des documents informatisés acceptés. |

### <a name="schemas"></a>Schémas

Sélectionnez un schéma pour chaque type de transaction (ST1) et application de l’expéditeur (GS2). Le pipeline de réception désassemble le message entrant en faisant correspondre les valeurs ST1 et GS2 dans le message entrant avec les valeurs que vous définissez ici, et le schéma du message entrant avec le schéma que vous définissez ici.

![Sélectionner un schéma](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriété | Description |
| --- | --- |
| Version |Sélectionnez la version X12 |
| Type de transaction (ST01) |Sélectionnez le type de transaction |
| Application de l’expéditeur (GS02) |Sélectionnez l’application de l’expéditeur |
| Schéma |Sélectionnez le fichier de schéma que vous souhaitez utiliser. Les schémas sont ajoutés à votre compte d’intégration. |

> [!NOTE]
> Configurez le [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) requis qui est chargé dans votre [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Enveloppes

![Spécifiez le séparateur dans un document informatisé : choisissez l’identificateur standard ou le séparateur de répétition](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriété | Description |
| --- | --- |
| Utilisation d’ISA11 |Spécifie le séparateur à utiliser dans un document informatisé : <p>Sélectionnez **Identificateur standard** pour utiliser un point (.) pour la notation décimale, au lieu de la notation décimale du document entrant dans le pipeline de réception EDI. <p>Sélectionnez **Séparateur de répétition** pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, l’accent circonflexe (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser l’accent circonflexe. |

### <a name="control-numbers"></a>Numéros de contrôle

![Sélectionnez le mode de traitement des doublons de numéros de contrôle](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriété | Description |
| --- | --- |
| Interdire les doublons de numéro de contrôle d’échange |Bloquer les échanges en double. Vérifie le numéro de contrôle d’échange (ISA13) du numéro de contrôle de l’échange reçu. Si une correspondance est détectée, le pipeline de réception ne traite pas l’échange. Vous pouvez spécifier la fréquence en nombre de jours à laquelle le contrôle est effectué en définissant la valeur appropriée dans le champ *Vérifier les doublons ISA13 tous les (jours)*. |
| Interdire les numéros de contrôle de groupe en double |Bloquer les échanges avec des numéros de contrôle de groupe en double. |
| Interdire les numéros de contrôle de document informatisé en double |Bloquer les échanges avec des numéros de contrôle de document informatisé en double. |

### <a name="validations"></a>Validations

![Définir les propriétés de validation pour les messages reçus](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Une nouvelle ligne de validation est automatiquement ajoutée dès que la ligne précédente est terminée. Si vous ne spécifiez aucune règle, la validation utilise la ligne « Par défaut ».

| Propriété | Description |
| --- | --- |
| Type de message |Sélectionnez le type de message EDI. |
| Validation EDI |Effectuez une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| Validation étendue |Si le type de données n’est pas EDI, la validation s’effectue sur la spécification d’élément de données et la répétition autorisée, les énumérations et la validation de la longueur d’élément de données (min/max). |
| Autoriser les zéros de début ou de fin |Conservez les zéros de début ou de fin ainsi que les caractères d’espace supplémentaires. Ne supprimez pas ces caractères. |
| Supprimer les zéros de début ou de fin |Supprimez les zéros de début ou de fin ainsi que les caractères d’espace. |
| Stratégie de séparateur de fin |Générez des séparateurs de fin. <p>Sélectionnez **Non autorisé** pour interdire les délimiteurs et les séparateurs dans l’échange reçu. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide. <p>Sélectionnez **Facultatif** pour accepter les échanges avec ou sans délimiteurs et séparateurs. <p>Sélectionnez **Obligatoire** lorsque l’échange reçu doit contenir des délimiteurs et des séparateurs. |

### <a name="internal-settings"></a>Paramètres internes

![Sélectionnez les paramètres internes](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriété | Description |
| --- | --- |
| Convertir le format décimal implicite « Nn » en valeur numérique de base 10 |Convertit un numéro EDI spécifié au format « Nn » en une valeur numérique de base 10 |
| Créer des balises XML vides si les séparateurs de fin sont autorisés |Cochez cette case afin d’inclure des balises XML vides pour les séparateurs de l’expéditeur. |
| Scinder l’échange en documents informatisés : suspendre les documents informatisés en cas d’erreur|Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée au document informatisé. Suspend uniquement les transactions dont la validation échoue. |
| Scinder l’échange en documents informatisés : suspendre l’échange en cas d’erreur|Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Suspend l’intégralité de l’échange lorsque la validation d’un ou plusieurs documents informatisés de l’échange échoue. | 
| Préserver l'échange : suspendre les documents informatisés en cas d’erreur |Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot. Suspend uniquement les documents informatisés dont la validation échoue, tout en continuant à traiter tous les autres documents informatisés. |
| Préserver l’échange : suspendre l’échange en cas d’erreur |Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot. Suspend l’intégralité de l’échange lorsque la validation d’un ou plusieurs documents informatisés de l’échange échoue. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configuration de l’envoi des messages

Vous pouvez configurer la manière dont votre contrat identifie et traite les messages sortants que vous envoyez à votre partenaire par l’intermédiaire de ce contrat.

1.  Sous **Ajouter**, sélectionnez **Paramètres d’envoi**.
Configurez ces propriétés selon le contrat conclu avec le partenaire qui échange des messages avec vous. Pour obtenir les descriptions des propriétés, consultez les tableaux de cette section.

    L’option **Paramètres d’envoi** est organisée en plusieurs sections : identificateurs, accusé de réception, schémas, enveloppes, jeux de caractères et séparateurs, numéros de contrôle et validation.

2. Une fois que vous avez terminé, cliquez sur **OK** pour enregistrer vos paramètres.

Votre contrat est maintenant prêt à traiter les messages sortants qui sont conformes aux paramètres que vous avez sélectionnés.

### <a name="identifiers"></a>Identificateurs

![Définir les propriétés de l’identificateur](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriété | Description |
| --- | --- |
| Qualificateur d’autorisation (ISA1) |Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation. |
| ISA2 |Entrez la valeur d’autorisation. Si cette valeur est autre que 00, entrez au moins un caractère alphanumérique (et 10 caractères maximum). |
| Qualificateur de sécurité (ISA3) |Dans la liste déroulante, sélectionnez la valeur du qualificateur de sécurité. |
| ISA4 |Entrez la valeur de sécurité. Si cette valeur est autre que 00, dans la zone de texte Valeur (ISA4), entrez au moins un caractère alphanumérique (et 10 caractères maximum). |

### <a name="acknowledgment"></a>Accusé de réception

![Définir les propriétés de l’accusé de réception](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriété | Description |
| --- | --- |
| TA1 attendu |Renvoie un accusé de réception technique (TA1) à l’expéditeur de l’échange. Ce paramètre spécifie que le partenaire hôte qui envoie le message demande un accusé de réception de la part du partenaire invité figurant dans le contrat. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat. |
| FA attendu |Renvoie un accusé de réception fonctionnel (FA) à l’expéditeur de l’échange. Indiquez si vous souhaitez utiliser les accusés de réception 997 ou 999, selon les versions de schéma sur lesquelles vous travaillez. Ces accusés de réception sont attendus par le partenaire hôte en fonction des paramètres de réception du contrat. |
| Version FA |Sélectionnez la version FA |

### <a name="schemas"></a>Schémas

![Sélectionnez le schéma à utiliser](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriété | Description |
| --- | --- |
| Version |Sélectionnez la version X12 |
| Type de transaction (ST01) |Sélectionnez le type de transaction |
| Schéma |Sélectionnez le schéma à utiliser. Les schémas se trouvent dans votre compte d’intégration. Si vous sélectionnez schéma d’abord, il configure automatiquement la version et le type de transaction.  |

> [!NOTE]
> Configurez le [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) requis qui est chargé dans votre [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Enveloppes

![Spécifiez le séparateur dans un document informatisé : choisissez l’identificateur standard ou le séparateur de répétition](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriété | Description |
| --- | --- |
| Utilisation d’ISA11 |Spécifie le séparateur à utiliser dans un document informatisé : <p>Sélectionnez **Identificateur standard** pour utiliser un point (.) pour la notation décimale, au lieu de la notation décimale du document entrant dans le pipeline de réception EDI. <p>Sélectionnez **Séparateur de répétition** pour spécifier le séparateur des occurrences répétées d’un élément de données simple ou une structure de données répétées. Par exemple, l’accent circonflexe (^) est généralement utilisé comme séparateur de répétition. Pour les schémas HIPAA, vous pouvez uniquement utiliser l’accent circonflexe. |

### <a name="control-numbers"></a>Numéros de contrôle

![Spécifier les propriétés de numéro de contrôle](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriété | Description |
| --- | --- |
| Numéro de contrôle de version (ISA12) |Sélectionnez la version de la X12 standard. |
| Indicateur d’utilisation (ISA15) |Sélectionnez le contexte d’un échange.  Les valeurs sont des informations, des données de production ou des données de test. |
| Schéma |Génère les segments GS et ST pour un échange X12 encodé qu’il envoie au pipeline d’envoi. |
| GS1 |Facultatif, sélectionnez une valeur pour le code fonctionnel dans la liste déroulante. |
| GS2 |Facultatif, expéditeur de l’application. |
| GS3 |Facultatif, destinataire de l’application. |
| GS4 |Facultatif, sélectionnez SSAAMMJJ ou AAMMJJ. |
| GS5 |Facultatif, sélectionnez HHMM, HHMMSS ou HHMMSSjj. |
| GS7 |Facultatif, sélectionnez une valeur pour l’agence responsable dans la liste déroulante. |
| GS8 |Facultatif, version du document. |
| Numéro de contrôle de l’échange (ISA13) |Obligatoire, entrez une plage de valeurs pour le numéro de contrôle de l’échange. Entrez une valeur numérique comprise entre 1 et 999999999. |
| Numéro de contrôle de groupe (GS06) |Obligatoire, entrez une plage de nombres pour le numéro de contrôle du groupe. Entrez une valeur numérique comprise entre 1 et 999999999. |
| Numéro de contrôle de document informatisé (ST02) |Obligatoire, entrez une plage de nombres pour le numéro de contrôle de document informatisé. Entrez une plage de valeurs numériques comprises entre 1 et 999999999. |
| Préfixe |Facultatif, désigné pour la plage de numéros de contrôle de documents informatisés utilisés dans l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle. |
| Suffixe |Facultatif, désigné pour la plage de numéros de contrôle de documents informatisés utilisés dans l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont requis et contiennent les valeurs minimales et maximales pour le numéro de contrôle. |

### <a name="character-sets-and-separators"></a>Jeux de caractères et séparateurs

Outre le jeu de caractères, vous pouvez entrer un autre ensemble de délimiteurs pour chaque type de message. Si aucun jeu de caractères n’est spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

![Spécifier des délimiteurs pour les types de messages](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriété | Description |
| --- | --- |
| Jeu de caractères à utiliser |Sélectionnez le jeu de caractères X12 pour valider les propriétés. Options : De base, Étendu et UTF-8. |
| Schéma |Dans la liste déroulante, sélectionnez un schéma. Une nouvelle ligne est automatiquement ajoutée lorsque la ligne précédente est terminée. Pour le schéma sélectionné, sélectionnez le jeu de séparateurs à utiliser en fonction de leur description ci-dessous. |
| Type d’entrée |Dans la liste déroulante, sélectionnez un type d’entrée. |
| Séparateur de composants |Entrez un caractère unique pour séparer les éléments de données composites. |
| Séparateur d'éléments de données |Entrez un caractère unique pour séparer les éléments de données simples au sein des éléments de données composites. |
| Caractère de remplacement |Entrez un caractère de remplacement pour remplacer tous les caractères de séparation dans les données de charge utile lors de la génération du message X12 sortant. |
| Terminateur de segment |Entrez un caractère unique pour indiquer la fin d’un segment EDI. |
| Suffixe |Sélectionnez le caractère utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est laissé vide, vous devez désigner un suffixe. |

> [!TIP]
> Pour fournir des valeurs de caractère spécial, modifiez l’accord au format JSON et fournissez la valeur ASCII du caractère spécial.

### <a name="validation"></a>Validation

![Définir les propriétés de validation pour l’envoi de messages](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Une nouvelle ligne de validation est automatiquement ajoutée dès que la ligne précédente est terminée. Si vous ne spécifiez aucune règle, la validation utilise la ligne « Par défaut ».

| Propriété | Description |
| --- | --- |
| Type de message |Sélectionnez le type de message EDI. |
| Validation EDI |Effectuez une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| Validation étendue |Si le type de données n’est pas EDI, la validation s’effectue sur la spécification d’élément de données et la répétition autorisée, les énumérations et la validation de la longueur d’élément de données (min/max). |
| Autoriser les zéros de début ou de fin |Conservez les zéros de début ou de fin ainsi que les caractères d’espace supplémentaires. Ne supprimez pas ces caractères. |
| Supprimer les zéros de début ou de fin |Supprimez les zéros de début ou de fin. |
| Stratégie de séparateur de fin |Générez des séparateurs de fin. <p>Sélectionnez **Non autorisé** pour interdire les délimiteurs et les séparateurs dans l’échange envoyé. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide. <p>Sélectionnez **Facultatif** pour envoyer des échanges avec ou sans délimiteurs et séparateurs. <p>Sélectionnez **Obligatoire** si l’échange envoyé doit contenir des délimiteurs et des séparateurs. |

## <a name="find-your-created-agreement"></a>Comment retrouver le contrat que vous avez créé

1.  Après avoir défini toutes les propriétés de votre contrat, dans la page **Ajouter**, choisissez **OK** pour finaliser la création de votre contrat et revenir à votre compte d’intégration.

    Le contrat que vous venez d’ajouter s’affiche dans votre liste **Contrats**.

2.  Vous pouvez également afficher vos contrats dans la vue d’ensemble de votre compte d’intégration. Dans le menu de votre compte d’intégration, choisissez **Vue d’ensemble**, puis sélectionnez la mosaïque **Contrats**.

    ![Choisissez la mosaïque « Contrats »](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Afficher le swagger
Consultez les [détails sur Swagger](/connectors/x12/). 

## <a name="learn-more"></a>En savoir plus
* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  

