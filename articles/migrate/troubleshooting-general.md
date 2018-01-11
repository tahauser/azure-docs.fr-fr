---
title: "Résolution de problèmes Azure Migrate | Microsoft Docs"
description: "Fournit une vue d'ensemble des problèmes connus dans le service Azure Migrate et des conseils de dépannage pour les erreurs courantes."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 1fcc9e12e63eda73d53ae2085bc2a64d31ea2067
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="troubleshoot-azure-migrate"></a>Résoudre les problèmes d’Azure Migrate

## <a name="troubleshoot-common-errors"></a>Résolution des erreurs courantes

[Azure Migrate](migrate-overview.md) évalue les charges de travail sur site pour la migration vers Azure. Utilisez cet article pour résoudre les problèmes lors du déploiement et de l'utilisation d'Azure Migrate.


**Le collecteur n’est pas en mesure de se connecter à Internet**

Cela peut se produire lorsque l’ordinateur que vous utilisez est derrière un proxy. Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
Si vous utilisez un proxy de pare-feu basé sur une URL pour contrôler la connectivité sortante, veillez à inclure dans la liste verte les URL nécessaires suivantes :

**URL** | **Objectif**  
--- | ---
*. portal.azure.com | Indispensable pour vérifier la connectivité avec le service Azure et valider les problèmes de synchronisation de l’heure.
*. oneget.org | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell.

**Le collecteur ne peut pas se connecter au projet à l'aide de l'ID du projet et de la clé copiée sur le portail.**

Assurez-vous d'avoir copié et collé les bonnes informations. Pour résoudre ce problème, installez Microsoft Monitoring Agent (MMA) comme suit :

