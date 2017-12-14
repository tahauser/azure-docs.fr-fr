---
title: "Didacticiel : Traitement des factures EDIFACT à l’aide d’Azure BizTalk Services | Microsoft Docs"
description: "Comment créer et configurer le connecteur Box ou une application API et l'utiliser dans une application logique d’Azure App Service"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 2ebd6a8cb70f218c3b56bc78c9b853dbf51ab468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Didacticiel : Processus de facturation EDIFACT à l’aide des Services BizTalk Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Vous pouvez utiliser le portail BizTalk Services pour configurer et déployer des accords X12 et EDIFACT. Dans ce didacticiel, nous allons étudier comment créer un accord EDIFACT destiné à l’échange de factures entre partenaires commerciaux. Ce didacticiel a été rédigé à partir d’une solution de bout en bout faisant intervenir deux partenaires commerciaux, Northwind et Contoso qui échangent des messages EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemple basé sur ce didacticiel
Ce didacticiel s’articule autour d’un exemple, **Envoi de factures EDIFACT à l’aide de BizTalk  Services**, qui est disponible au téléchargement depuis [MSDN Code Gallery](http://go.microsoft.com/fwlink/?LinkId=401005). Vous pouvez utiliser l’exemple et parcourir ce didacticiel pour comprendre comment l’exemple a été créé. Vous pouvez également utiliser ce didacticiel pour créer votre propre solution de A à Z. Ce didacticiel concerne la deuxième approche, afin que vous sachiez comment la solution a été créée. En outre, le plus souvent possible, ce didacticiel est cohérent avec l’exemple et utilise pour les artefacts (schémas et transformations, par exemple) les mêmes noms que ceux qui sont utilisés dans l’exemple.  

> [!NOTE]
> Étant donné que cette solution implique l’envoi d’un message d’un pont EAI vers un pont EDI, elle il réutilise le [l’exemple de chaînage du pont BizTalk Services](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  
> 
> 

## <a name="what-does-the-solution-do"></a>Que fait la solution ?
Dans cette solution, Northwind reçoit des factures EDIFACT de la part de Contoso. Ces factures ne sont pas dans un format EDI standard. Par conséquent, avant d’envoyer la facture à Northwind, elle doit être transformée en facture EDIFACT (également appelé INVOIC). À la réception, Northwind doit traiter la facture EDIFACT et renvoyer un message de contrôle (également appelé CONTRL) à Contoso.

![][1]  

Pour réaliser ce scénario d’entreprise, Contoso utilise les fonctionnalités fournies par Microsoft Azure BizTalk Services.

* Contoso utilise des ponts IAE pour convertir la facture d’origine en EDIFACT INVOIC.
* Le pont IAE envoie ensuite le message à un pont d’envoi EDI déployé dans le cadre d’un accord configuré dans le portail BizTalk Services.
* Le pont d’envoi EDI traite EDIFACT INVOIC et l’achemine vers Northwind.
* Après réception de la facture, Northwind renvoie un message CONTRL au pont de réception EDI déployé dans le cadre de l’accord.  

> [!NOTE]
> Cette solution explique également comment utiliser le traitement par lots pour envoyer les factures en lots, au lieu de les envoyer séparément.  
> 
> 

Pour achever le scénario, nous utilisons des files d’attente Service Bus pour envoyer la facture de Contoso à Northwind ou recevoir l’accusé de réception de la part de Northwind. Ces files d’attente peuvent être créées à l’aide d’une application cliente disponible au téléchargement incluse dans l’exemple de package disponible dans le cadre de ce didacticiel.  

## <a name="prerequisites"></a>Composants requis
* Vous devez disposer d’un espace de noms Azure Service Bus. Pour obtenir des instructions sur la création d’un espace de noms, consultez [Création ou modification d’un espace de noms de service Service Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supposons que vous disposez déjà d’un espace de noms Service Bus configuré appelé **edifactbts**.
* Vous devez posséder un abonnement BizTalk Services. Pour ce didacticiel, supposons que vous disposez d’un abonnement BizTalk Services, appelé **contosowabs**.
* Enregistrez votre abonnement BizTalk Services sur le portail BizTalk Services. Pour obtenir des instructions, consultez la page [Enregistrement d’un déploiement de BizTalk Services sur le portail BizTalk Services](https://msdn.microsoft.com/library/hh689837.aspx)
* Visual Studio doit être installé sur votre machine.
* Vous devez disposer du kit de développement logiciel BizTalk Services. Vous pouvez télécharger le kit de développement logiciel sur [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Étape 1 : créer les files d’attente Service Bus
Cette solution utilise des files d’attente Service Bus pour l’échange de messages entre partenaires commerciaux. Contoso et Northwind envoient aux files d’attente des messages qui sont ensuite utilisés par les ponts IAE et/ou EDI. Pour cette solution, vous avez besoin de trois files d’attente Service Bus :

* **northwindreceive** : c’est celle sur laquelle Northwind reçoit la facture de Contoso.
* **contosoreceive** : contoso reçoit l’accusé de réception de Northwind sur cette file d’attente.
* **suspendu** : c’est la file d’attente sur laquelle tous les messages suspendus sont acheminés. Les messages sont suspendus s’ils échouent en cours du traitement.

Vous pouvez créer ces files d’attente Service Bus à l’aide d’une application cliente incluse dans l’exemple de package.  

1. À partir de l’emplacement depuis lequel vous avez téléchargé l’exemple, ouvrez **Didacticiel d’envoi de factures à l’aide de BizTalk Services EDI Bridges.sln**.
2. Appuyez sur **F5** pour générer et lancer l’application **Client de didacticiel**.
3. Dans l’écran, saisissez l’espace de noms ACS Service Bus, le nom de l’émetteur et la clé de l’émetteur.
   
   ![][2]  
4. Un message vous informe que trois files d’attente vont être créées dans votre espace de noms Service Bus. Cliquez sur **OK**.
5. Quittez le client de didacticiel en cours d’exécution. Ouvrez, cliquez sur **Service Bus** > ***Votre espace de noms Service Bus*** > **Files d’attente** et vérifiez que les trois files d’attente ont bien été générées.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Étape 2 : créer et déployer l’accord de partenariat commercial
Créez un accord de partenariat commercial entre Contoso et Northwind. Un accord de partenariat commercial définit un contrat commercial entre les deux partenaires commerciaux, par exemple, le schéma de message et le protocole de messagerie à utiliser, etc. Un accord de partenariat commercial comprend deux ponts EDI, l’un servant à envoyer des messages à des partenaires commerciaux (appelé **pont d’envoi EDI**) et l’autre, à recevoir des messages de partenaires commerciaux (appelé **pont de réception EDI**).

Dans le cadre de cette solution, le pont d’envoi EDI correspond au côté envoi de l’accord et est utilisé pour envoyer la facture EDIFACT de Contoso à Northwind. De même, le pont de réception EDI est associé au côté réception de l’accord et est utilisé pour recevoir des accusés de réception de la part de Northwind.  

### <a name="create-the-trading-partners"></a>Créer des partenaires commerciaux
Commencez par créer des partenaires commerciaux pour Contoso et Northwind.  

1. Dans le portail BizTalk Services, sur l’onglet **Partenaires**, cliquez sur **Ajouter**.
2. Dans la page Nouveau partenaire, saisissez **Contoso** comme nom de partenaire, puis cliquez sur **Enregistrer**.
3. Répétez l’opération pour créer le deuxième partenaire, **Northwind**.  

### <a name="create-the-agreement"></a>Création de l’accord
Des accords de partenariat commercial sont créés entre les profils d’entreprise de partenaires commerciaux. Cette solution utilise les profils de partenaires par défaut créés automatiquement au moment de la création des partenaires.  

1. Dans le portail BizTalk Services, cliquez sur **Accords** > **Ajouter**.
2. Dans la page **Paramètres généraux** du nouvel accord, spécifiez les valeurs comme indiqué dans l’image ci-dessous, puis cliquez sur **Continuer**.
   
   ![][3]  
   
   Après avoir cliqué sur **Continuer**, deux onglets, **Paramètres de réception** et **Paramètres d’envoi** deviennent disponibles.
3. Créez l’accord d’envoi entre Contoso et Northwind. Ce contrat régit la façon dont Contoso envoie la facture EDIFACT à Northwind.
   
   1. Cliquez sur **Paramètres d’envoi**.
   2. Retenez les valeurs par défaut des onglets **URL entrante**, **Transformation** et **Traitement par lots**.
   3. Sur l’onglet **Protocole**, sous la section **Schémas**, chargez le schéma **EFACT_D93A_INVOIC.xsd**. Ce schéma est disponible avec l’exemple de package.
      
      ![][4]  
   4. Sur l’onglet **Transport** , spécifiez les détails pour les files d’attente Service Bus. Pour le côté envoi de l’accord, nous utilisons la file d’attente **northwindreceive** pour envoyer la facture EDIFACT à Northwind, et la file d’attente **Exécution suspendue** pour acheminer les messages ayant échoué en cours de traitement et suspendus. Vous avez créé ces files d’attente à l’ **Étape 1 : créer les files d’attente Service Bus** (dans cette rubrique).
      
      ![][5]  
      
      Sous **Paramètres de Transport > Type de Transport** et **Paramètres de Suspension de message > Type de Transport**, sélectionnez Azure Service Bus et fournissez les valeurs comme indiqué dans l’image.
4. Créez l’accord de réception entre Contoso et Northwind. Cet accord régit la façon dont Contoso reçoit l’accusé de réception de Northwind.
   
   1. Cliquez sur **Paramètres de réception**.
   2. Conservez les valeurs par défaut des onglets **Transport** et **Transformation**.
   3. Sur l’onglet **Protocole**, sous la section **Schémas**, chargez le schéma **EFACT_4.1_CONTRL.xsd**. Ce schéma est disponible avec l’exemple de package.
   4. Sur l’onglet **Itinéraire** , créez un filtre pour vous assurer que seuls les accusés de réception de Northwind sont acheminés vers Contoso. Sous **Paramètres d’itinéraire**, cliquez sur **Ajouter** pour créer le filtre de routage.
      
      ![][6]  
      
      1. Fournissez des valeurs pour **Nom de la règle**, **Règle de routage** et **Destination d’itinéraire** comme indiqué dans l’image.
      2. Cliquez sur **Save**.
   5. De retour sur l’onglet **Itinéraire** , spécifiez l’endroit vers lequel acheminer les accusés de réception suspendus (accusés de réception qui échouent en cours du traitement). Définissez le type de transport sur Azure Service Bus, le type de destination d’itinéraire sur **File d’attente**, le type d’authentification sur **Signature d’accès partagé** (SAS), fournissez la chaîne de connexion SAP pour l’espace de noms Service Bus, puis saisissez le nom de la file d’attente en tant que **Suspendu**.
5. Enfin, cliquez sur **Déployer** pour redéployer l’accord. Notez les points de terminaison sur lesquels les accords d’envoi et de réception sont déployés.
   
   * Sur l’onglet **Paramètres d’envoi**, sous **URL entrante**, notez le point de terminaison. Pour envoyer un message de Contoso vers Northwind avec le pont d’envoi EDI, vous devez envoyer un message à ce point de terminaison.
   * Sur l’onglet **Paramètres de réception**, sous **Transport**, notez le point de terminaison. Pour envoyer un message de Northwind vers Contoso avec le pont de réception EDI, vous devez recevoir un message à ce point de terminaison.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Étape 3 : créer et déployer le projet BizTalk Services
Au cours de l’étape précédente, vous avez déployé les accords d’envoi et de réception EDI pour traiter les accusés de réception et les factures EDIFACT. Ces accords peuvent également traiter des messages conformes au schéma de message EDIFACT standard. Toutefois, selon le scénario de cette solution, Contoso envoie à Northwind une facture respectant un schéma propriétaire interne. Donc, avant que le message soit envoyé vers le pont d’envoi EDI, il doit être converti au schéma de facture EDIFACT standard. C’est à ça que sert le projet IAE BizTalk Services.

Le projet BizTalk Services **InvoiceProcessingBridge**qui transforme le message fait lui aussi partie de l’exemple que vous avez téléchargé. Le projet inclut les artefacts suivants :

* **INHOUSEINVOICE. XSD** : schéma de la facture maison envoyée à Northwind.
* **EFACT_D93A_INVOIC. XSD** : schéma de la facture EDIFACT standard.
* **EFACT_4.1_CONTRL. XSD** : schéma de l’accusé de réception EDIFACT que Northwind envoie à Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** : conversion permettant de mapper le schéma de facture maison sur le schéma de facture EDIFACT standard.  

### <a name="create-the-biztalk-services-project"></a>Créer le projet BizTalk Services
1. Dans la solution Visual Studio, développez le projet InvoiceProcessingBridge, puis ouvrez le fichier **MessageFlowItinerary.bcs** .
2. Cliquez n’importe où dans la zone de dessin et définissez l’ **URL du Service BizTalk** dans la zone de propriété pour spécifier le nom de votre abonnement BizTalk Services. Par exemple : `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Dans la boîte à outils, faites glisser un **pont Xml unidirectionnel** sur la zone de dessin. Définissez les propriétés **Nom de l’entité** et **Adresse Relative** du pont **ProcessInvoiceBridge**. Double-cliquez sur **ProcessInvoiceBridge** pour ouvrir la surface de configuration de pont.
4. Dans la zone **Types de message**, cliquez sur le bouton plus (**+**) pour spécifier le schéma du message entrant. Le message entrant du pont IAE étant toujours une facture maison, affectez-lui la valeur **INHOUSEINVOICE**.
   
   ![][8]  
5. Cliquez sur la forme **transformation Xml** et dans la zone de propriété correspondant à la propriété **Cartes**, cliquez sur le bouton portant les points de suspension (**...**). Dans la boîte de dialogue **Sélection de correspondance**, sélectionnez le fichier de conversion **INHOUSEINVOICE_to_D93AINVOIC**, puis cliquez sur **OK**.
   
   ![][9]  
6. Revenez à **MessageFlowItinerary.bcs**, et depuis la boîte à outils, faites glisser un **point de terminaison de Service externe bidirectionnel** à droite du **ProcessInvoiceBridge**. Définissez la propriété **Nom de l’entité** sur **EDIBridge**.
7. Dans l’Explorateur de solutions, développez **MessageFlowItinerary.bcs** et double-cliquez sur le fichier **EDIBridge.config**. Remplacez le contenu du fichier **EDIBridge.config** par le code suivant.
   
   > [!NOTE]
   > Pourquoi dois-je modifier le fichier .config ? Le point de terminaison de service externe que nous avons ajouté à la zone de dessin du pont représente les ponts EDI que nous avons déployés précédemment. Les ponts EDI sont bidirectionnels et présentent un côté envoi et un côté réception. Toutefois, le pont IAE que nous avons ajouté au concepteur de pont est un pont unidirectionnel. Par conséquent, pour gérer les modèles d’échange de messages différents des deux ponts, nous utilisons un comportement de pont personnalisé en incluant sa configuration dans le fichier .config. En outre, le comportement personnalisé gère également l’authentification au niveau du point de terminaison du pont d’envoi EDI. Ce comportement personnalisé est disponible sous forme d’exemple distinct dans [l’exemple de chaînage de pont BizTalk Services - IAE à EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Cette solution réutilise l’exemple.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Mettez à jour le fichier EDIBridge.config pour y inclure les détails de la configuration
   
   * Sous *<behaviors>*, indiquez l’espace de nom ACS et la clé associée à l’abonnement BizTalk Services.
   * Sous *<client>*, indiquez le point de terminaison sur lequel l’accord d’envoi EDI est déployé.
   
   Enregistrez les modifications et fermez le fichier de configuration.
9. Dans la boîte à outils, cliquez sur **Connecteur** et associez les composants **ProcessInvoiceBridge** et **EDIBridge**. Sélectionnez le connecteur, puis, dans la zone Propriétés, définissez **Condition de filtre** sur **Toutes les correspondances**. Cela garantit que tous les messages traités par le pont IAE sont acheminés vers le pont EDI.
   
   ![][10]  
10. Enregistrez des modifications à la solution.  

### <a name="deploy-the-project"></a>Déployer le projet
1. Sur l’ordinateur dans lequel vous avez créé le projet BizTalk Services, téléchargez et installez le certificat SSL de votre abonnement BizTalk Services. Depuis BizTalk Services, cliquez sur le **Tableau de bord**, puis cliquez sur **Télécharger le certificat SSL**. Double-cliquez sur le certificat et suivez les instructions pour achever l’installation. Veillez à installer le certificat sous le magasin de certificats **Autorités de certification racines de confiance** .
2. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur le projet **InvoiceProcessingBridge**, puis cliquez sur **Déployer**.
3. Fournissez les valeurs comme indiqué dans l’image, puis cliquez sur **Déployer**. Vous pouvez obtenir les informations d’identification ACS pour BizTalk Services en cliquant sur **Informations de connexion** dans le tableau de bord BizTalk Services.
   
   ![][11]  
   
   Dans le panneau de sortie, copiez le point de terminaison sur lequel le pont IAE est déployé, par exemple, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Vous aurez besoin de cette URL de point de terminaison ultérieurement.  

## <a name="step-4-test-the-solution"></a>Étape 4 : tester la solution
Dans cette rubrique, nous allons étudier comment tester la solution à l’aide de l’application **Client de didacticiel** fournie dans le cadre de l’exemple.  

1. Dans Visual Studio, appuyez sur la touche F5 pour lancer le **Client du didacticiel**.
2. Suite à l’opération qui a créé les files d’attente Service Bus, les valeurs d’écran sont en principe pré-renseignées . Cliquez sur **Suivant**.
3. Dans la fenêtre suivante, fournissez les informations d’identification ACS correspondant à l’abonnement BizTalk Services et les points de terminaison où les ponts EAI et EDI (réception) sont déployés.
   
   Vous avez copié le point de terminaison du pont IAE à l’étape précédente. Pour le point de terminaison de pont de réception EDI dans le portail BizTalk Services, accédez à l’accord > Paramètres de réception > Transport > Point de terminaison.
   
   ![][12]  
4. Dans la fenêtre suivante, sous Contoso, cliquez sur le bouton **Envoyer la facture maison** . Dans la boîte de dialogue Fichier, ouvrez le fichier INHOUSEINVOICE.txt. Examinez le contenu du fichier, puis cliquez sur **OK** pour envoyer la facture.
   
   ![][13]  
5. Quelques secondes après, Northwind reçoit la facture. Cliquez sur le lien **Afficher le Message** pour afficher la facture reçue par Northwind. Notez que la facture reçue chez Northwind respecte le schéma EDIFACT standard, alors que celle qui a été envoyée par Contoso était obéissait à un format maison.
   
   ![][14]  
6. Sélectionnez la facture, puis cliquez sur **Envoyer un accusé de réception**. Dans la boîte de dialogue qui s’affiche, notez que l’ID d’échange présent sur la facture reçue est équivalent à celui qui figure sur l’accusé de réception envoyé. Cliquez sur OK dans la boîte de dialogue **Envoyer un accusé de réception** .
   
   ![][15]  
7. Quelques secondes après, Contoso reçoit l’accusé de réception.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Étape 5 (facultatif) : envoyer la facture EDIFACT en lots
Les ponts EDI BizTalk Services prennent également en charge le traitement par lots des messages sortants. Cette fonctionnalité est utile pour la réception de partenaires qui préfèrent recevoir un lot de messages (respectant certains critères) plutôt que des messages individuels.

L’aspect le plus important du travail avec des lots est la publication réelle du lot, également appelée critère de publication. Les critères de publication peuvent être déterminés en fonction de la façon dont les partenaires de réception veulent recevoir leurs messages. Si le traitement par lots est activé, le pont EDI n’envoie pas de message sortant vers le partenaire de réception jusqu’à ce que les critères de déclenchement soient atteints. Par exemple, un critère de traitement par lot basé sur la taille des messages distribue un lot uniquement lorsque « n » messages sont regroupés. Un critère de traitement par lots peut également être basé sur le temps, et faire en sorte qu’un lot soit envoyé à heure fixe chaque jour. Dans cette solution, nous essayons des critères basés sur la taille des messages.

1. Dans le portail BizTalk Services, cliquez sur l’accord que vous avez créé précédemment. Cliquez sur Paramètres d’envoi > Traitement par lot > Ajouter un lot.
2. Comme nom de lot, saisissez **InvoiceBatch**, fournissez une description, puis cliquez sur **Suivant**.
3. Spécifiez des critères de lancement de lots qui définissent les messages devant être regroupés dans lots. Dans cette solution, nous allons regrouper les messages par lot. Alors, sélectionnez l’option Utilisation de définitions avancées, et saisissez **1 = 1**. Il s’agit d’une condition qui conservera toujours la valeur true, et par conséquent, tous les messages seront traités par lot. Cliquez sur **Suivant**.
   
   ![][17]  
4. Spécifiez un critère de lancement par lot. Dans la zone de liste déroulante, sélectionnez **MessageCountBased** et attribuez à **Nombre** la valeur **3**. Cela signifie qu’un lot de trois messages sera envoyé à Northwind. Cliquez sur **Suivant**.
   
   ![][18]  
5. Consultez le résumé, puis cliquez sur **Enregistrer**. Cliquez sur **Déployer** pour redéployer l’accord.
6. Revenez au **client du didacticiel**, cliquez sur **Envoyer la facture maison** et suivez les invites pour envoyer la facture. Vous remarquerez qu’aucune facture n’est reçue par Northwind, car la taille de lot n’est pas atteinte. Répétez cette étape encore deux fois afin d’avoir trois messages de facturation envoyés à Northwind. Cela satisfait les critères de lancement par lot de 3 messages et vous devez maintenant voir une facture chez Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

