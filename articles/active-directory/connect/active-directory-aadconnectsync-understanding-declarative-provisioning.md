---
title: "Azure AD Connect : Présentation de l’approvisionnement déclaratif | Microsoft Docs"
description: "Explique le modèle de configuration de l’approvisionnement déclaratif dans Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 50fce526d667fa829551425edff4bd3863429ef2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect Sync : présentation de l’approvisionnement déclaratif
Cette rubrique présente le modèle de configuration dans Azure AD Connect. Ce modèle est appelé « approvisionnement déclaratif » et vous permet de modifier la configuration en toute simplicité. De nombreux éléments décrits dans cette rubrique sont des éléments avancés, non indispensables pour la plupart des scénarios clients.

## <a name="overview"></a>Vue d’ensemble
L’approvisionnement déclaratif correspond au traitement des objets provenant d’un répertoire source connecté. Il détermine comment l’objet et les attributs doivent être transformés à partir d’une source vers une cible. Les objets sont traités dans un pipeline de synchronisation identique pour les règles de trafic entrant et sortant. Les règles de trafic entrant vont d’un espace de connecteur au métaverse et les règles de trafic sortant vont du métaverse vers un espace de connecteur.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Le pipeline a plusieurs modules. Chacun d’eux est responsable d’un concept de synchronisation des objets.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Source, l’objet source
* [Scope](#scope), recherche toutes les règles de synchronisation dans la portée
* [Join](#join), détermine la relation entre l’espace de connecteur et le métaverse
* [Transform](#transform), calcule comment les attributs doivent être transformés et flux
* [Precedence](#precedence), résout les contributions d’attribut conflictuelles
* Target, l’objet cible

## <a name="scope"></a>Étendue
Le module Scope évalue un objet et détermine les règles qui sont dans la portée et doivent être incluses lors du traitement. En fonction des valeurs d’attributs de l’objet, différentes règles de synchronisation sont évaluées pour être dans la portée. Par exemple, un utilisateur désactivé sans boîte aux lettres Exchange possède des règles différentes d’un utilisateur activé avec une boîte aux lettres.  
![Étendue](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

La portée est définie selon des groupes et des clauses. Les clauses sont à l’intérieur des groupes. Un opérateur logique AND est utilisé entre toutes les clauses d’un groupe. Par exemple, (department = IT AND country = Denmark). Un opérateur logique OR est utilisé entre les groupes.

![Étendue](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
La portée de cette image doit être lue comme (department = IT AND country = Denmark) OR (country = Sweden). Si le groupe 1 ou le groupe 2 est évalué comme true, la règle est dans la portée.

Le module Scope prend en charge les opérations suivantes.

| Opération | Description |
| --- | --- |
| EQUAL, NOTEQUAL |Comparaison de chaînes qui évalue si la valeur est égale à la valeur de l’attribut. Pour les attributs à valeurs multiples, consultez ISIN et ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Comparaison de chaînes qui évalue si la valeur est inférieure à la valeur de l’attribut. |
| CONTAINS, NOTCONTAINS |Comparaison de chaînes qui évalue si la valeur se trouve dans la valeur de l’attribut. |
| STARTSWITH, NOTSTARTSWITH |Comparaison de chaînes qui évalue si la valeur est au début de la valeur de l’attribut. |
| ENDSWITH, NOTENDSWITH |Comparaison de chaînes qui évalue si la valeur est à la fin de la valeur de l’attribut. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Comparaison de chaînes qui évalue si la valeur est supérieure à la valeur de l’attribut. |
| ISNULL, ISNOTNULL |Évalue si l’attribut est absent de l’objet. Si l’attribut n’est pas présent et par conséquent « null », la règle est dans la portée. |
| ISIN, ISNOTIN |Évalue si la valeur est présente dans l’attribut défini. Cette opération est la variation à valeurs multiples des opérations EQUAL et NOTEQUAL. L’attribut est censé être un attribut à valeurs multiples et si la valeur se trouve dans une des valeurs d’attribut, alors la règle est dans la portée. |
| ISBITSET, ISNOTBITSET |Évalue si un bit particulier est défini. Peut par exemple être utilisé pour évaluer les bits dans userAccountControl pour voir si un utilisateur est activé ou désactivé. |
| ISMEMBEROF, ISNOTMEMBEROF |La valeur doit contenir un nom unique vers un groupe dans l’espace de connecteur. Si l’objet est membre du groupe spécifié, la règle est dans la portée. |

## <a name="join"></a>Join
Le module Join dans le pipeline de synchronisation est chargé de rechercher la relation entre l’objet de la source et un objet dans la cible. Sur une règle de trafic entrant, cette relation serait un objet dans un espace de connecteur ayant une relation avec un objet dans le métaverse.  
![Jointure entre cs et mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
L’objectif est de voir si une relation doit être établie avec un objet créé par un autre connecteur et se trouvant déjà dans le métaverse. Par exemple, dans une forêt de ressources de comptes, l’utilisateur de la forêt de comptes doit être associé à l’utilisateur de la forêt de ressources.

Les jointures sont principalement utilisées sur les règles de trafic entrant, pour joindre les objets d’espace de connecteur au même objet dans le métaverse.

Les jointures sont définies comme un ou plusieurs groupes. À l’intérieur d’un groupe, il existe des clauses. Un opérateur logique AND est utilisé entre toutes les clauses d’un groupe. Un opérateur logique OR est utilisé entre les groupes. Les groupes sont traités de haut en bas. Lorsqu’un groupe a trouvé une correspondance exacte avec un objet dans la cible, aucune autre règle de jointure n’est évaluée. Si aucun ou plus d’un objet est trouvé, le traitement continue pour le groupe de règles suivant. Pour cette raison, les règles doivent être créées dans l’ordre, de la plus explicite à la moins explicite.  
![Définition de jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Les jointures dans cette image sont traitées de haut en bas. Le pipeline de synchronisation détecte d’abord si une correspondance sur employeeID existe. Si ce n’est pas le cas, la deuxième règle détecte si le nom du compte peut être utilisé pour joindre les objets. Si aucune correspondance n’est trouvée, la troisième et dernière règle utilise le nom d’utilisateur pour trouver une correspondance moins stricte.

Si toutes les règles de jointure ont été évaluées et qu’il n’existe aucune correspondance exacte, le **type de lien** indiqué dans la page de **description** est utilisé. Si cette option a la valeur **Provision**, un nouvel objet est créé dans la cible.  
![Approvisionnement ou jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un objet doit avoir une seule règle de synchronisation avec des règles de jointure dans la portée. S’il existe plusieurs règles de synchronisation dans lesquelles la jointure est définie, une erreur se produit. La précédence n’est pas utilisée pour résoudre les conflits de jointure. Un objet doit avoir une règle de jointure dans la portée des attributs pour que la circulation se fasse dans le même sens en entrée et en sortie. Si vous avez besoin de faire circuler les attributs en entrée et en sortie sur le même objet, vous devez disposer à la fois d’une règle de synchronisation de trafic entrant et de trafic sortant avec une jointure.

La jointure en sortie a un comportement spécial lorsqu’elle tente d’approvisionner un objet sur un espace de connecteur cible. L’attribut de nom unique est utilisé pour essayer en premier lieu une jointure inverse. Si l’espace de connecteur cible contient déjà un objet portant le même nom unique, les objets sont joints.

Le module Join est évalué une seule fois lorsqu’une nouvelle règle de synchronisation arrive dans l’étendue. Lorsqu’un objet est joint, il n’est pas séparé, même si le critère de jointure n’est plus rempli. Si vous souhaitez séparer un objet, la règle de synchronisation qui joint les objets doit être hors de portée.

### <a name="metaverse-delete"></a>Suppression du métaverse
Un objet de métaverse demeure tant qu’une règle de synchronisation reste dans la portée, avec le**type de lien** défini sur **Provision** ou **StickyJoin**. Le type StickyJoin est utilisé lorsqu’un connecteur n’est pas autorisé à approvisionner un nouvel objet dans le métaverse, mais lorsqu’il est joint, il doit être supprimé de la source avant la suppression de l’objet du métaverse.

Lorsqu’un objet de métaverse est supprimé, tous les objets associés à une règle de synchronisation de trafic sortant marquée **Provision** sont marqués pour suppression.

## <a name="transformations"></a>Transformations
Les transformations sont utilisées pour définir le flux d’attributs de la source vers la cible. Les flux peuvent être des **types**suivants : Direct, Constant ou Expression. Un flux direct envoie la valeur de l’attribut telle quelle, sans transformation supplémentaire. Un flux constant définit la valeur spécifiée. Une expression utilise le langage d’expression d’approvisionnement déclaratif pour exprimer la manière dont la transformation doit avoir lieu. Vous trouverez des informations sur le langage d’expression dans la rubrique [Comprendre le langage d’expression d’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Approvisionnement ou jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La case **Appliquer une fois** indique si l’attribut doit être défini uniquement lors de la création initiale de l’objet. Par exemple, cette configuration peut être utilisée pour définir un mot de passe initial pour un nouvel objet utilisateur.

### <a name="merging-attribute-values"></a>Fusion de valeurs d’attribut
Dans les flux d’attributs, il existe un paramètre permettant de déterminer si les attributs à valeurs multiples doivent être fusionnés à partir de plusieurs connecteurs différents. La valeur par défaut est **Mettre à jour**, ce qui indique que la règle de synchronisation avec la priorité la plus élevée prévaut.

![Types de fusion](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Il existe également une option **Fusionner** et **MergeCaseInsensitive** (Fusion non sensible à la casse). Ces options permettent de fusionner des valeurs issues de différentes sources. Par exemple, elles peuvent être utilisées pour fusionner le membre ou l’attribut proxyAddresses de plusieurs forêts différentes. Lorsque vous utilisez cette option, toutes les règles de synchronisation dans l’étendue d’un objet doivent utiliser le même type de fusion. Vous ne pouvez pas définir **Mettre à jour** à partir d’un connecteur et **Fusionner** à partir d’un autre connecteur. Si vous essayez, vous recevrez une erreur.

La différence entre **Fusionner** et **MergeCaseInsensitive** (Fusion non sensible à la casse) est le traitement des valeurs d’attribut en double. Le moteur de synchronisation s’assure qu’aucun doublon n’est inséré dans l’attribut cible. Avec **MergeCaseInsensitive**(Fusion non sensible à la casse), les doublons présentant uniquement une différence de casse ne sont pas inclus. Par exemple, vous ne verrez pas à la fois « SMTP:bob@contoso.com » et « smtp:bob@contoso.com » dans l’attribut cible. **Fusionner** examine uniquement les valeurs exactes et plusieurs valeurs présentant uniquement une différence de casse peuvent être présentes.

L’option **Remplacer** est identique à **Mettre à jour**, mais elle n’est pas utilisée.

### <a name="control-the-attribute-flow-process"></a>Contrôler le processus de flux d’attributs
Lorsque plusieurs règles de synchronisation entrantes sont configurées pour contribuer au même attribut du métaverse, un principe de priorité est utilisé pour déterminer celle qui sera appliquée. La règle de synchronisation ayant la priorité la plus élevée (la valeur numérique la plus basse) transmettra sa valeur. Le même principe s’applique pour les règles sortantes. La règle de synchronisation ayant la priorité la plus élevée l’emporte et transmet sa valeur au répertoire connecté.

Dans certains cas, plutôt que de transmettre une valeur, la règle de synchronisation doit déterminer le comportement des autres règles. Certains littéraux particuliers sont utilisés dans ce cas.

Pour les règles de synchronisation entrantes, le littéral **NULL** peut être utilisé pour indiquer que le flux n’a aucune valeur à transmettre. Une autre règle avec une priorité inférieure peut transmettre sa valeur. Si aucune règle n’a contribué de valeur, l’attribut du métaverse est supprimé. Pour une règle sortante, si **NULL** est la valeur finale obtenue une fois toutes les règles de synchronisation traitées, la valeur est alors supprimée du répertoire connecté.

Le littéral **AuthoritativeNull** est similaire à **NULL**, à ceci près qu’aucune des règles de priorité inférieure ne peut transmettre une valeur.

Un flux d’attributs peut également utiliser le littéral **IgnoreThisFlow**. Celui-ci est similaire à la valeur NULL en ce sens qu’il indique qu’il n’a rien à transmettre. En revanche, il ne supprime aucune valeur déjà existante dans la cible. Il agit comme si le flux d’attributs n’avait jamais existé.

Voici un exemple : 

Dans *Out to AD - User Exchange hybrid*, vous trouverez le flux suivant :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Cette expression doit être lue de la manière suivante : si la boîte aux lettres de l’utilisateur se trouve dans Azure AD, transmettre l’attribut d’Azure AD à Active Directory. Si ce n’est pas le cas, ne rien transmettre en retour à Active Directory. Dans ce cas, la valeur existante dans AD est conservée.

### <a name="importedvalue"></a>ImportedValue
La fonction ImportedValue est différente de toutes les autres fonctions, car le nom d’attribut doit être placé entre guillemets doubles plutôt qu’entre crochets :   
`ImportedValue("proxyAddresses")`.

Généralement, lors de la synchronisation, un attribut utilise la valeur attendue, même s’il n’a pas encore été exporté ou si une erreur a été reçue pendant l’exportation (« top of the tower »). Une synchronisation entrante part du principe qu’un attribut qui n’a pas encore atteint un annuaire connecté finira par l’atteindre. Dans certains cas, il est important de synchroniser uniquement une valeur qui a été confirmée par l’annuaire connecté (« hologram and delta import tower »).

Vous trouverez un exemple de cette fonction dans la règle de synchronisation par défaut *In from AD – User Common from Exchange*. Dans Exchange hybride, la valeur ajoutée par Exchange Online doit uniquement être synchronisée après avoir confirmé que la valeur a bien été exportée :   
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedence
Lorsque plusieurs règles de synchronisation essaient de transmettre la même valeur d’attribut à la cible, la valeur de précédence est utilisée pour déterminer la valeur prioritaire. La règle ayant la priorité la plus élevée (la valeur numérique la plus basse) transmettra l’attribut.

![Types de fusion](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Cette commande permet de définir plus précisément les flux d’attributs pour un petit sous-ensemble d’objets. Par exemple, les règles out-of-box permettent de s’assurer que les attributs d’un compte activé (**User AccountEnabled**) ont la priorité sur d’autres comptes.

La précédence peut être définie entre les connecteurs. Cela permet aux connecteurs avec de meilleures données de transmettre leurs valeurs en premier.

### <a name="multiple-objects-from-the-same-connector-space"></a>Plusieurs objets du même espace de connecteur
Si vous avez plusieurs objets dans le même espace de connecteur joints au même objet de métaverse, vous devez ajuster la précédence. Si plusieurs objets sont dans la portée de la même règle de synchronisation, le moteur de synchronisation n’est pas en mesure de déterminer la précédence. Il demeure une incertitude quant à l’objet source qui doit transmettre la valeur au métaverse. Cette configuration est signalée comme ambiguë même si les attributs de la source ont la même valeur.  
![Plusieurs objets joints au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Pour ce scénario, vous devez modifier la portée des règles de synchronisation, de façon à ce que les objets sources aient des règles de synchronisation différentes dans la portée. Cela vous permet de définir une précédence différente.  
![Plusieurs objets joints au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>étapes suivantes
* En savoir plus sur le langage d’expression dans [Comprendre les expressions d’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Apprendre comment l’approvisionnement déclaratif est utilisé out-of-box dans [Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).
* Apprendre à effectuer une modification pratique à l’aide de l’approvisionnement déclaratif dans [Comment modifier la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).
* Continuer à lire le fonctionnement des utilisateurs et des contacts dans [Présentation des utilisateurs et des Contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

**Rubriques de référence**

* [Azure AD Connect Sync : Référence aux fonctions](active-directory-aadconnectsync-functions-reference.md)
