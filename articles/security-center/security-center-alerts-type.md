---
title: "Alertes de sécurité par type dans Azure Security Center | Microsoft Docs"
description: "Cet article décrit les différents types d’alertes de sécurité disponibles dans Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2017
ms.author: yurid
ms.openlocfilehash: 829657664cf1e37b22d57c62614300a205b5e91c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Présentation des alertes de sécurité dans Azure Security Center
Cet article vous aide à comprendre les différents types d’alertes de sécurité et les informations associées disponibles dans Azure Security Center. Pour plus d’informations sur la gestion des alertes et des incidents, consultez l’article [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).

Pour configurer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. Une version d’évaluation gratuite de 60 jours est disponible. Pour mettre à niveau, sélectionnez **Niveau tarifaire** sous [Stratégie de sécurité](security-center-policies.md). Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Security Center a sorti avec les versions préliminaires limitées un nouvel ensemble de moyens de détection qui exploitent les enregistrements d’audit, une infrastructure d’audit courante, pour détecter des comportements malveillants sur les machines Linux. [Envoyez-nous](mailto:ASC_linuxdetections@microsoft.com) un e-mail avec vos ID d’abonnement pour joindre la version préliminaire.

## <a name="what-type-of-alerts-are-available"></a>Quels types d’alertes sont disponibles ?
Azure Security Center utilise diverses [fonctionnalités de détection](security-center-detection-capabilities.md) pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Les informations figurant dans une alerte varient en fonction du type d’analyse utilisé pour détecter la menace. Les incidents sont également susceptibles de contenir des informations contextuelles supplémentaires qui peuvent se révéler utiles lors de l’étude d’une menace.  Cet article fournit des informations sur les types d’alertes suivants :

* Analyse comportementale de la machine virtuelle (VMBA)
* Analyse du réseau
* Analyse des ressources
* Informations contextuelles

## <a name="virtual-machine-behavioral-analysis"></a>Analyse comportementale de la machine virtuelle
Azure Security Center peut utiliser l’analyse comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux d’événements. Par exemple, les événements de création de processus et les événements de connexion. En outre, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée.

> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, voir [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).
>

### <a name="crash-analysis"></a>Analyse des incidents
L’analyse de la mémoire de vidage sur incident est une méthode utilisée pour détecter les programmes malveillants sophistiqués capables d’échapper aux solutions de sécurité traditionnelles. Différentes formes de programmes malveillants tentent de réduire le risque de détection par des produits antivirus en n’écrivant jamais sur le disque ou en chiffrant les composants logiciels écrits sur le disque. Cette technique rend les programmes malveillants difficiles à détecter à l’aide des logiciels anti-programmes malveillants traditionnels. Toutefois, ce type de logiciel malveillant peut être détecté à l’aide de l’analyse de mémoire, car le programme malveillant laisse des traces en mémoire pour pouvoir fonctionner.

Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident. L’incident peut être provoqué par un programme malveillant, une application générale ou des problèmes système. En analysant la mémoire dans le vidage sur incident, Azure Security Center peut détecter les techniques utilisées pour exploiter la vulnérabilité des logiciels, accéder aux données confidentielles et persister subrepticement au sein d’un ordinateur compromis. Ces opérations ont peu d’impact sur les performances des hôtes, car l’analyse est effectuée au cœur d’Azure Security Center.

Les champs suivants sont communs aux exemples d’alerte de vidage sur incident qui apparaissent plus loin dans cet article :

* DUMPFILE : nom du fichier de vidage sur incident.
* PROCESSNAME : nom du processus bloqué.
* PROCESSVERSION : version du processus bloqué.

### <a name="code-injection-discovered"></a>Injection de code détectée
Une injection de code est l’insertion de modules exécutables dans des processus ou threads en cours d’exécution.  Cette technique est utilisée par les programmes malveillants pour accéder aux données, masquer ou empêcher leur suppression (par exemple, la persistance). Cette alerte indique qu’un module injecté est présent dans l’image mémoire. Les développeurs de logiciels légitimes procèdent parfois à une injection de code pour des raisons non malveillantes, comme la modification ou l’extension d’une application existante ou d’un composant de système d’exploitation.  Pour distinguer les modules injectés malveillants et non malveillants, Azure Security Center vérifie si le module injecté est conforme à un profil de comportement suspect. Le résultat de cette vérification est indiqué par le champ « SIGNATURE » de l’alerte et est répercuté dans le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte. 

