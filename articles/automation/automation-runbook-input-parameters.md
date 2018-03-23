---
title: Paramètres d’entrée de Runbook
description: Les paramètres d’entrée de Runbook augmentent la flexibilité des Runbooks en vous permettant de transmettre des données à un Runbook lors de son démarrage. Cet article décrit différents cas où des paramètres d’entrée sont utilisés dans des Runbooks.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a2ce87c300d3e9092794e6e437dc9919c7eb0f3c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="runbook-input-parameters"></a>Paramètres d’entrée de Runbook

Les paramètres d’entrée de Runbook augmentent la flexibilité des Runbooks en vous permettant de lui transmettre des données lors de son démarrage. Les paramètres permettent que les actions du Runbook soient ciblées pour des scénarios et environnements spécifiques. Cet article explore différents scénarios dans lesquels des paramètres d’entrée sont utilisés dans des runbooks.

## <a name="configure-input-parameters"></a>Configurer les paramètres d’entrée

Des paramètres d’entrée peuvent être configurés dans des Runbooks PowerShell, PowerShell Workflow, Python et graphiques. Un Runbook peut avoir plusieurs paramètres avec différents types de données, ou aucun paramètre. Des paramètres d’entrée peuvent être obligatoires ou facultatifs, et vous pouvez affecter une valeur par défaut à des paramètres facultatifs. Vous pouvez affecter des valeurs aux paramètres d'entrée d'un Runbook lorsque vous le démarrez via l’une des méthodes disponibles. Ces méthodes incluent le démarrage d’un Runbook à partir du portail ou d’un service web. Vous pouvez également démarrer un Runbook en tant que Runbook enfant appelé en ligne dans un autre Runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Configurer les paramètres d’entrée dans des Runbooks PowerShell et PowerShell Workflow

Les runbooks PowerShell et [PowerShell Workflow](automation-first-runbook-textual.md) dans Azure Automation prennent en charge les paramètres d’entrée définis à l’aide des attributs suivants :  

| **Propriété** | **Description** |
|:--- |:--- |
| type |Requis. Type de données attendu pour la valeur du paramètre. Tout type .NET est valide. |
| NOM |Requis. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
| Obligatoire |facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous définissez cette valeur sur **$true**, une valeur doit être fournie lors du démarrage du Runbook. Si vous définissez cette valeur sur **$false**, une valeur est facultative. |
| Valeur par défaut |facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du runbook. Une valeur par défaut peut être définie pour tout paramètre, qui rend automatiquement le paramètre facultatif, indépendamment du paramètre Mandatory. |

Windows PowerShell prend en charge d’autres attributs de paramètres d’entrée que ceux répertoriés ici, tels que la validation, les alias et les jeux de paramètres. Toutefois, Azure Automation ne prend actuellement en charge que les paramètres d’entrée ci-dessus.

