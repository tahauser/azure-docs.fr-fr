# Vue d'ensemble
## [Qu’est-ce que la messagerie Service Bus ?](service-bus-messaging-overview.md)
## [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
## [Architecture de Service Bus](service-bus-architecture.md)
## [FAQ](service-bus-faq.md)

# Prise en main
## [Créer un espace de noms](service-bus-create-namespace-portal.md)
### [Files d’attente, rubriques et abonnements.](service-bus-queues-topics-subscriptions.md)
## Files d’attente
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.JS](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Rubriques et abonnements
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.JS](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
## [Créer une application Service Bus à plusieurs niveaux](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

# Procédure
## Planifier et concevoir
### [Identité de service managée (préversion)](service-bus-managed-service-identity.md)
### [Contrôle d’accès en fonction du rôle (préversion)](service-bus-role-based-access-control.md)
### [Messagerie Premium](service-bus-premium-messaging.md)
### [Comparer les files d’attente Azure et Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Optimiser les performances](service-bus-performance-improvements.md)
### [Récupération d’urgence et géo-réplication](service-bus-geo-dr.md)
### [Messagerie asynchrone et haute disponibilité](service-bus-async-messaging.md)
### [Gestion des urgences et des pannes](service-bus-outages-disasters.md)

## Développement
### Gestion des messages
#### [Messages, charges utiles et sérialisation](service-bus-messages-payloads.md)
#### [Transferts de messages, verrous et règlement](message-transfers-locks-settlement.md)
#### [Timestamps et séquencement de message](message-sequencing.md)
#### [Expiration du message (durée de vie)](message-expiration.md)
### [Authentification et autorisation](service-bus-authentication-and-authorization.md)
#### [Migrer à partir des services ACS vers SAP](service-bus-migrate-acs-sas.md)
#### [Authentification avec les signatures d’accès partagé](service-bus-sas.md)
### [Actions et filtres de rubrique](topic-filters.md)
### [Files d’attente et rubriques partitionnées](service-bus-partitioning.md)
### [Sessions de message](message-sessions.md)
### AMQP
#### [Vue d’ensemble du protocole AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service et AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Guide du protocole AMQP](service-bus-amqp-protocol-guide.md)
#### [Opérations basées sur les requêtes-réponses AMQP](service-bus-amqp-request-response.md)
### Fonctionnalités avancées
#### [Files d’attente de lettres mortes](service-bus-dead-letter-queues.md)
#### [Prérécupération de messages](service-bus-prefetch.md)
#### [Détection de messages en double](duplicate-detection.md)
#### [Compteurs de messages](message-counters.md)
#### [Report de message](message-deferral.md)
#### [Navigation dans les messages](message-browsing.md)
#### [Chaîner des entités avec le transfert automatique](service-bus-auto-forwarding.md)
#### [Traitement des transactions](service-bus-transactions.md)
#### [Implémentation de l’espace de noms associé](service-bus-paired-namespaces.md)
## Gérer
### [Analyser Service Bus avec la surveillance Azure](service-bus-metrics-azure-monitor.md)
### [Bibliothèques de gestion de Service Bus](service-bus-management-libraries.md)
### [Journaux de diagnostic](service-bus-diagnostic-logs.md)
### [Interrompre et réactiver des entités de messagerie](entity-suspend.md)
### [Utiliser les modèles Azure Resource Manager](service-bus-resource-manager-overview.md)
#### [Créer un espace de noms](service-bus-resource-manager-namespace.md)
#### [Créer un espace de noms et une file d’attente](service-bus-resource-manager-namespace-queue.md)
#### [Créer des espaces de noms avec des rubriques et des abonnements](service-bus-resource-manager-namespace-topic.md)
#### [Créer une règle d’autorisation pour les espaces de noms et les files d’attente](service-bus-resource-manager-namespace-auth-rule.md)
#### [Créer des espaces de noms avec des rubriques, des abonnements et des règles](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Utilisation d’Azure PowerShell pour approvisionner des entités](service-bus-manage-with-ps.md)

# Référence
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Exceptions](service-bus-messaging-exceptions.md)
## [Quotas](service-bus-quotas.md)
## [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
## [Syntaxe SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blog](https://blogs.msdn.microsoft.com/servicebus/)
## [Forums MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Tarification](https://azure.microsoft.com/pricing/details/service-bus/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Détails de la tarification](service-bus-pricing-billing.md)
## [Exemples](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