Cette alerte présente les champs supplémentaires suivants :

- ADRESSE : emplacement du module injecté dans la mémoire
- NOM DE L’IMAGE : nom du module injecté. Notez que ce champ peut être vide si le nom de l’image n’est pas fourni dans l’image.
- SIGNATURE : indique si le module injecté est conforme à un profil de comportement suspect. 

Le tableau ci-dessous répertorie des exemples de résultats et leur description :

| Valeur de la signature                      | Description                                                                                                       |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Exploit de chargeur par réflexion suspect | Ce comportement suspect est souvent corrélé au chargement de code injecté indépendamment du chargeur du système d’exploitation |
| Exploit injecté suspect          | Révèle une malveillance souvent corrélée à l’injection de code dans la mémoire                                       |
| Exploit d’injection suspect         | Révèle une malveillance souvent corrélée à l’utilisation du code injecté dans la mémoire                                   |
| Exploit de débogueur injecté suspect | Révèle une malveillance souvent corrélée à la détection ou au contournement d’un débogueur                         |
| Exploit distant injecté suspect   | Révèle une malveillance souvent corrélée aux scénarios de contrôle et de commande (C2)                                 |

Voici un exemple de ce type d’alerte :

![Alerte d’injection de code](./media/security-center-alerts-type/security-center-alerts-type-fig21.png)

### <a name="suspicious-code-segment"></a>Segment de code suspect
Le segment de code suspect indique qu’un segment de code a été alloué à l’aide des méthodes non standard, comme celles utilisées par l’injection par réflexion et le « creusage » de processus (process hollowing).  En outre, cette alerte présente d’autres caractéristiques du segment de code pour fournir un contexte relatif aux fonctionnalités et aux comportements du segment de code indiqué.

Cette alerte présente les champs supplémentaires suivants :

- ADRESSE : emplacement du module injecté dans la mémoire
- TAILLE : taille du segment de code suspect
- SIGNATURES DE CHAÎNE : ce champ répertorie les fonctionnalités des API dont les noms de fonction sont contenus dans le segment de code. Voici des exemples de fonctionnalités :
    - Descripteurs de la section image, exécution de code dynamique pour x64, allocation de mémoire et capacité du chargeur, capacité d’injection de code à distance, capacité de contrôle de détournement, variables d’environnement de lecture, mémoire du processus arbitraire de lecture, privilèges de jeton de requête ou de modification, communication réseau HTTP/HTTPS et communication de socket réseau.
- IMAGE DÉTECTÉE : ce champ indique si une image PE a été injectée dans le processus au sein duquel le segment de code suspect a été détecté et à quelle adresse le module injecté commence.
- SHELLCODE : ce champ indique la présence d’un comportement couramment utilisé par des charges utiles malveillantes pour obtenir un accès à des fonctions supplémentaires du système d’exploitation sensible sur le plan de la sécurité. 

Voici un exemple de ce type d’alerte :

![Alerte de segment de code suspect](./media/security-center-alerts-type/security-center-alerts-type-fig22.png)

### <a name="shellcode-discovered"></a>Shellcode détecté
Un shellcode est la charge utile exécutée après qu’un programme malveillant a exploité une vulnérabilité logicielle. Cette alerte indique que l’analyse de vidage sur incident a détecté du code exécutable présentant un comportement souvent associé à des charges utiles malveillantes. Bien que des logiciels non malveillants puissent présenter aussi ce comportement, il n’est pas typique des pratiques de développement logiciel normales.

Cet exemple d’alerte shellcode fournit le champ supplémentaire suivant :

* ADRESSE : emplacement du shellcode dans la mémoire.

Voici un exemple de ce type d’alerte :

