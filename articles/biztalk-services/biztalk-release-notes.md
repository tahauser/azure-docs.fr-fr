---
title: "Notes de publication pour Azure BizTalk Services | Microsoft Docs"
description: "Répertorie les problèmes connus pour Azure BizTalk Services"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 0f3662ed386c3a3d2d132a483e7192bddc26de06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Notes de publication pour Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Les notes de version des Services Microsoft Azure BizTalk reprennent les problèmes connus dans cette version.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Nouveautés de la mise à jour BizTalk Services de novembre
* Le chiffrement au repos peut être activé dans le portail BizTalk Services. Consultez [Activer le chiffrement au repos dans le portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Historique de mise à jour
### <a name="october-update"></a>Mise à jour d’octobre
* Les comptes de société sont pris en charge :  
  * **Scénario** : vous avez inscrit un déploiement de service BizTalk à l’aide d’un compte Microsoft (par exemple, user@live.com). Dans ce scénario, seuls des utilisateurs de compte Microsoft peuvent gérer le service BizTalk au moyen du portail BizTalk Services. Il est impossible d’utiliser un compte professionnel.  
  * **Scénario** : vous avez inscrit un déploiement de service BizTalk à l’aide d’un compte professionnel dans Azure Active Directory (par exemple user@fabrikam.com ou user@contoso.com). Dans ce scénario, seuls des utilisateurs Azure Active Directory au sein de la même organisation peuvent gérer le service BizTalk par le biais du portail BizTalk Services. Il est impossible d’utiliser un compte Microsoft.  
* Lorsque vous créez un service BizTalk, vous êtes automatiquement inscrit dans le portail BizTalk Services.
  * **Scénario** : vous vous connectez à Azure, créez un service BizTalk, puis sélectionnez **Gérer** pour la première fois. Lorsque le portail BizTalk Services s’ouvre, le service BizTalk est automatiquement inscrit et est prêt pour vos déploiements.  
    Consultez [Enregistrement et mise à niveau d’un déploiement de service BizTalk sur le portail BizTalk Services](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Mise à jour du 14 août
* Découplage accord et pont : les accords et les ponts entre partenaires commerciaux sont désormais découplés dans le portail BizTalk Services. Vous pouvez maintenant créer des contrats et des ponts séparément, et, au moment de l’exécution, les ponts sont associés à un accord selon les valeurs présentes dans le message EDI. Consultez [Créer des accords dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Création d’un pont EDI à l’aide du portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Création d’un pont AS2 à l’aide du portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn793993.aspx) et [Comment les ponts résolvent-ils les accords au moment de l’exécution ?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* L’option permettant de créer des modèles pour les accords est abandonnée.  
* Pour le côté envoi de l’accord, vous pouvez maintenant spécifier différents ensembles de délimiteurs pour chaque schéma. Cette configuration est spécifiée dans les paramètres de protocole pour le côté envoi de l’accord. Pour plus d’informations, consultez [Création d’un accord X12 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) et [Création d’un accord EDIFACT dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Deux nouvelles entités sont également ajoutées à l’API TPM OM dans le même but. Consultez [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) et [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Les constructions XSD standard, et notamment les types dérivés, sont désormais prises en charge. Consultez [Utilisation de constructions XSD standard dans vos mappages](https://msdn.microsoft.com/library/azure/dn793987.aspx) et [Scénarios et exemples de mappage de type dérivé](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 prend en charge les nouveaux algorithmes MIC pour la signature des messages et de nouveaux algorithmes de chiffrement. Voir [Créer un accord AS2 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Problèmes connus

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problèmes de connectivité après la mise à jour du portail BizTalk Services
  Si le portail BizTalk Services est ouvert pendant une mise à jour de Services BizTalk en vue du déploiement de modifications dans le service, vous pouvez rencontrer des problèmes de connectivité avec le portail BizTalk Services.  
  Pour résoudre ce problème, vous pouvez redémarrer le navigateur, supprimer le cache du navigateur ou démarrer le portail en mode privé.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>L’IDE Visual Studio ne peut pas localiser l’artefact si vous cliquez sur une erreur ou sur un avertissement dans un projet BizTalk Services
Installez Visual Studio 2012 Update 3 RC 1 pour résoudre le problème.  

### <a name="custom-binding-project-reference"></a>Référence de projet de liaison personnalisée
Considérez les situations suivantes avec un projet BizTalk Services dans une solution Visual Studio :  

* Dans la même solution Visual Studio, il existe un projet BizTalk Services et un projet de liaison personnalisée. Le projet BizTalk Services comporte une référence à ce fichier de projet de liaison personnalisée.
* Le projet BizTalk Services comporte une référence à une DLL de liaison/comportement personnalisée.

Vous « générez » la solution dans Visual Studio avec succès. Ensuite, vous « régénérez » ou « Nettoyez » la solution. Après cela, lorsque vous réexécutez une régénération ou un nettoyage, l’erreur qui suit se produit :  
  Impossible de copier le fichier <Path to DLL> dans « bin\Debug\NomFichier.dll ». Le processus ne peut pas accéder au fichier « bin\Debug\FileName.dll », car il est utilisé par un autre processus.  

#### <a name="workaround"></a>Solution de contournement
* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) est installé, vous disposez des deux options suivantes :
  
  * Redémarrer Visual Studio ou
  * Redémarrer la solution. Effectuez ensuite une seule compilation de la solution.  
* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) n’est pas installé, ouvrez le Gestionnaire des tâches, cliquez sur l’onglet Processus et cliquez sur le processus MSBuild.exe, puis cliquez sur le bouton Terminer le processus.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>L’acheminement vers les points de terminaison BasicHttpRelay n’est pas pris en charge à partir de ponts ou du portail BizTalk Services si les caractères non imprimables sont utilisés en tant qu’en-têtes HTTP
Si vous utilisez des caractères non imprimables dans le cadre des propriétés promues des messages, ces messages ne peuvent pas être acheminés vers les destinations de relais qui utilisent la liaison BasicHttpRelay. En outre, les propriétés promues disponibles dans le cadre du suivi sont codées URL pour les objets blob et non codés pour les destinations.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN est envoyé de manière asynchrone même si l’option Envoi MDN asynchrone est désactivée
Imaginez le scénario suivant : si vous cochez la case **Envoyer un MDN asynchrone** et spécifiez une URL pour y envoyer le MDN asynchrone, puis décochez de nouveau la case **Envoyer un MDN asynchrone**, le MDN est toujours envoyé vers l’URL spécifiée, même si l’option d’envoi du MDN asynchrone n’est pas sélectionnée.  
Pour résoudre ce problème, vous devez effacer l’URL spécifiée avant de décocher la case **Envoyer un MDN asynchrone**, puis déployer le contrat AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Les espaces situés au-delà d’un échange valide génèrent un message vide à envoyer au point de terminaison d’interruption
S’il existe des espaces au-delà d’un segment IEA, le désassembleur les traite comme s’il s’agissait de la fin de l’échange actuel et examine l’ensemble d’espaces suivant comme un nouveau message. Comme il ne s’agit pas d’un échange valide, vous pouvez constater qu’un message de réussite est envoyé à la destination d’itinéraire et un message vide est envoyé au point de terminaison d’interruption.  

### <a name="tracking-in-biztalk-services-portal"></a>Suivi dans le portail Azure BizTalk Services
Les événements de suivi sont enregistrés jusqu’au traitement de messages EDI et une corrélation. Si un message échoue en dehors de la phase de protocole, le suivi sera affiché comme réussi. Dans ce cas, reportez-vous à la section JOURNAL de la colonne **Détails** dans **Suivi** pour connaître les détails de l’erreur.
Les paramètres de réception et d’envoi X 12 ([Créer un accord X12 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fournissent des informations sur l’étape de protocole.  

### <a name="update-agreement"></a>Accord de mise à jour
En cas de configuration d’un accord, le portail BizTalk Services vous permet de modifier le qualificateur d’une identité. Cela peut entraîner des propriétés incohérentes. Par exemple, il existe un accord utilisant ZZ:1234567 et le qualificateur ZZ:7654321. Dans les paramètres de profil du portail BizTalk Services, vous remplacez ZZ:1234567 par 01:ChangedValue. Ouvrez l’accord et 01:ChangedValue s’affiche à la place de ZZ:1234567.
Pour modifier le qualificateur d’une identité, supprimez l’accord, mettez à jour **identités** dans le profil du partenaire, puis recréez l’accord.  

> AZURE.WARNING Ce comportement a un impact sur X12 et AS2.  
> 
> 

### <a name="as2-attachments"></a>Pièces jointes AS2
Les pièces jointes aux messages AS2 ne sont pas prises en charge dans les envois ou la réception. Plus précisément, les pièces jointes sont ignorées silencieusement et le corps du message est traité comme un message AS2 classique.  

### <a name="resources-remembering-path"></a>Ressources : mémoriser le chemin d’accès
Lorsque vous ajoutez des **ressources**, la fenêtre de dialogue peut ne pas conserver le chemin d’accès utilisé précédemment pour ajouter une ressource. Pour vous souvenir du chemin d’accès utilisé précédemment, essayez d’ajouter le site web du portail BizTalk Services aux **Sites de confiance** dans Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Si vous renommez le nom de l’entité d’un pont et fermez le projet sans enregistrer les modifications, la réouverture de l’entité génère une erreur
Imaginez un scénario dans l’ordre suivant :  

* Ajoutez un pont (par exemple, un pont unidirectionnel XML) à un projet BizTalk Service  
* Renommez le pont en spécifiant une valeur pour la propriété Nom d’entité. Cette opération renomme le fichier .bridgeconfig associé au nom que vous avez spécifié.  
* Fermez le fichier .bcs (en fermant l’onglet dans Visual Studio) sans enregistrer les modifications.  
* Ouvrez de nouveau le fichier .bcs à partir de l’Explorateur de solutions.  
  Vous remarquerez que lorsque le fichier .bridgeconfig associé porte le nouveau nom que vous avez spécifié, le nom d’entité figurant sur l’aire de conception reste l’ancien nom. Si vous essayez d’ouvrir la configuration du pont en double-cliquant sur le composant de pont, vous obtenez l’erreur suivante :  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Pour éviter ce scénario, veillez à enregistrer vos modifications après avoir renommé les entités d’un projet de service BizTalk.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Le projet de BizTalk Service est généré avec succès même si un artefact a été exclu d’un projet Visual Studio
Imaginez un scénario dans lequel vous ajoutez un artefact (par exemple, un fichier XSD) à un projet BizTalk Service, incluez cet artefact dans la configuration du pont (par exemple, en le spécifiant comme un type de message de demande), puis excluez-le du projet Visual Studio. Dans un tel cas, la compilation du projet ne génère pas d’erreur aussi longtemps que l’artefact supprimé est disponible sur le disque, à l’endroit où il a été inclus dans le projet Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Le projet BizTalk Service ne vérifie pas la disponibilité de schéma lors de la configuration des ponts
Dans un projet BizTalk Services, si un schéma ajouté au projet importe un autre schéma, le projet BizTalk Service ne vérifie pas si le schéma importé est ajouté au projet. Si vous essayez de créer un tel projet, vous n’obtenez pas d’erreur de configuration.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Le message de réponse d’un pont de requête-réponse XML est toujours rédigé avec le jeu de caractères UTF-8
Dans cette version, le jeu de caractères du message de réponse d’un pont demande-réponse XML est toujours défini sur UTF-8.
  
### <a name="user-defined-datatypes"></a>Types de données définis par l’utilisateur
Les adaptateurs BizTalk Adapter Pack présents dans la fonctionnalité BizTalk Adapter Service peuvent utiliser des types de données définis par l’utilisateur pour les opérations d’adaptateur.
Lorsque vous utilisez des types de données définis par l’utilisateur, copiez les fichiers (.dll) sur le lecteur:\Program Files\Microsoft Service\BAServiceRuntime\bin\ d’adaptateur BizTalk ou dans Global Assembly Cache (GAC) sur le serveur qui héberge le service de l’adaptateur BizTalk. Dans le cas contraire, l’erreur suivante peut se produire sur le client :  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Pour installer un fichier dans Global Assembly Cache, il est recommandé d’utiliser GACUtil.exe. Les documents GACUtil.exe expliquent comment utiliser cet outil et les options de ligne de commande Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Redémarrage du site web BizTalk Adapter Service
L’installation du **Runtime du service d’adaptateur BizTalk*** a pour effet de créer le site web du **Service d’adaptateur BizTalk** dans IIS, qui contient l’application **BAService**. L’application **BAService** en interne utilise une liaison relais pour étendre la portée du point de terminaison de service local au cloud. Pour un service hébergé localement, le point de terminaison de relais correspondant est inscrit sur le bus de Service uniquement lorsque le service local démarre.  

Si vous arrêtez et démarrez une application, la configuration de démarrage automatique de l’application n’est pas respectée. Lorsque l’application **BAService** est arrêtée, il est donc préférable de toujours redémarrer le site web **Service d’adaptateur de BizTalk**. Ne démarrez pas ou n’arrêtez pas l’application **BAService** .

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Les caractères spéciaux ne doivent pas être utilisés pour les noms d’adresse et d’entité des composants métier.
Vous ne devez pas utiliser de caractères spéciaux avec les nom et adresse d’entité et des composants métier. Si vous procédez ainsi, vous obtiendrez une erreur lors du déploiement du projet BizTalk Services. Pour certains caractères tels que « % », le site web de Service d’adaptateur BizTalk peut entrer en état d’arrêt et vous devrez alors démarrer manuellement.

### <a name="test-map-with-get-context-property"></a>Test de mappage avec propriété de contexte Get
Si une transformation contient une opération de mappage **Obtenir une propriété de contexte**, l’opération **Tester le mappage** échoue. Pour contourner provisoirement la solution, remplacez l’opération de mappage **Obtenir la propriété de contexte** par une opération de mappage par concaténation de chaîne contenant des données fictives. Cette opération renseignera le schéma cible et vous permettra de tester une autre fonctionnalité de transformation.

### <a name="test-map-property-does-not-display"></a>La propriété Tester le mappage ne s’affiche pas
Les propriétés **Tester le mappage** ne s’affichent pas dans Visual Studio. Cela peut se produire si les fenêtres **Propriétés** et **Explorateur de solutions** ne sont pas ancrées simultanément. Pour résoudre ce problème, ancrez les fenêtres **Propriétés** et **Explorateur de solutions**.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>La liste déroulante Reformat DateTime est affichée en grisé
Une opération de mappage de reformatage de DateTime est ajoutée à l’aire de conception et configurée, la liste déroulante Format peut être grisée. Cela peut se produire si l’affichage de l’ordinateur est défini sur **Moyenne - 125 %** ou **Grande - 150 %**. Pour résoudre l’opération, définissez l’affichage sur **Plus petit - 100 % (par défaut)** à l’aide de la procédure ci-dessous :  

1. Ouvrez le **Panneau de configuration** et cliquez sur **Apparence et personnalisation**.
2. Cliquez sur **Affichage**.
3. Cliquez sur **Petite - 100 % (par défaut)**, puis sur **Appliquer**.

La liste déroulante **Format** devrait désormais fonctionner comme prévu.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplication des accords dans le portail BizTalk Services
Examinez le scénario suivant :

1. Création d’un accord à l’aide de l’API OM de gestion des partenaires commerciaux.
2. Ouvrez l’accord dans le portail BizTalk Services dans deux onglets différents.
3. Déployez l’accord depuis les deux onglets.
4. Par conséquent, les deux accords sont alors déployés, ce qui se traduit par des entrées en double dans le portail BizTalk Services

**Solution de contournement**. Ouvrez l’un des accords dupliqués sur le portail BizTalk Services et annulez le déploiement.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Les ponts n’utilisent pas de certificat mis à jour même après qu’un certificat a été mis à jour dans le magasin d’artefacts
Examinez les scénarios suivants :  

**Scénario 1 : à l’aide de certificats basés sur l’empreinte numérique pour sécuriser le transfert de message d’un pont vers un point de terminaison**  
Envisagez un scénario dans lequel vous utilisez les certificats basés sur l’empreinte numérique dans votre projet de BizTalk Service. Vous pouvez mettre à jour le certificat dans le portail BizTalk Services avec le même nom, mais avec une autre empreinte numérique, mais ne mettez pas le projet BizTalk Service à jour en conséquence. Dans un tel scénario, le pont peut continuer de traiter les messages, car les anciennes données de certificat  se trouvent toujours dans la mémoire cache du canal. Après cela, le traitement du message échoue.  

**Solution de contournement**: mettez à jour le certificat dans le projet BizTalk Service et redéployez le projet.  

**Scénario 2 : en utilisant des procédures basées sur des noms pour identifier les certificats pour sécuriser le transfert de message depuis un pont vers un point de terminaison de service**

Envisagez un scénario dans lequel vous utilisez des procédures basées sur le nom pour identifier des certificats dans votre projet BizTalk Service. Il est possible de mettre à jour le certificat dans le portail BizTalk Services, mais ne mettez pas le projet BizTalk Service à jour en conséquence. Dans un tel scénario, le pont peut continuer de traiter les messages, car les anciennes données de certificat  se trouvent toujours dans la mémoire cache du canal. Après cela, le traitement du message échoue.  

**Solution de contournement**: mettez à jour le certificat dans le projet BizTalk Service et redéployez le projet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Les ponts continuent de traiter les messages même lorsque la base de données SQL est hors connexion
Les ponts BizTalk Services continuent de traiter les messages pendant un certain temps, même si Azure SQL Database Microsoft (qui stocke les informations en cours d’exécution telles que les artefacts déployés et les pipelines), est hors connexion, et ce, parce que les Services BizTalk utilisent les artefacts mis en cache et la configuration du pont.
Si vous ne souhaitez pas que les ponts traitent les messages lorsque la base de données SQL est hors connexion, vous pouvez utiliser les applets de commande PowerShell BizTalk Services pour arrêter ou suspendre le service BizTalk. Consultez [Azure BizTalk Service Management Sample (Exemple de gestion des services Azure BizTalk)](http://go.microsoft.com/fwlink/p/?LinkID=329019) pour que les applets de commande Windows PowerShell gèrent les opérations.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>La lecture d’un message XML au sein d’un composant de code personnalisé d’un pont inclut un caractère BOM supplémentaire
Imaginez un scénario dans lequel vous souhaitez lire un message XML dans le code personnalisé d’un pont. Si vous utilisez l’API .NET System.Text.Encoding.UTF8.GetString(bytes) un caractère BOM supplémentaire est inclus dans la sortie au début du message. Par conséquent, si vous ne souhaitez pas que la sortie inclue le caractère BOM supplémentaire, vous devez utiliser ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>L’envoi de messages à un pont à l’aide de WCF n’évolue pas
Le message envoyé à un pont à l’aide de WCF n’est pas mis à l’échelle. Il est préférable d’utiliser HttpWebRequest si vous souhaitez un client évolutif.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>Mise à niveau : Erreur de fournisseur de jetons après la mise à niveau de la version préliminaire de BizTalk Services vers disponibilité générale
Il existe un accord EDI ou AS2 avec des lots actifs. Lorsque le BizTalk Service est mis à niveau de la version préliminaire vers la disponibilité générale, les événements suivants peuvent se produire :

* Erreur : Le fournisseur de jetons n’a pas pu fournir de jeton de sécurité. Le fournisseur de jeton a renvoyé le message : le nom distant ne peut pas être résolu.
* Les tâches de traitement par lots sont annulées.

**Solution de contournement**: une fois le service BizTalk mis à jour vers GA (General Availability), redéployez l’accord.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>MISE À NIVEAU : la boîte à outils affiche les anciennes icônes des ponts après la mise à niveau du kit de développement logiciel BizTalk Services.
Une fois que vous avez mis à niveau une version antérieure du kit de développement logiciel BizTalk Services, dont les anciennes icônes représentaient les ponts, la boîte à outils continue d’afficher les anciennes icônes pour les ponts. Toutefois, si vous ajoutez un pont à l’aire de conception du projet BizTalk Service, la surface affiche la nouvelle icône.  

**Solution de contournement**. Vous pouvez contourner ce problème en supprimant les fichiers .tbd sous <system drive> : \Users\<utilisateur>\AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>MISE À NIVEAU : la mise à jour du portail BizTalk de la version préliminaire vers la disponibilité générale peut générer un message d’erreur indiquant que la fonctionnalité EDI n’est pas disponible
Si vous êtes connecté au portail de Services BizTalk lors de la mise à jour des services BizTalk de la version préliminaire vers la disponibilité générale, vous pouvez obtenir l’erreur suivante sur le portail :  

Cette fonctionnalité n’est pas disponible dans le cadre de cette édition des Services Microsoft Azure BizTalk. Pour utiliser ces fonctionnalités de bascule vers une édition adéquate.  

**Résolution**: déconnectez-vous du portail, fermez et ouvrez le navigateur, puis connectez-vous au portail.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>Mise à niveau : les nouvelles données de suivi ne s’affichent pas une fois que BizTalk Services est mis à niveau vers la disponibilité générale
Prenons un scénario où vous avez un pont XML déployé sur un abonnement BizTalk Services en version préliminaire. Vous envoyez des messages au pont et les données de suivi correspondantes sont disponibles sur le portail BizTalk Services. Maintenant, si les bits de runtime de portail BizTalk Services et BizTalk Services sont mis à niveau vers la disponibilité générale (GA) et si vous envoyez un message au point de terminaison de pont que vous avez déployé précédemment, les données de suivi n’apparaissent pas pour les messages envoyés après la mise à niveau.  

### <a name="pipelines-versus-bridges"></a>Pipelines et ponts
Dans ce document, les termes « pipeline » et « pont » sont utilisés indifféremment. Les deux termes désignent la même chose, c’est-à-dire une unité de traitement de message déployée sur BizTalk Services.  

### <a name="concepts"></a>Concepts
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

