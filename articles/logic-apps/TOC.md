# Vue d’ensemble
## [Que sont les applications logiques ?](logic-apps-what-are-logic-apps.md)
## [B2B avec Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
## [Connecteurs](../connectors/apis-list.md)

# Prise en main
## [Créez votre première application logique](logic-apps-create-a-logic-app.md)
## [Créer des applications logiques à partir de modèles](logic-apps-create-logic-apps-from-templates.md)
## [Créer des applications logiques à l’aide de modèles Resource Manager](logic-apps-create-deploy-azure-resource-manager-templates.md)
## [Déplacer des applications MABS vers des applications logiques](logic-apps-move-from-mabs.md)

# Procédure
## Créer
### [Créer et déployer des applications logiques avec Visual Studio](logic-apps-deploy-from-vs.md)
### [Ajouter des conditions et exécuter des flux de travail](logic-apps-use-logic-app-features.md)
### [Ajouter des instructions switch](logic-apps-switch-case.md)
### [Ajouter du code personnalisé avec Azure Functions](logic-apps-azure-functions.md)
### [Boucles, étendues et décomposition des données](logic-apps-loops-and-scopes.md)
### [Messages du processus de traitement par lot](logic-apps-batch-process-send-receive-messages.md)
### [Créer des définitions d’application logique](logic-apps-author-definitions.md)
### [Appeler, déclencher ou imbriquer des applications logiques](logic-apps-http-endpoint.md)
### Développer des connecteurs personnalisés
#### [Vue d’ensemble des connecteurs personnalisés](custom-connector-overview.md)
#### [Créer des connecteurs à partir d’API web](custom-connector-build-web-api-app-tutorial.md)
#### [Sécuriser des connecteurs avec Azure AD](custom-connector-azure-active-directory-authentication.md)
#### [Décrire des API avec Postman](custom-connector-api-postman-collection.md)
#### [Étendre OpenAPI pour les connecteurs personnalisés](custom-connector-openapi-extensions.md)
#### [Créer et inscrire un connecteur OpenAPI](logic-apps-custom-connector-register.md)
#### [Créer et inscrire un connecteur SOAP](logic-apps-soap-connector-create-register.md)
#### [Certifier votre connecteur](custom-connector-submit-certification.md)
#### [FAQ pour les connecteurs personnalisés](custom-connector-faq.md)
### Développer des API personnalisées
#### [Créer des API personnalisées pour les applications logiques](logic-apps-create-api-app.md)
#### [Appels sécurisés à des API personnalisées](logic-apps-custom-api-authentication.md)
#### [Déployer et appeler des API personnalisées](logic-apps-custom-api-host-deploy-call.md)
### [Gérer les erreurs et exceptions](logic-apps-exception-handling.md)
### [Gérer les types de contenu](logic-apps-content-type.md)
### [Sécuriser vos applications logiques](logic-apps-securing-a-logic-app.md)
### [Dépannage](logic-apps-diagnosing-failures.md)
### [Limites et configuration](logic-apps-limits-and-config.md)

## B2B avec Enterprise Integration Pack
### [Comptes d’intégration](logic-apps-enterprise-integration-create-integration-account.md)
### [Partenaires](logic-apps-enterprise-integration-partners.md)
### [Accords](logic-apps-enterprise-integration-agreements.md)
### [Traitement B2B](logic-apps-enterprise-integration-b2b.md)
### [Envoyer des messages EDI en tant que traitement par lots](logic-apps-scenario-edi-send-batch-messages.md)
### [Traitement XML](logic-apps-enterprise-integration-xml.md)
### [Traitement des fichiers plats](logic-apps-enterprise-integration-flatfile.md)
### [Valider Xml](logic-apps-enterprise-integration-xml-validation.md)
### [Ajouter des schémas de validation XML](logic-apps-enterprise-integration-schemas.md)
### [Transformer XML](logic-apps-enterprise-integration-transform.md)
### [Ajouter des mappages de transformation XML](logic-apps-enterprise-integration-maps.md)
### [Ajouter des certificats pour la sécurité B2B](logic-apps-enterprise-integration-certificates.md)
### [Stocker des métadonnées pour les artefacts](logic-apps-enterprise-integration-metadata.md)
### [Intégration d’entreprise AS2](logic-apps-enterprise-integration-as2.md)
### [Codage AS2](logic-apps-enterprise-integration-as2-encode.md)
### [Décodage AS2](logic-apps-enterprise-integration-as2-decode.md)
### [Intégration d’entreprise EDIFACT](logic-apps-enterprise-integration-edifact.md)
### [Codage EDIFACT](logic-apps-enterprise-integration-edifact-encode.md)
### [Décodage EDIFACT](logic-apps-enterprise-integration-edifact-decode.md)
### [Comment gérer les documents EDIFACT contenant un segment UNH2.5](logic-apps-enterprise-integration-edifact_inputfile_unh2.5.md)
### [Intégration d’entreprise X12](logic-apps-enterprise-integration-x12.md)
### [Codage X12](logic-apps-enterprise-integration-x12-encode.md)
### [Décodage X12](logic-apps-enterprise-integration-x12-decode.md)
### [Récupération d’urgence](logic-apps-enterprise-integration-b2b-business-continuity.md)
### [Erreurs et des solutions](logic-apps-enterprise-integration-b2b-list-errors-solutions.md)

