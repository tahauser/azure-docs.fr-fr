---
title: "Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center [préversion] | Microsoft Docs"
description: "Cet article explique comment personnaliser les évaluations de Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center [préversion]

Découvrez dans ce guide comment personnaliser les évaluations de la configuration de la sécurité du système d’exploitation dans Azure Security Center.

## <a name="what-are-os-security-configurations"></a>À quoi correspondent les configurations de la sécurité du système d’exploitation ?

Azure Security Center effectue le monitoring des configurations de la sécurité selon un jeu de plus de 150 règles recommandées pour renforcer le système d’exploitation et relatives aux pare-feu, aux audits, aux stratégies de mot de passe et bien plus encore. Si une configuration vulnérable est identifiée sur un ordinateur, une recommandation de sécurité est générée.

En personnalisant les règles, les organisations ont la possibilité de décider des options de configuration les mieux adaptées à leur environnement. Cette fonctionnalité permet aux utilisateurs de définir une stratégie d’évaluation personnalisée et de l’appliquer à tous les ordinateurs candidats de l’abonnement.

> [!NOTE]
> - Actuellement, la personnalisation de la configuration de la sécurité du système d’exploitation est uniquement disponible pour les systèmes d’exploitation Windows Server 2008, 2008 R2, 2012 et 2012 R2.
- La configuration s’applique à l’ensemble des machines virtuelles et des ordinateurs connectés à tous les espaces de travail de l’abonnement sélectionné.
- La personnalisation de la configuration de la sécurité du système d’exploitation est disponible uniquement sur le niveau Standard de Security Center.
>
>

Comment personnaliser les règles de configuration de la sécurité du système d’exploitation ?

Vous pouvez personnaliser les règles de configuration de la sécurité du système d’exploitation en activant ou en désactivant une règle en particulier, en modifiant le paramètre souhaité d’une règle existante ou en ajoutant une nouvelle règle selon les types pris en charge (registre, stratégie d’audit et stratégie de sécurité). Actuellement, le paramètre souhaité doit avoir une valeur exacte.

Les nouvelles règles doivent avoir le même format et la même structure que les autres règles existantes du même type.

> [!NOTE]
> Pour personnaliser les configurations de la sécurité du système d’exploitation, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.
>
>

## <a name="customize-security-configuration"></a>Personnaliser la configuration de la sécurité

Pour personnaliser la configuration par défaut de la sécurité du système d’exploitation dans Security Center :

1.  Ouvrez le tableau de bord **Security Center**.

2.  Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.  La fenêtre **Security Center - Stratégie de sécurité** s’ouvre.

3.  Sélectionnez l’abonnement sur lequel portera la personnalisation.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. Dans **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Modifier les configurations de la sécurité**.