![Alerte de shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Détournement de module détecté
Windows s’appuie sur des bibliothèques de liens dynamiques (DLL) pour exploiter les fonctionnalités système courantes. Un détournement de DLL se produit lorsqu’un programme malveillant modifie l’ordre de chargement des DLL pour charger des charges utiles malveillantes dans la mémoire, où du code arbitraire peut être exécuté. Cette alerte indique que l’analyse de vidage sur incident a détecté qu’un module portant un nom similaire a été chargé à partir de deux chemins d’accès différents. L’un des chemins d’accès chargés provient d’un emplacement binaire système Windows commun.

Les développeurs de logiciels légitimes modifient parfois l’ordre de chargement des DLL pour des raisons non malveillantes, telles que l’instrumentation ou l’extension du système d’exploitation Windows ou des applications Windows. Pour distinguer les modifications malveillantes et les modifications potentiellement bénignes apportées à l’ordre de chargement des DLL, Azure Security Center vérifie si un module chargé est conforme à un profil suspect. Le résultat de cette vérification est indiqué par le champ « SIGNATURE » de l’alerte et est répercuté dans le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte. Pour rechercher si le module est légitime ou malveillant, analysez la copie sur disque du module de détournement. Par exemple, vous pouvez vérifier la signature numérique du fichier, ou exécuter une analyse antivirus.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* SIGNATURE : indique si le module de détournement est conforme à un profil de comportement suspect.
* HIJACKEDMODULE : nom du module système Windows détourné.
* HIJACKEDMODULEPATH : chemin d’accès du module système Windows détourné.
* HIJACKINGMODULEPATH : chemin d’accès du module de détournement.

Voici un exemple de ce type d’alerte :

![Alerte de détournement de module](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Usurpation d’identité de module Windows détectée
Les programmes malveillants peuvent utiliser des noms courants de fichiers binaires système (par exemple, SVCHOST.EXE) ou de modules (par exemple, NTDLL.DLL) Windows pour *se fondre* et cacher la nature malveillante des logiciels aux administrateurs système. Cette alerte indique que l’analyse de vidage sur incident a détecté que le fichier de vidage sur incident contient des modules qui utilisent des noms de modules système Windows, mais ne remplissent pas les autres critères typiques des modules Windows. L’analyse de la copie sur disque du module d’usurpation d’identité peut fournir des informations supplémentaires quant à la nature légitime ou malveillante de ce dernier. L’analyse peut inclure les opérations suivantes :

* Confirmation que le fichier en question est fourni dans le cadre d’un package logiciel légitime.
* Vérification de la signature numérique du fichier.
* Exécution d’une analyse antivirus sur le fichier.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* DETAILS : indique si les métadonnées du module sont valides et si le module a été chargé depuis un chemin d’accès système.
* NOM : nom du module Windows d’usurpation d’identité.
* PATH : chemin d’accès du module Windows d’usurpation d’identité.

Cette alerte extrait et affiche également certains champs de l’en-tête PE du module, tels que « CHECKSUM » et « TIMESTAMP ». Ces champs sont affichés uniquement s’ils sont présents dans le module. Pour plus d’informations sur ces champs, consultez la [spécification Microsoft PE et COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) .

Voici un exemple de ce type d’alerte :

![Alerte d’usurpation d’identité Windows](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Fichier binaire système modifié détecté
Les programmes malveillants peuvent modifier des fichiers binaires système de base pour accéder de façon dissimulée aux données ou persister subrepticement sur un système compromis. Cette alerte indique que l’analyse de vidage sur incident a détecté que des fichiers binaires de base du système d’exploitation Windows ont été modifiés dans la mémoire ou sur le disque.

Les développeurs de logiciels légitimes modifient parfois des modules système dans la mémoire pour des raisons non malveillantes, par exemple avec Detours ou pour la compatibilité des applications. Pour distinguer les modules malveillants et les modules potentiellement légitimes, Azure Security Center vérifie si le module modifié est conforme à un profil suspect. Le résultat de cette vérification est indiqué par le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* MODULENAME : nom du fichier binaire système modifié.
* MODULEVERSION : version du fichier binaire système modifié.

Voici un exemple de ce type d’alerte :

![Alerte de fichier binaire système](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Processus suspect exécuté
Azure Security Center identifie le processus suspect en cours d’exécution dans la machine virtuelle cible et déclenche une alerte. La détection ne recherche pas de nom spécifique, mais le paramètre du fichier exécutable. Par conséquent, même si l’attaquant renomme le fichier exécutable, Security Center peut toujours détecter le processus suspect.

Voici un exemple de ce type d’alerte :

![Alerte de processus suspect](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domains-accounts-queried"></a>Plusieurs comptes de domaine interrogés
Security Center peut détecter plusieurs tentatives d’interrogation de comptes de domaine Active Directory, généralement effectuées par des personnes malveillantes au cours de la reconnaissance du réseau. Les personnes malveillantes peuvent exploiter cette technique pour interroger le domaine et identifier les utilisateurs, les comptes d’administration de domaine, les ordinateurs qui sont des contrôleurs de domaine et la relation d’approbation potentielle d’un domaine avec d’autres domaines.

Voici un exemple de ce type d’alerte :

![Alerte de compte de domaines multiples](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Les membres du groupe Administrateurs locaux ont été énumérés

Security Center va déclencher une alerte lorsque l’événement de sécurité 4798, dans Windows Server 2016 et Windows 10, est déclenché. Cela se produit lorsque le groupe Administrateurs locaux est énuméré, ce qui est généralement effectué par des pirates au cours de la reconnaissance du réseau. Les pirates peuvent exploiter cette technique pour interroger l’identité des utilisateurs avec des privilèges d’administrateur.

Voici un exemple de ce type d’alerte :

![Administrateur local](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Combinaison anormale de majuscules et minuscules

Security Center déclenche une alerte lorsqu’il détecte l’utilisation d’une combinaison de majuscules et minuscules à la ligne de commande. Certains pirates peuvent utiliser cette technique pour se dérober aux règles de hachage ou de casse.

Voici un exemple de ce type d’alerte :

![Combinaison anormale](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Suspicion d’attaque de golden ticket Kerberos

Une clé [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) compromise peut être utilisée par un pirate pour créer des « golden tickets » Kerberos, lui permettant d’emprunter l’identité de l’utilisateur de leur choix. Security Center déclenche une alerte lorsqu’il détecte ce type d’activité.

> [!NOTE] 
> Pour plus d’informations sur les golden ticket Kerberos, lisez le [guide de prévention contre le vol d’informations d’identification Windows 10](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx).

Voici un exemple de ce type d’alerte :

![Golden ticket](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Création d’un compte suspect

Security Center déclenche une alerte lorsqu’un compte ressemblant fortement à un compte bénéficiant de privilèges d’administrateur intégré existant est créé. Cette technique peut être utilisée par des pirates pour créer un compte non autorisé sans être détecté par une vérification humaine.
 
Voici un exemple de ce type d’alerte :

![Compte suspect](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Création d’une règle de pare-feu suspecte

Les pirates peuvent tenter de contourner la sécurité de l’ordinateur hôte en créant des règles de pare-feu personnalisées pour permettre aux applications malveillantes de communiquer avec la commande et le contrôle, ou pour lancer des attaques sur le réseau par le biais de l’hôte compromis. Security Center déclenche une alerte lorsqu’il détecte qu’une règle de pare-feu a été créée à partir d’un fichier exécutable à un emplacement suspect.
 
Voici un exemple de ce type d’alerte :

![Règle de pare-feu](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Combinaison suspecte de HTA et PowerShell

Security Center déclenche une alerte lorsqu’il détecte qu’un hôte d’application HTML (HTA) lance des commandes PowerShell. Il s’agit d’une technique utilisée par les pirates pour lancer des scripts PowerShell malveillants.
 
Voici un exemple de ce type d’alerte :

![HTA et PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Analyse du réseau
La détection des menaces sur le réseau assurée par Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir du trafic IPFIX (Internet Protocol Flow Information Export) Azure. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces.

### <a name="suspicious-outgoing-traffic-detected"></a>Trafic sortant suspect détecté
Les périphériques réseau peuvent être détectés et profilés de la même façon que les autres types de systèmes. Les pirates commencent généralement par l’analyse des ports ou le balayage des ports. L’exemple ci-dessous présente un trafic Secure Shell (SSH) suspect d’une machine virtuelle pouvant être en train de procéder à une attaque de force brute SSH ou à une attaque par balayage des ports contre une ressource externe.

![Alerte de trafic sortant suspect](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Cette alerte fournit des informations permettant d’identifier la ressource utilisée pour lancer cette attaque. Cette alerte fournit également des informations permettant d’identifier l’ordinateur compromis, le temps de détection, ainsi que le protocole et le port utilisés. Cette page vous donne également une liste des étapes de résolution pouvant être utilisées pour résoudre ce problème.

### <a name="network-communication-with-a-malicious-machine"></a>Communication réseau avec un ordinateur malveillant
En exploitant les flux des informations sur les menaces de Microsoft, Azure Security Center peut détecter les ordinateurs compromis qui communiquent avec des adresses IP malveillantes. Dans de nombreux cas, l’adresse malveillante est un centre de commande et de contrôle. Dans ce cas, Security Center a détecté que la communication a été effectuée à l’aide du programme malveillant Pony Loader (également appelé [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![Alerte de communication réseau](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Cette alerte fournit des informations qui vous permettent d’identifier la ressource qui a été utilisée pour lancer ce type d’attaque, la ressource attaquée, l’adresse IP de la victime, l’adresse IP de l’attaquant et l’heure de détection.

> [!NOTE]
> Les adresses IP dynamiques ont été supprimés de cette capture d’écran à des fins de confidentialité.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Attaque potentielle par déni de service sortant détectée
Un trafic réseau anormal provenant d’une machine virtuelle peut amener Azure Security Center à déclencher un type d’attaque potentielle par déni de service.

Voici un exemple de ce type d’alerte :

![Déni de service sortant](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Analyse des ressources
Dans Security Center, l’analyse des ressources se concentre sur les services PaaS, comme l’intégration avec la fonctionnalité de [détection des menaces de la base de données SQL Azure](../sql-database/sql-database-threat-detection.md). Selon les résultats de l’analyse de ces zones, Azure Security Center déclenche une alerte associée aux ressources.

### <a name="potential-sql-injection"></a>Injection potentielle de code SQL
Une attaque par injection de code SQL est une attaque dans laquelle un code malveillant est inséré dans les chaînes transmises ultérieurement à une instance de SQL Server pour analyse et exécution. Toute procédure qui construit des instructions SQL doit être analysée pour rechercher les vulnérabilités d’injection, car SQL Server exécutera toutes les requêtes syntaxiquement valides qu’il reçoit. La fonctionnalité de détection des menaces SQL utilise l’apprentissage machine, l’analyse comportementale et la détection d’anomalie pour déterminer les événements suspects pouvant survenir dans les bases de données SQL Azure. Par exemple :

* Tentative d’accès à la base de données par un ancien employé
* Attaques par injection de code SQL
* Accès inhabituel à une base de données de production par un utilisateur à son domicile

![Alerte d’injection potentielle de code SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Les informations contenues dans cette alerte peuvent être utilisées pour identifier la ressource attaquée, le temps de détection et l’état de l’attaque. Elles fournissent également un lien vers d’autres étapes d’enquête.

### <a name="vulnerability-to-sql-injection"></a>Vulnérabilité par injection de code SQL
Cette alerte est déclenchée lorsqu’une erreur d’application est détectée sur une base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL.

![Alerte d’injection potentielle de code SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Accès inhabituel depuis un emplacement inconnu
Cette alerte est déclenchée lorsqu’un événement d’accès à partir d’une adresse IP inconnue a été détecté sur le serveur, qui n’était pas visible lors de la dernière période.

![Alerte d’accès inhabituel](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>Informations contextuelles
Dans le cadre d’une investigation, les analystes ont besoin d’un contexte supplémentaire pour aboutir à un verdict sur la nature de la menace et sur les moyens de l’atténuer.  Par exemple, si une anomalie de réseau a été détectée, mais que l’on ne connaît pas les autres événements qui se produisent sur le réseau ou en ce qui concerne la ressource ciblée, il est très difficile de déterminer les mesures à prendre en conséquence. Pour faciliter cette opération, un incident de sécurité peut inclure des artefacts, des événements associés et des informations susceptibles d’aider l’investigateur. La disponibilité des informations supplémentaires varie en fonction du type de menace détecté et de la configuration de votre environnement, et ces informations ne sont pas disponibles pour tous les incidents de sécurité.

Si des informations supplémentaires sont disponibles, elles s’affichent dans la section « Incident de sécurité » sous la liste des alertes. Cette section peut contenir des informations telles que :

- Événements d’effacement de journal
- Appareil Plug-and-Play branché à partir d’un appareil inconnu
- Alertes non actionnables 

![Alerte d’accès inhabituel](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Voir aussi
Cet article vous a présenté les différents types d’alertes de sécurité dans Azure Security Center. Pour plus d’informations sur Security Center, consultez :

* [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md)
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.