## Accès aux données locales
### [Se connecter à des données locales](logic-apps-gateway-connection.md)
### [Installer une passerelle de données](logic-apps-gateway-install.md)

## Automatiser et déployer
### [Créer des modèles de déploiement automatisé](logic-apps-create-deploy-template.md)
### [Publier à partir de Visual Studio](logic-apps-deploy-from-vs.md)

## Gérer et surveiller
### [Gérer des applications logiques avec Visual Studio](logic-apps-manage-from-vs.md)
### [Gérer les comptes d’intégration](logic-apps-enterprise-integration-accounts.md)
### [Surveiller l’état de l’application logique](logic-apps-monitor-your-logic-apps.md)
### [Surveiller l’état de l’application logique dans OMS avec Log Analytics](logic-apps-monitor-your-logic-apps-oms.md)
### [Surveiller les messages B2B](logic-apps-monitor-b2b-message.md)
#### [Suivre des messages B2B dans OMS](logic-apps-track-b2b-messages-omsportal.md)
#### [Interroger OMS à propos des messages B2B](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
#### [Schéma de suivi AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
#### [Schéma de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md)
#### [Schéma de suivi personnalisé](logic-apps-track-integration-account-custom-tracking-schema.md)

## [Exemples, scénarios et procédures pas à pas](logic-apps-examples-and-scenarios.md)
### [Créer un tableau de bord des réseaux sociaux sans serveur](logic-apps-scenario-social-serverless.md)
### [Appeler des applications logiques avec Azure Functions](logic-apps-scenario-function-sb-trigger.md)
### [Ajouter la gestion des erreurs et des exceptions](logic-apps-scenario-error-and-exception-handling.md)
### [Traitement B2B](logic-apps-enterprise-integration-b2b.md) 

## Tarification et facturation
### [Tarification](https://azure.microsoft.com/pricing/details/logic-apps/)
### [Contrôle de l’utilisation](logic-apps-pricing.md)

# Sans serveur
## Vue d'ensemble
### [Sans serveur dans Azure](logic-apps-serverless-overview.md)
## Prise en main
### [Créer une application sans serveur dans Visual Studio](logic-apps-serverless-get-started-vs.md)
## Scénarios et procédures pas à pas
### [Collecter les commentaires des clients](logic-apps-scenario-social-serverless.md)
### [Envoyer des e-mails automatisés](https://channel9.msdn.com/Blogs/Microsoft-Integration/Build-and-Deploy-Serverless-Part-1)
### [Traitement des commandes de B2B/EDI](logic-apps-enterprise-integration-overview.md)

# Référence
## [Langage de définition de flux de travail](logic-apps-workflow-definition-language.md)
## [Actions et déclencheurs de flux de travail](logic-apps-workflow-actions-triggers.md)
## [API REST](/rest/api/logic/)
## [Azure PowerShell](/powershell/module/azurerm.logicapp)
## Connecteurs : rubriques approfondies
### [AS2](logic-apps-enterprise-integration-as2.md)
### [Stockage Blob Azure](../connectors/connectors-create-api-azureblobstorage.md)
### [Azure Functions](logic-apps-azure-functions.md)
### [Azure Logic Apps](logic-apps-http-endpoint.md)
### [Base de données SQL Azure](../connectors/connectors-create-api-sqlazure.md)
### [API personnalisées/applications web](logic-apps-create-api-app.md)
### [DB2](../connectors/connectors-create-api-db2.md)
### [Dynamics 365 CRM Online](../connectors/connectors-create-api-crmonline.md)
### [EDIFACT](logic-apps-enterprise-integration-edifact.md)
### [Hubs d'événements](../connectors/connectors-create-api-azure-event-hubs.md)
### [Système de fichiers](logic-apps-using-file-connector.md)
### [Fichier plat](logic-apps-enterprise-integration-flatfile.md)
### [FTP](../connectors/connectors-create-api-ftp.md)
### [HTTP](../connectors/connectors-native-http.md)
### [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
### [Déclencheur HTTPWebhook](../connectors/connectors-native-webhook.md)
### [Informix](../connectors/connectors-create-api-informix.md)
### [Compte d’intégration](logic-apps-enterprise-integration-metadata.md)
### [MQ](../connectors/connectors-create-api-mq.md)
### [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)
### [Oracle Database](../connectors/connectors-create-api-oracledatabase.md)
### [Requête/réponse](../connectors/connectors-native-reqres.md)
### [Salesforce](../connectors/connectors-create-api-salesforce.md)
### [SAP](logic-apps-using-sap-connector.md)
### [Planification](../connectors/connectors-native-recurrence.md)
### [Service Bus](../connectors/connectors-create-api-servicebus.md)
### [SharePoint Online](../connectors/connectors-create-api-sharepointonline.md)
### [SharePoint Server](../connectors/connectors-create-api-sharepointserver.md)
### [SQL Server](../connectors/connectors-create-api-sqlazure.md)
### [Transformer XML](logic-apps-enterprise-integration-transform.md)
### [Twitter](../connectors/connectors-create-api-twitter.md)
### [X12](logic-apps-enterprise-integration-x12.md)
### [Validation XML](logic-apps-enterprise-integration-xml-validation.md)

## [Connecteurs : détails complets](https://docs.microsoft.com/connectors/)
## Historique de schéma
### [GA](logic-apps-schema-2016-04-01.md)
### [Version préliminaire](logic-apps-schema-2015-08-01.md)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azurelogicapps)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=logic-apps)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-logic-apps)