6.  La fenêtre **Modifier les configurations de la sécurité** s’ouvre. Suivez les étapes qui s’affichent à l’écran pour télécharger, modifier et charger le fichier modifié.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > Par défaut, le fichier de configuration téléchargé est au format *json*. Vous trouverez les instructions à suivre pour modifier ce fichier dans la rubrique [Personnaliser le fichier de configuration](#customize-the-configuration-file).
  >

7. Une fois le fichier enregistré, la configuration s’applique à l’ensemble des machines virtuelles et des ordinateurs connectés à tous les espaces de travail de l’abonnement sélectionné. Ce processus est susceptible de prendre un certain temps, généralement quelques minutes, mais parfois plus en fonction de la taille de l’infrastructure. Sélectionnez **Enregistrer** pour valider la modification ; sinon, la stratégie n’est pas stockée.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

À tout moment, vous pouvez réinitialiser la configuration actuelle de la stratégie à l’état par défaut en sélectionnant l’option **Réinitialiser** dans **Modifier les règles de configuration de la sécurité du système d’exploitation**. Si vous choisissez cette option, vous recevrez l’alerte contextuelle suivante. Sélectionnez **Oui** pour confirmer.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personnaliser le fichier de configuration

Dans le fichier de personnalisation, chaque version de système d’exploitation prise en charge a un ensemble de règles (ruleset). Chacun a son propre nom et un ID unique, comme dans l’exemple suivant :

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ce fichier a été modifié avec Visual Studio, mais vous pouvez également utiliser le Bloc-notes à condition que le plug-in JSON Viewer soit installé.
>
>

Lorsque vous éditez ce fichier, vous pouvez modifier une règle ou la totalité d’entre elles. Chaque ensemble de règles comprend une section *rules* qui contient les règles, réparties en trois catégories : Registre, Stratégie d’audit et Stratégie de sécurité, comme ci-dessous :

![](media/security-center-customize-os-security-config/rules-section.png)

Chaque catégorie possède son propre ensemble d’attributs. Vous pouvez apporter des modifications aux attributs suivants des règles existantes :

- expectedValue : le type de données du champ de cet attribut doit correspondre aux valeurs prises en charge par chaque type de règle, par exemple :

  - baselineRegistryRules : la valeur doit correspondre à [regValueType] (https://msdn.microsoft.com/library/windows/desktop/ms724884(v=vs.85)), défini dans cette règle.

  - baselineAuditPolicyRules : la valeur doit être l’une des chaînes suivantes :

    - Success and Failure (« Succès et échec »)

    - Succès

  - baselineSecurityPolicyRules : la valeur doit être l’une des chaînes suivantes :

    - « No one » (« Aucune »)

    - Liste des groupes d’utilisateurs autorisés, par exemple : « Administrators, Backup Operators » (« Administrateurs, opérateurs de sauvegarde »).

-   state : chaîne qui peut contenir l’option « Désactivé » ou « Activé ». Dans cette préversion privée, la chaîne respecte la casse.

Ce sont les seuls champs configurables. Si vous ne respectez pas le format ou la taille de fichier, vous ne pourrez pas enregistrer la modification. Le message d’erreur suivant apparaît si le fichier n’a pas pu être traité :

![](media/security-center-customize-os-security-config/invalid-json.png)

Consultez la rubrique [Codes d’erreur](#error-codes) pour connaître la liste des erreurs potentielles.

Voici quelques exemples de règles et d’attributs (en gras) modifiables :

**Section de règles :** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**Section de règles :** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**Section de règles :** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

Certaines règles sont dupliquées pour les différents types de systèmes d’exploitation. Les règles en double ont le même « originalId ».

## <a name="adding-a-new-custom-rule"></a>Ajouter une nouvelle règle personnalisée

Vous pouvez également créer une règle. Au préalable, gardez à l’esprit les restrictions suivantes :

-   La version du schéma, *baselineId* et *baselineName* ne sont pas modifiables.

-   Il n’est pas possible de supprimer un ensemble de règles.

-   Il n’est pas possible d’ajouter un ensemble de règles.

-   Le nombre maximal de règles autorisées (y compris les règles par défaut) est de 1 000 règles.

Les nouvelles règles personnalisées sont marquées par une nouvelle source personnalisée (!= « Microsoft »). Le champ *ruleId* peut être Null ou vide. S’il est vide, Microsoft en génère un. Sinon, il doit avoir un GUID valide et commun à toutes les règles (par défaut et personnalisées). Prenez connaissance des contraintes ci-dessous concernant les champs de base :

-   *originalId* : peut être Null ou vide. Si *non vide*, doit être un GUID valide.

-   *cceId* : peut être Null ou vide. Si *non vide*, doit être unique.

-   *ruleType* : Registry, AuditPolicy ou SecurityPolicy.

-   *Severity* : Unknown, Critical, Warning ou Informational.

-   *analyzeOperation* : doit être égal.

-   *auditPolicyId* : doit être un GUID valid.

-   *regValueType* : Int, Long, String ou MultipleString.

> [!NOTE]
> Hive doit avoir la valeur *LocalMachine*.
>
>

Exemple de nouvelle règle personnalisée :

**Registry** :
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**Security policy** :
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Audit policy** :
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Échecs de chargement de fichier

Si le fichier de configuration soumis n’est pas valide en raison d’erreurs dans les valeurs ou dans la mise en forme, une erreur d’échec s’affiche. Il est possible de télécharger un rapport d’erreurs CSV détaillé pour résoudre et corriger les erreurs avant de soumettre un fichier de configuration corrigé.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Exemple de fichier d’erreurs :

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Codes d’erreur

La liste ci-dessous présente toutes les erreurs potentielles :

| **Error**                                | **Description**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | La propriété « schemaVersion » est vide ou non valide. Elle doit avoir la valeur « {0} ».                                                         |
| BaselineInvalidStringError               | La propriété « {0} » ne peut pas contenir « \\n ».                                                                                                         |
| BaselineNullRuleError                    | La liste de règles de configuration de base contient une règle dont la valeur est « Null ».                                                                         |
| BaselineRuleCceIdNotUniqueError          | Le CCE-ID « {0} » n’est pas unique.                                                                                                                  |
| BaselineRuleEmptyProperty                | La propriété « {0} » est manquante ou non valide.                                                                                                       |
| BaselineRuleIdNotInDefault               | La règle a comme propriété de source « Microsoft », mais est introuvable dans l’ensemble de règles Microsoft par défaut.                                                   |
| BaselineRuleIdNotUniqueError             | L’ID de règle n’est pas unique.                                                                                                                       |
| BaselineRuleInvalidGuid                  | La propriété « {0} » n’est pas valide. La valeur n’est pas un GUID valide.                                                                             |
| BaselineRuleInvalidHive                  | Hive doit avoir la valeur LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Le nom de règle n’est pas unique.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | La règle est introuvable dans la nouvelle configuration. La règle n’est pas supprimable.                                                                     |
| BaselineRuleNotFoundError                | La règle est introuvable dans l’ensemble de règles de base par défaut.                                                                                        |
| BaselineRuleNotInPlace                   | La règle correspond à une règle par défaut possédant le type {0} et figure dans la liste {1}.                                                                       |
| BaselineRulePropertyTooLong              | La propriété « {0} » est trop longue. Longueur maximale autorisée : {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | La valeur attendue « {0} » ne correspond pas au type valeur de registre défini.                                                              |
| BaselineRulesetAdded                     | L’ensemble de règles correspondant à l’ID « {0} » est introuvable dans la configuration par défaut. Il n’est pas possible d’ajouter un ensemble de règles.                                               |
| BaselineRulesetIdMustBeUnique            | L’ensemble de règles de base spécifié « {0} » doit être unique.                                                                                           |
| BaselineRulesetNotFound                  | L’ensemble de règles correspondant à l’ID « {0} » et au nom « {1} » est introuvable dans la configuration spécifiée. Il n’est pas possible de supprimer un ensemble de règles.                                |
| BaselineRuleSourceNotMatch               | Une règle correspondant à l’ID « {0} » est déjà définie.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Le type de règle par défaut est « {0} ».                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Le type réel de la règle est {0}, alors que la propriété ruleType est {1}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Seules les propriétés « expectedValue » et « state » sont modifiables.                                                                       |
| BaselineTooManyRules                     | Le nombre maximal de règles personnalisées autorisées est de {0} règles. La configuration spécifiée contient {1} règles ({2} règles par défaut + {3} règles personnalisées). |
| ErrorNoConfigurationStatus               | Aucun état de configuration n’a été trouvé. Veuillez indiquer l’état de configuration souhaité : « Default » (« par défaut ») ou « Custom » (« personnalisé »).                                    |
| ErrorNonEmptyRulesetOnDefault            | L’état de la configuration a la valeur par défaut. La liste BaselineRulesets doit être Null ou vide.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | L’état de configuration indiqué est « Custom » (« personnalisé »), alors que la propriété « baselineRulesets » est Null ou vide.                                             |
| ErrorParsingBaselineConfig               | La configuration spécifiée n’est pas valide. Au moins une des valeurs définies est Null ou a un type non valide.                                  |
| ErrorParsingIsDefaultProperty            | La valeur « configurationStatus » spécifiée, « {0} », n’est pas valide. Elle ne peut être que « Default » (« par défaut ») ou « Custom » (« personnalisé »).                                         |
| InCompatibleViewVersion                  | La version de l’affichage {0} n’est PAS prise en charge sur ce type d’espace de travail.                                                                                   |
| InvalidBaselineConfigurationGeneralError | La configuration de base spécifiée comporte une ou plusieurs erreurs de validation de type.                                                          |
| ViewConversionError                      | L’affichage correspond à une ancienne version qui était prise en charge par cet espace de travail. La conversion de l’affichage a échoué : {0}.                                                                 |

Si vous ne disposez pas d’autorisations suffisantes, vous risquez de recevoir une erreur d’échec général :

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>étapes suivantes
Cet article vous a montré comment personnaliser les évaluations de la configuration de la sécurité du système d’exploitation dans Security Center. Pour en savoir plus sur les règles de configuration et sur la correction, consultez les pages :

- [Règles de base et identificateurs de configuration courants de Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques pour les règles de configuration. Visitez le site [CCE](https://nvd.nist.gov/config/cce/index) pour plus d’informations.
- [Corriger les configurations de la sécurité](security-center-remediate-os-vulnerabilities.md) explique comment résoudre les vulnérabilités lorsque la configuration du système d’exploitation ne correspond pas aux règles de configuration de la sécurité recommandées.