1. Sur la machine virtuelle du collecteur, téléchargez [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Pour démarrer l’installation, double-cliquez sur le fichier téléchargé.
3. Dans la page de **bienvenue** du programme d'installation, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
4. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
5. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics (OMS)** > **Suivant**.
6. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l'ID et la clé du projet que vous avez copiés. Cliquez ensuite sur **Suivant**.
7. Vérifiez que l'agent peut se connecter au projet. Dans le cas contraire, vérifiez les paramètres. Si l'agent peut se connecter mais que le collecteur ne le peut pas, contactez le support.


**Erreur 802 : Je reçois une erreur de synchronisation de date et d'heure.**

L'horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l'heure actuelle. Modifiez l'heure de l'horloge sur la machine virtuelle du collecteur afin qu'elle corresponde à l'heure actuelle, en procédant comme suit :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez w32tm /tz.
3. Pour synchroniser l'heure, exécutez w32tm /resync.

**Ma clé de projet se termine par les symboles « == ». Ces symboles sont codés en d'autres caractères alphanumériques par le collecteur. Est-ce normal ?**

Oui, chaque clé de projet se termine par « == ». Le collecteur chiffre la clé du projet avant de la traiter.

**Les données de performances des disques et cartes réseau s'affichent sous forme de zéros**

Cela peut se produire si le niveau de paramétrage des statistiques sur le serveur vCenter est inférieur à 3. Au niveau 3 ou supérieur, vCenter stocke l'historique des performances de la machine virtuelle pour le calcul, le stockage et le réseau. À un niveau inférieur à 3, vCenter ne stocke pas les données de stockage et de réseau, mais uniquement les données d'UC et de mémoire. Dans ce scénario, les données de performances s'affichent comme des zéros dans Azure Migrate, et Azure Migrate fournit une recommandation de taille pour les disques et les réseaux en fonction des métadonnées collectées sur les machines sur site.

Pour activer la collecte des données de performances des disques et du réseau, définissez le niveau de paramétrage des statistiques sur 3. Puis attendez au moins une journée pour découvrir votre environnement et l'évaluer. 

**J'ai installé des agents et utilisé la visualisation de dépendance pour créer des groupes. Maintenant, après le basculement, les machines affichent l'action « Installer l'agent » au lieu de « Afficher les dépendances ».**
* Après un basculement planifié ou non planifié, les machines sur site sont désactivées et les machines équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente. Elles peuvent acquérir une adresse IP différente selon que l'utilisateur a choisi de conserver ou non l'adresse IP sur site. Si les adresses MAC et IP diffèrent, Azure Migrate n'associe pas les machines sur site avec les données de dépendance Service Map et demande à l'utilisateur d'installer des agents au lieu d'afficher les dépendances.
* Après un basculement de test, les machines sur site restent activées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que l'utilisateur ne bloque le trafic OMS provenant de ces machines, Azure Migrate n'associe pas les machines sur site avec les données de dépendance Service Map et demande à l'utilisateur d'installer des agents au lieu d'afficher les dépendances.


## <a name="troubleshoot-readiness-issues"></a>Résoudre les problèmes de disponibilité

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Sélectionnez BIOS avant d'exécuter une migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés de la machine avant la migration.
La taille du disque dépasse la limite autorisée | Réduisez la taille du disque à moins de 4 To avant la migration. 
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l'adéquation du disque en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe. 
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n'a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs de la machine sur site avant de migrer. 
Un ou plusieurs disques ne sont pas adaptés | Assurez-vous que la taille des disques sur site est inférieure ou égale à 4 To avant d'exécuter une migration.
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées de la machine avant la migration.
Impossible de déterminer l'adéquation de la machine virtuelle en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe. 
Impossible de déterminer l'adéquation pour un ou plusieurs disques en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l'adéquation pour un ou plusieurs adaptateurs réseau en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage requis | Les performances de stockage (IOPS/débit) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les besoins de stockage de la machine avant la migration.
Nous n'avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau requis | Les performances réseau (entrée/sortie) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les exigences de mise en réseau de la machine. 
Nous n'avons pas trouvé de machine virtuelle correspondant au niveau de tarification indiqué | Vérifiez les paramètres du niveau de tarification. 
Nous n'avons pas trouvé de machine virtuelle dans l'emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Problèmes de prise en charge du système d'exploitation Linux | Assurez-vous que vous exécutez la version 64 bits de ces [systèmes d'exploitation](../virtual-machines/linux/endorsed-distros.md) pris en charge.
Problèmes de prise en charge du système d'exploitation Windows | Vérifiez que vous exécutez un système d’exploitation pris en charge. [En savoir plus](concepts-assessment-calculation.md#azure-suitability-analysis)
Système d'exploitation inconnu. | Vérifiez que le système d'exploitation spécifié dans vCenter est correct et répétez le processus de découverte.
Nécessite un abonnement Visual Studio. | Les systèmes d'exploitation clients Windows sont uniquement pris en charge par les abonnements Visual Studio (MSDN).


## <a name="collect-logs"></a>Collecter les journaux

**Comment collecter des journaux sur la machine virtuelle du collecteur ?**

La journalisation est activée par défaut. Les journaux se trouvent dans les emplacements suivants :

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Pour collecter le suivi d'événements pour Windows, procédez comme suit :

1. Sur la machine virtuelle du collecteur, ouvrez une fenêtre de commande PowerShell.
2. Exécutez **Get-EventLog -LogName Application | export-csv eventlog.csv**.

**Comment collecter les journaux du trafic réseau du portail ?**

1. Ouvrez le navigateur et connectez-vous [au portail](https://portal.azure.com).
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Cliquez sur l'onglet **Réseau**, puis commencez la capture du trafic réseau :
 - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si ce n'est pas le cas, cliquez sur le cercle noir pour commencer
 - Dans Edge/Internet Explorer, l'enregistrement devrait commencer automatiquement. Si ce n'est pas le cas, cliquez sur le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
 - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette opération compresse et exporte les journaux en tant que fichier .har.
 - Dans Edge/Internet Explorer, cliquez sur l'icône **Exporter le trafic capturé**. Cette opération compresse et exporte les journaux.
6. Accédez à l'onglet **Console** pour vérifier la présence d'avertissements ou d'erreurs. Pour enregistrer le journal de la console :
 - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
 - Dans Edge/Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**. 
7. Fermez les outils de développement.
 