Une définition de paramètre dans des Runbooks PowerShell Workflow a la forme générale suivante, où plusieurs paramètres sont séparés par des virgules.

   ```powershell
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> Lorsque vous définissez des paramètres, si vous ne spécifiez pas l’attribut **Mandatory** , par défaut, le paramètre est considéré comme facultatif. De même, si l’un des paramètres des runbooks PowerShell Workflow possède la valeur par défaut, il est traité par PowerShell comme un paramètre facultatif, indépendamment de la valeur de l’attribut **Mandatory**.
> 
> 

En guise d’exemple, nous allons configurer les paramètres d’entrée pour un Runbook PowerShell Workflow qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un groupe de ressources. Ce Runbook comporte deux paramètres, comme illustré dans la capture d’écran ci-dessous : le nom de la machine virtuelle et le nom du groupe de ressources.

![PowerShell Workflow d’Automation](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Dans cette définition de paramètre, les paramètres **$VMName** et **$resourceGroupName** sont des paramètres simples de type chaîne. Toutefois, les Runbooks PowerShell et PowerShell Workflow prennent en charge tous les types, simples et complexes, tels que **object** ou **PSCredential** pour les paramètres d’entrée.

Si votre runbook comporte un paramètre d’entrée de type d’objet, utilisez une table de hachage PowerShell contenant des paires (nom, valeur) pour transmettre une valeur. Par exemple, si vous avez le paramètre suivant dans un Runbook :

     [Parameter (Mandatory = $true)]
     [object] $FullName

Vous pouvez transmettre la valeur suivante au paramètre :

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurer des paramètres d’entrée dans des Runbooks graphiques

Pour configurer un Runbook graphique avec des paramètres d’entrée, nous allons créer un [Runbook graphique](automation-first-runbook-graphical.md) qui renvoie des détails sur des machines virtuelles, soit une machine virtuelle unique, soit toutes les machines virtuelles au sein d’un groupe de ressources. La configuration d'un Runbook implique deux activités principales, comme décrit ci-dessous.

[**Authentifier des Runbooks avec un compte d’identification Azure**](automation-sec-configure-azure-runas-account.md) pour s’authentifier avec Azure.

[**Get-AzureRmVm**](https://msdn.microsoft.com/library/mt603718.aspx) pour récupérer les propriétés d’une machine virtuelle.

Vous pouvez utiliser l’activité [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) pour renvoyer les noms de machines virtuelles. L’activité **Get-AzureRmVm** accepte deux paramètres : le **nom de machine virtuelle** et le **nom du groupe de ressources**. Étant donné que ces paramètres peuvent nécessiter des valeurs différentes chaque fois que vous démarrez le Runbook, vous pouvez ajouter des paramètres d’entrée à votre Runbook. Voici les étapes pour ajouter des paramètres d’entrée :

1. Sélectionnez le Runbook graphique dans le panneau **Runbooks** , puis cliquez sur [**Modifier**](automation-graphical-authoring-intro.md).
2. Dans l’éditeur de Runbooks, cliquez sur **Entrée et sortie** pour ouvrir le panneau **Entrée et sortie**.
   
    ![Runbook graphique d’Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. Le panneau **Entrée et sortie** affiche la liste des paramètres d’entrée qui sont définis pour le Runbook. Dans ce panneau, vous pouvez ajouter un nouveau paramètre d’entrée ou modifier la configuration d’un paramètre d’entrée existant. Pour ajouter un nouveau paramètre pour le Runbook, cliquez sur **Ajouter une entrée** pour ouvrir le panneau **Paramètre d’entrée de Runbook**. Dans ce panneau, vous pouvez configurer les paramètres suivants :
   
   | **Propriété** | **Description** |
   |:--- |:--- |
   | NOM |Requis. Le nom du paramètre. Il doit être unique dans un Runbook, ne peut contenir que des lettres, des chiffres ou des caractères de soulignement, et doit commencer par une lettre. |
   | Description |facultatif. Description de l’objectif du paramètre d’entrée. |
   | type |facultatif. Type de données attendu pour la valeur du paramètre. Les types de paramètres pris en charge sont **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** et **Object**. Si un type de données n’est pas sélectionné, le type par défaut est **String**. |
   | Obligatoire |facultatif. Spécifie si une valeur doit être fournie pour le paramètre. Si vous choisissez **yes**, une valeur doit être fournie lors du démarrage du Runbook. Si vous choisissez **no**, aucune valeur n’est requise lors du démarrage du Runbook, et une valeur par défaut peut être définie. |
   | Valeur par défaut |facultatif. Spécifie une valeur à utiliser pour le paramètre si aucune valeur n’est transmise lors du démarrage du runbook. Une valeur par défaut peut être définie pour un paramètre qui n’est pas obligatoire. Pour définir une valeur par défaut, choisissez **Custom**. Cette valeur est utilisée, sauf si une autre valeur est fournie lorsque du démarrage du Runbook. Choisissez **None** si vous ne souhaitez pas fournir de valeur par défaut. |
   
    ![Ajouter une nouvelle entrée](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Créez deux paramètres avec les propriétés suivantes qui seront utilisées par l’activité **Get-AzureRmVm** :
   
   * **Paramètre 1 :**
     
     * Nom - VMName
     * Type : Chaîne
     * Obligatoire : Non
   * **Paramètre 2 :**
     
     * Nom : resourceGroupName
     * Type : Chaîne
     * Obligatoire : Non
     * Valeur par défaut : Personnalisé
     * Valeur par défaut personnalisée : \<Nom du groupe de ressources qui contient les machines virtuelles>
5. Une fois les paramètres ajoutés, cliquez sur **OK**. Vous pouvez maintenant les voir dans le panneau **Entrée et sortie**. Cliquez de nouveau sur **OK**, puis sur **Enregistrer** et **Publier** pour publier votre Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configurer des paramètres d’entrée dans des Runbooks Python

Contrairement aux runbooks PowerShell, PowerShell Workflow et graphiques, les runbooks Python ne prennent pas de paramètres nommés.
Tous les paramètres d’entrée sont analysés en tant que tableau de valeurs d’argument.
Vous accédez au tableau en important le module `sys` dans votre script Python, puis en utilisant le tableau `sys.argv`.
Il est important de noter que le premier élément du tableau, `sys.argv[0]`, est le nom du script, par conséquent, le premier paramètre d’entrée réel est `sys.argv[1]`.

Pour obtenir un exemple montrant comment utiliser les paramètres d’entrée dans un runbook Python, consultez [My first Python runbook in Azure Automation](automation-first-runbook-textual-python2.md) (Mon premier runbook Python dans Azure Automation).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Affecter des valeurs aux paramètres d’entrée dans des Runbooks

Vous pouvez transmettre des valeurs aux paramètres d’entrée de runbooks dans les cas suivants :

### <a name="start-a-runbook-and-assign-parameters"></a>Démarrer un Runbook et affecter des paramètres

Un Runbook peut être démarré de plusieurs façons : via le portail Azure, avec un WebHook, avec des applets de commande PowerShell, avec l’API REST ou avec le Kit de développement logiciel (SDK). Nous abordons ci-dessous différentes méthodes pour démarrer un Runbook et affecter des paramètres.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Démarrer un Runbook publié à l’aide du portail Azure et affecter des paramètres

Au [démarrage du runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), le panneau **Démarrer le runbook** s’ouvre. Vous pouvez y entrer les valeurs des paramètres que avez créés.

![Démarrer à l’aide du portail](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Dans l'étiquette située sous la zone d'entrée, vous pouvez voir les attributs qui ont été définis pour le paramètre. Les attributs incluent Obligatoire ou Facultatif, Type et Valeur par défaut. Dans la bulle d’aide en regard du nom de paramètre, vous pouvez voir toutes les informations clés dont vous avez besoin pour prendre des décisions sur les valeurs d’entrée du paramètre. Ces informations indiquent si un paramètre est obligatoire ou facultatif. Elles précisent également le type et la valeur par défaut (le cas échéant) et d’autres notes utiles.

> [!NOTE]
> Les paramètres de type String prennent en charge les valeurs de chaîne **vides** .  La valeur **[EmptyString]** dans la zone des paramètres d’entrée a pour effet de transmettre une chaîne vide au paramètre. De même, les paramètres de type String ne prennent pas en charge la transmission de valeurs **Null** . Si vous ne transmettez pas de valeur au paramètre de type String, PowerShell interprète sa valeur comme Null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Démarrer un Runbook publié à l’aide d’applets de commande PowerShell et affecter des paramètres

* **Applets de commande d’Azure Resource Manager :** vous pouvez démarrer un Runbook Automation créé dans un groupe de ressources à l’aide de l’applet de commande [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Exemple :**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Cmdlets du modèle de déploiement Azure Classic :** vous pouvez démarrer un runbook Automation créé dans un groupe de ressources par défaut à l’aide de [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Exemple :**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Lorsque vous démarrez un Runbook à l’aide d’applets de commande PowerShell, un paramètre par défaut, **MicrosoftApplicationManagementStartedBy**, est créé avec la valeur **PowerShell**. Vous pouvez afficher ce paramètre dans le panneau de **détails du travail** .  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Démarrer un Runbook à l’aide d’un Kit de développement logiciel (SDK) et affecter des paramètres

* **Méthode d’Azure Resource Manager :** vous pouvez démarrer un Runbook à l’aide du Kit de développement logiciel (SDK) d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Vous pouvez voir le code complet dans notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Méthode du modèle de déploiement Azure Classic :** vous pouvez démarrer un runbook à l’aide du Kit de développement logiciel (SDK) d’un langage de programmation. Voici un extrait de code C# permettant de démarrer un Runbook dans votre compte Automation. Vous pouvez voir le code complet dans notre [dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Pour lancer cette méthode, créez un dictionnaire pour stocker les paramètres de Runbook **VMName** et **resourceGroupName** ainsi que leurs valeurs. Démarrez ensuite le Runbook. Voici l’extrait de code C# permettant d’appeler la méthode définie ci-dessus.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Démarrer un Runbook à l’aide de l’API REST et affecter des paramètres
Vous pouvez créer et démarrer une tâche de runbook avec l’API REST Azure Automation à l’aide de la méthode **PUT** et de l’URI de requête suivant :

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

Dans l’URI de demande, remplacez les paramètres suivants :

* **subscription-id** : ID de votre abonnement Azure.  
* **cloud-service-name :** nom du service cloud auquel la demande doit être envoyée.  
* **automation-account-name :** nom de votre compte Automation hébergé dans le service cloud spécifié.  
* **job-id :** GUID de la tâche. Vous pouvez créer des GUID dans PowerShell à l’aide de la commande **[GUID]::NewGuid(). ToString()** .

Pour transmettre des paramètres à la tâche du Runbook, utilisez le corps de la demande. Il accepte les deux propriétés suivantes, fournies au format JSON :

* **Runbook Name :** obligatoire. Nom du Runbook pour le démarrage de la tâche.  
* **Runbook Parameters :** facultatif. Dictionnaire de la liste de paramètres au format (name, value) où name doit être un nom de type String, et valeur peut être toute valeur JSON valide.

Si vous souhaitez démarrer le Runbook **Get-AzureVMTextual** créé précédemment avec **VMName** et **resourceGroupName** en tant que paramètres, utilisez le format JSON suivant pour le corps de la demande.

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Un code d’état HTTP 201 est retourné si la tâche est correctement créée. Pour plus d’informations sur les en-têtes et les corps de réponse, consultez l'article [Guide pratique pour créer une tâche de runbook à l'aide de l'API REST](https://msdn.microsoft.com/library/azure/mt163849.aspx).

### <a name="test-a-runbook-and-assign-parameters"></a>Tester un Runbook et affecter des paramètres
Lorsque vous [testez le brouillon de votre runbook](automation-testing-runbook.md) à l’aide de l’option de test, la page **Test** s’ouvre, et vous pouvez y configurer les valeurs des paramètres que vous avez créés.

![Tester et affecter des paramètres](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Lier une planification à un Runbook et affecter des paramètres
Vous pouvez [lier une planification](automation-schedules.md) à votre Runbook afin de démarrer celui-ci à un moment spécifique. Lors de la création de la planification, vous affectez des paramètres d’entrée, que le runbook utilise quand la planification le démarre. Vous ne pouvez pas enregistrer la planification tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Planifier et affecter des paramètres](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Créer un WebHook pour un Runbook et affecter des paramètres
Vous pouvez créer un [WebHook](automation-webhooks.md) pour votre Runbook, puis configurer les paramètres d’entrée de celui-ci. Vous ne pouvez pas enregistrer le WebHook tant que toutes les valeurs de paramètres obligatoires n’ont pas été fournies.

![Créer un WebHook et affecter des paramètres](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Lorsque vous exécutez un Runbook à l’aide d’un WebHook, un paramètre d’entrée prédéfini, **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , est envoyé avec les paramètres d’entrée que vous avez définis. Pour plus de détails, vous pouvez cliquer sur le paramètre **WebhookData** pour le développer.

![Paramètre WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’entrée et la sortie du Runbook, voir [Azure Automation : entrée et sortie de Runbook, et Runbook imbriqués](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)
* Pour plus d’informations sur les différentes façons de démarrer un Runbook, voir [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour modifier un Runbook textuel, voir [Modification des Runbooks textuels](automation-edit-textual-runbook.md).
* Pour modifier un Runbook graphique, voir [Création graphique dans Azure Automation](automation-graphical-authoring-intro.md).

