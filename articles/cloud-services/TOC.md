# [Documentation Services cloud](index.md)

# Vue d'ensemble
## [Présentation de Services cloud](cloud-services-choose-me.md)
## [Création de packages et fichiers de configuration de service cloud](cloud-services-model-and-package.md)

# Prise en main
## [Exemple de service cloud .NET](cloud-services-dotnet-get-started.md)
## [Exemple de service cloud avec les outils Python pour Visual Studio](cloud-services-python-ptvs.md)
## [Configurer un cluster HPC hybride avec Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Procédure
## Planification
### [Tailles de machines virtuelles](cloud-services-sizes-specs.md)
### [Mises à jour](cloud-services-update-azure-service.md)

## Développement
### [Créer des rôles web et de travail PHP](../cloud-services-php-create-web-role.md)
### [Créer et déployer une application Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Créer une application web Node.js avec Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Storage et Visual Studio
#### [Stockage Blob et services connectés](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Stockage File d’attente et services connectés](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Stockage Table et services connectés](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [Configurer les règles de trafic pour un rôle](cloud-services-enable-communication-role-instances.md)
### [Gérer les événements de cycle de vie du service cloud](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Utiliser Twilio pour passer un appel téléphonique (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### Configurer des tâches de démarrage
#### [Créer des tâches de démarrage](cloud-services-startup-tasks.md)
#### [Tâches de démarrage courantes](cloud-services-startup-tasks-common.md)
#### [Utiliser une tâche pour installer .NET sur un rôle de service cloud](cloud-services-dotnet-install-dotnet.md)

### Configurer le Bureau à distance
#### [Portail](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## Déployer
### [Créer et déployer un service cloud dans le portail](cloud-services-how-to-create-deploy-portal.md)
### [Créer un conteneur de service cloud vide dans PowerShell](cloud-services-powershell-create-cloud-container.md)
### [Configurer un nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md)
### [Se connecter à un contrôleur de domaine personnalisé](cloud-services-connect-to-custom-domain.md)

## Gérer le service
### [Tâches de gestion courantes](cloud-services-how-to-manage-portal.md)
### [Configurer le service cloud](cloud-services-how-to-configure-portal.md)
### [Gérer un service cloud à l’aide d’Azure Automation](automation-manage-cloud-services.md)
### [Configurer la mise à l’échelle automatique](cloud-services-how-to-scale-portal.md)
### [Gérer les ressources Azure avec Python](cloud-services-python-how-to-use-service-management.md)
### [Atténuation de l’exécution spéculative](mitigate-se.md)

### [Correctifs du SE invité](cloud-services-guestos-msrc-releases.md)
### Suppression du SE invité
#### [Stratégie de suppression](cloud-services-guestos-retirement-policy.md)
#### [Informations relatives à la suppression de la famille 1](cloud-services-guestos-family1-retirement.md)
### [Actualités concernant les versions de SE invité](cloud-services-guestos-update-matrix.md)
### [Aide-mémoire XPath de configuration d’un rôle Services cloud](cloud-services-role-config-xpath.md)

## Gérer des certificats
### [Services cloud et certificats de gestion](cloud-services-certs-create.md)
### [Configuration du chiffrement SSL](cloud-services-configure-ssl-certificate-portal.md)

## Surveiller
### [Surveiller le service cloud](cloud-services-how-to-monitor.md)
### [Utiliser des compteurs de performances](diagnostics-performance-counters.md)
### [Tester les performances](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Tester avec le profileur Visual Studio](cloud-services-performance-testing-visual-studio-profiler.md)
### Activation des diagnostics
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Stocker et afficher des données de diagnostic dans le stockage Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Suivre un service cloud avec les diagnostics](cloud-services-dotnet-diagnostics-trace-flow.md)

## Résolution des problèmes
### Déboguer 
#### [Options d’un service cloud](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Service cloud local avec Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Service cloud publié avec Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Échec d’allocation de service cloud](cloud-services-allocation-failures.md)
### [Causes courantes de recyclage des rôles de service cloud](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Taille par défaut du dossier TEMP trop petite pour un rôle](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Problèmes de déploiement courants](cloud-services-troubleshoot-deployment-problems.md)
### [Problèmes de démarrage des rôles](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Conseils sur la récupération d’urgence](cloud-services-disaster-recovery-guidance.md)
### Forum aux questions sur les services cloud
#### [FAQ sur la disponibilité de l’application et du service](cloud-services-application-and-service-availability-faq.md)
#### [FAQ sur la configuration et la gestion](cloud-services-configuration-and-management-faq.md)
#### [FAQ sur la connectivité et la mise en réseau](cloud-services-connectivity-and-networking-faq.md)
#### [FAQ sur le déploiement](cloud-services-deployment-faq.md)

# Informations de référence
## [Exemples de code](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [Schéma XML .csdef](schema-csdef-file.md)
### [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
### [Schéma WebRole](schema-csdef-webrole.md)
### [Schéma WorkerRole](schema-csdef-workerrole.md)
### [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)
## [Schéma XML .cscfg](schema-cscfg-file.md)
### [Schéma Role](schema-cscfg-role.md)
### [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Tarification](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
