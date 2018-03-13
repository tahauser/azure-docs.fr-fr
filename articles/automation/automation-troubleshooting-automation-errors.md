---
title: "Dépannage des problèmes courants d’Azure Automation | Microsoft Docs"
description: "Cet article fournit des informations pour le dépannage et la correction d’erreurs courantes dans Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "erreur, dépannage, problème d’automation"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: sngun; v-reagie
ms.openlocfilehash: 76a3096595d07916159b3a50da0793c653a490a0
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Dépannage des problèmes courants dans Azure Automation 
Cet article aide à dépanner les problèmes courants que vous pourriez rencontrer dans Azure Automation et suggère des solutions possibles pour les résoudre.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erreurs d’authentification avec des runbooks Azure Automation
### <a name="scenario-sign-in-to-azure-account-failed"></a>Scénario : la connexion à un compte Azure a échoué
**Erreur :** vous recevez l’erreur « Unknown_user_type : Type d’utilisateur inconnu » lorsque vous travaillez avec les applets de commande Add-AzureAccount ou AzureRmAccount de connexion.

**Raison de l'erreur :** cette erreur se produit si le nom de la ressource d’informations d’identification n'est pas valide ou si le nom d'utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation (Automatisation) ne sont pas valides.

**Conseils de dépannage :** procédez comme suit pour identifier le problème :  

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **@** , dans le nom de la ressources d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.  
2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe stockés dans les informations d’identification Azure Automation dans votre éditeur PowerShell ISE local. Vous pouvez le faire en exécutant les applets de commande suivantes dans PowerShell ISE :  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. Si l’authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Reportez-vous au billet de blog [Authenticating to Azure using Azure Active Directory (Authentification auprès d’Azure à l’aide d’Azure Active Directory)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer correctement le compte Azure Active Directory.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure
**Erreur :** vous recevez l’erreur « l’abonnement nommé ``<subscription name>`` est introuvable » lorsque vous travaillez avec les applets de commande Select-AzureSubscription ou Select-AzureRmSubscription.

**Raison de l’erreur :** cette erreur se produit si le nom de l’abonnement n’est pas valide ou si l’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré en tant qu’administrateur de l’abonnement.

**Conseils de dépannage :** pour déterminer si vous vous êtes correctement authentifié dans Azure et avez accès à l’abonnement que vous tentez de sélectionner, procédez comme suit :  

1. Assurez-vous d’exécuter l’applet de commande **Add-AzureAccount** avant d’exécuter l’applet de commande **Select-AzureSubscription**.  
2. Si le message d’erreur persiste, modifiez votre code en ajoutant l’applet de commande **Get-AzureSubscription** à la suite de l’applet de commande **Add-AzureAccount**, puis exécutez le code. Maintenant, vérifiez si la sortie de Get-AzureSubscription contient les détails de votre abonnement.  

   * Si la sortie ne contient aucun des détails de l’abonnement, vous pouvez en conclure que l’abonnement n’est pas encore initialisé.  
   * Si vous ne voyez pas les détails de l’abonnement dans la sortie, vérifiez que vous utilisez le bon nom d’abonnement ou le bon identificateur avec l’applet de commande **Select-AzureSubscription** .   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scénario : L’authentification auprès d’Azure a échoué car l’authentification multifacteur est activée.
**Erreur :** vous recevez l’erreur « Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required » (« Add-AzureAccount : AADSTS50079 : une inscription de l’authentification forte (résistante) est requise ») pendant une authentification dans Azure avec votre nom d’utilisateur et votre mot de passe Azure.

**Raison de l’erreur :** si votre compte Azure est doté de l’authentification multifacteur, vous ne pouvez pas vous authentifier dans Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

**Conseils de dépannage :** pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à la rubrique sur la [création et l’ajout d’un certificat de gestion des services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec des applets de commande Azure Resource Manager, voir la rubrique sur la [création du principal du service à l’aide du portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md) et l’[authentification d’un principal du service à l’aide d’Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

## <a name="common-errors-when-working-with-runbooks"></a>Erreurs courantes avec des runbooks
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais n’a pas pu démarrer
**Erreur :** votre runbook échoue et indique l’erreur « The job was tried three times but it failed. » (Le travail a effectué trois tentatives de démarrage mais a échoué.).

**Raison de l’erreur :** les éléments suivants peuvent être à l’origine de cette erreur :  

1. Limite de mémoire. Il existe des limites documentées sur la quantité de mémoire allouée à un bac à sable que le [service Automation limite](../azure-subscription-service-limits.md#automation-limits), de façon à ce qu’un travail puisse échouer s’il utilise plus de 400 Mo de mémoire. 

2. Module incompatible. Cela peut se produire si les dépendances de module ne sont pas correctes. Dans ce cas, votre runbook renvoie généralement un message « Commande introuvable » ou « Impossible de lier le paramètre ». 

**Conseils de dépannage :** l’une des solutions suivantes peut corriger ce problème :

* Les méthodes recommandées pour travailler dans la limite de mémoire consistent à diviser la charge de travail en plusieurs runbooks, à ne pas traiter trop de données en mémoire, à ne pas écrire de sortie inutile depuis vos runbooks ou à prendre en compte le nombre de points de contrôle que vous écrivez dans vos runbooks de flux de travail PowerShell.  

* Mettez à jour vos modules Azure en suivant les étapes de l’article [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scénario : échec du runbook en raison d’un objet désérialisé
**Erreur :** votre runbook a échoué en émettant l’erreur « Impossible de lier le paramètre ``<ParameterName>``. Impossible de convertir la valeur ``<ParameterType>`` du type désérialisé ``<ParameterType>`` en type ``<ParameterType>`` ».

**Raison de l’erreur :** si votre runbook est un flux de travail PowerShell, il stocke des objets complexes au format désérialisé afin de conserver l’état du runbook si le flux de travail est suspendu.

**Conseils de dépannage :** l’une des trois solutions suivantes peut corriger ce problème :

1. Si vous transférez des objets complexes d’une applet de commande vers une autre, encapsulez ces applets de commande dans un bloc InlineScript.
2. Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.
3. Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scénario : le travail de Runbook a échoué car le quota alloué dépassé
**Erreur :** votre travail de runbook a échoué en émettant l’erreur « Le quota mensuel de durée d’exécution totale pour cet abonnement a été atteint ».

**Motif de l’erreur :** cette erreur se produit lorsque l’exécution du travail dépasse le quota disponible de 500 minutes gratuit pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment un le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks et la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

**Conseils de dépannage :** si vous souhaitez utiliser plus de 500 minutes de traitement par mois, vous devez modifier votre abonnement à partir du niveau Gratuit au niveau de base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :  

1. Connectez-vous à votre abonnement Azure :  
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau  
3. Cliquez sur **Paramètres** > **Tarifs**.
4. Cliquez sur **Activer** en bas de page pour actualiser votre compte au niveau **De base**.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scénario : l’applet de commande n’est pas reconnue lors de l’exécution d’un runbook
**Erreur :** la tâche de runbook échoue en renvoyant l’erreur « ``<cmdlet name>`` : Le terme ``<cmdlet name>`` n’est pas reconnu comme nom d’applet de commande, fonction, fichier de script ou programme exécutable. »

**Motif de l’erreur :** cette erreur survient quand le moteur PowerShell ne trouve pas l’applet de commande que vous utilisez dans votre runbook. Cela peut être dû au fait que le module contenant l’applet de commande n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que l’applet de commande existe dans un autre module et Automation ne peut pas résoudre le nom.

**Conseils de dépannage :** l’une des solutions suivantes peut corriger ce problème :  

* Vérifiez que vous avez correctement saisi le nom de l’applet de commande.  
* Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande que vous souhaitez trouver dans la bibliothèque ou exécutez **Get-Command ``<CommandName>``**. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez-la à la zone de dessin et assurez-vous que vous utilisez un paramètre valide défini dans votre runbook.  
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **Nom_module\Nom_applet_de_commande**.  
* Si vous exécutez le runbook localement dans un groupe de Workers hybride, assurez-vous que le module/applet de commande est installé sur l'ordinateur qui héberge le Worker hybride.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scénario : Un runbook de longue durée échoue systématiquement avec l’exception : « La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle ».
**Raison de l’erreur :** il s’agit du comportement par défaut dû à la surveillance de la répartition de charge équilibrée des processus au sein d’Azure Automation, qui suspend automatiquement un runbook s’il s’exécute pendant plus de 3 heures. Toutefois, le message d’erreur ne fournit pas d’options pour la suite des événements. Un runbook peut être interrompu pour plusieurs raisons. Les suspensions sont généralement dues à des erreurs. Par exemple, une exception non interceptée dans un runbook, une panne réseau ou une panne sur le Runbook Worker qui exécute le runbook entraîne la suspension de ce dernier et son démarrage à partir de son dernier point de contrôle au moment de la reprise.

**Conseils de dépannage :** la solution documentée pour éviter ce problème consiste à utiliser des points de contrôle dans un flux de travail. Pour plus d’informations, consultez [Découverte des flux de travail PowerShell](automation-powershell-workflow.md#checkpoints). Vous trouverez une explication plus détaillée de la distribution de charge équilibrée dans le billet de blog [Using Checkpoints in Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) (Utilisation de points de contrôle dans des runbooks).

## <a name="common-errors-when-importing-modules"></a>Erreurs courantes survenant lors de l’importation de modules
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scénario : le module ne parvient pas à terminer l’importation ou il est impossible d’exécuter des applets de commande après l’importation
**Erreur :** un module ne parvient pas à importer ou réussit l’importation, mais aucune applet de commande n’est extraite.

**Raison de l’erreur :** voici quelques raisons courantes pour lesquelles un module peut échouer dans l’importation vers Azure Automation :

* La structure ne correspond pas à la structure dont Automation a besoin.
* Le module dépend d’un autre module qui n’a pas été déployé sur votre compte Automation.
* Le module n’a pas de dépendances dans le dossier.
* L’applet de commande **New-AzureRmAutomationModule** est utilisée pour charger le module, et vous ne disposez pas du chemin d’accès de stockage complet ou n’avez pas chargé le module en utilisant une URL accessible publiquement.

**Conseils de dépannage :** l’une des solutions suivantes peut corriger ce problème :

* Assurez-vous que le module suit le format suivant : NomModule.zip **->** NomModule ou Numéro de version **->** (ModuleName.psm1, ModuleName.psd1)
* Ouvrez le fichier .psd1 et regardez si le module possède des dépendances. Si c’est le cas, téléchargez ces modules dans le compte Automation.
* Assurez-vous que les fichiers .dll référencés sont présents dans le dossier de module.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erreurs courantes avec la Configuration d’état souhaité (DSC)
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scénario : le nœud est en état d’échec avec une erreur « Introuvable »
**Erreur :** le nœud dispose d’un rapport d’état **Échec** contenant l’erreur suivante : « The attempt to get the action from server https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction failed because a valid configuration ``<guid>`` cannot be found » (« La tentative d’obtention de l’action depuis le serveur https:////accounts//Nodes(AgentId=)/GetDscAction a échoué, car aucune configuration valide n’a été trouvée »).

**Raison de l’erreur :** cette erreur se produit généralement quand le nœud est affecté à un nom de configuration (par exemple ABC) au lieu d’un nom de configuration de nœud (par exemple ABC.WebServer).

**Conseils de dépannage :**

* Assurez-vous d'affecter le nœud avec « nom de configuration de nœud » et non pas le « nom de configuration ».
* Vous pouvez affecter une configuration de nœud à un nœud à l'aide du portail Azure ou d’une applet de commande PowerShell.

  * Pour affecter une configuration de nœud à un nœud à l’aide du portail Azure, ouvrez la page **Nœuds DSC**, sélectionnez un nœud, puis cliquez sur le bouton **Attribuer une configuration de nœud**.  
  * Pour affecter une configuration de nœud à un nœud à l’aide d’une applet de commande PowerShell, utilisez l’applet de commande **Set-AzureRmAutomationDscNode** .

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scénario : aucune configuration de nœud (fichiers MOF) ne s’est produite au cours d’une compilation de configuration
**Erreur :** la tâche de compilation de votre DSC a été interrompue avec l’erreur : « Compilation completed successfully, but no node configuration .mofs were generated » (« La compilation s’est terminée correctement, mais aucune configuration de nœud .mofs n’a été générée »).

**Raison de l’erreur :** quand l’expression qui suit le mot-clé **Node** dans la configuration DSC s’évalue à `$null`, aucune configuration de nœud n’est générée.

**Conseils de dépannage :** l’une des solutions suivantes peut corriger ce problème :

* Vérifiez que l’expression en regard du mot clé **Node** dans la définition de la configuration n’est pas $null.
* Si vous effectuez une transmission de ConfigurationData pendant la compilation de la configuration, vérifiez que vous transmettez les valeurs attendues nécessaires à la configuration depuis [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scénario : le rapport du nœud DSC se bloque à l’état « en cours »
**Erreur :** l’agent DSC génère le message « Impossible de trouver une instance avec les valeurs de propriétés fournies ».

**Raison de l’erreur :** vous avez mis à niveau votre version WMF et endommagé WMI.

**Conseils de dépannage :** pour résoudre ce problème, suivez les instructions fournies dans [DSC known issues and limitations](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) (Problèmes connus et limitations DSC).

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>Scénario : il est impossible d’utiliser des informations d’identification dans une configuration DSC
**Erreur :** le travail de compilation de votre DSC a été interrompu avec l’erreur : « Erreur System.InvalidOperationException lors du traitement de la propriété ’Credential’ de type ``<some resource name>`` : La conversion et le stockage d’un mot de passe chiffré sous forme de texte en clair n’est autorisée que si PSDscAllowPlainTextPassword a la valeur true ».

**Raison de l’erreur** : vous avez utilisé des informations d’identification dans une configuration, mais n’avez pas fourni la bonne valeur de **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud.

**Conseils de dépannage :**

* Assurez-vous de transmettre la bonne valeur **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud mentionnée dans la configuration. Pour plus d’informations, consultez les [ressources d’Azure Automation DSC](automation-dsc-compile.md#assets).

## <a name="common-errors-when-onboarding-solutions"></a>Erreurs courantes lors de l’intégration des solutions

Lorsque vous intégrez des solutions, vous pouvez rencontrer des erreurs. Voici une liste des erreurs courantes que vous pourriez voir.

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**Raison de l’erreur :**

Ce code d’erreur signifie que la requête de recherche de groupe d’ordinateurs enregistrée utilisée pour cibler la solution n’a pas été formatée correctement. Il est possible que vous ayez modifié la requête, ou elle peut avoir été modifiée par le système.

**Conseils de dépannage :**

Vous pouvez supprimer la requête pour cette solution et réintégrer la solution, ce qui recrée la requête. La requête se trouve dans votre espace de travail, sous **Recherches enregistrées**. Le nom de la requête est **MicrosoftDefaultComputerGroup**, et la catégorie de la requête est le nom de la solution associée à cette requête. Si plusieurs solutions sont activées, **MicrosoftDefaultComputerGroup** s’affiche plusieurs fois sous **Recherches enregistrées**.

### <a name="policyviolation"></a>PolicyViolation

**Raison de l’erreur :**

Ce code d’erreur signifie que le déploiement a échoué en raison d’une violation d’une ou de plusieurs stratégies.

**Conseils de dépannage :**

Pour déployer correctement la solution, vous devez envisager de modifier la stratégie indiquée. Étant donné qu’il existe plusieurs types de stratégies différents qui peuvent être définis, les modifications spécifiques requises dépendent de la stratégie violée. Par exemple, si une stratégie a été définie sur un groupe de ressources qui interdisait la modification du contenu de certains types de ressources au sein de ce groupe de ressources, vous pourriez, par exemple, effectuer les opérations suivantes :

*   Retirer la stratégie.
* Essayer d’effectuer une intégration à un groupe de ressources différent.
* Réviser la stratégie, par exemple:
   * en redirigant la stratégie vers une ressource spécifique (par exemple vers un compte Automation spécifique) ;
   * en révisant l’ensemble de ressources que cette stratégie a été configurée pour refuser.

Consultez les notifications dans le coin supérieur droit du portail Azure ou accédez au groupe de ressources comportant votre compte Automation, puis sélectionnez **Déploiements** sous **Paramètres** pour afficher le déploiement mis en échec. Pour en savoir plus sur Azure Policy, consultez : [Présentation d’Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez suivi les étapes de dépannage précédentes et que vous ne trouvez pas la réponse à votre problème, vous pouvez consulter les options de support supplémentaires suivantes :

* Obtenir l’aide des experts Azure. Soumettre votre problème sur les forums [MSDN Azure ou Stack Overflow](https://azure.microsoft.com/support/forums/).
* Signaler un incident au support Azure Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir un support** dans la section **Support technique et sur la facturation**.
* Publier une demande de script sur le [Centre de scripts](https://azure.microsoft.com/documentation/scripts/) si vous recherchez une solution de runbook Azure Automation ou un module d’intégration.
* Publier vos commentaires ou vos demandes de fonctionnalités pour Azure Automation sur [User Voice](https://feedback.azure.com/forums/34192--general-feedback).
