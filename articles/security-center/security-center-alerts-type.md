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
ms.date: 02/21/2018
ms.author: yurid
ms.openlocfilehash: a5c95fc7ddf78987d8a7b135d54f359eb5c49946
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
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
* Analyse de SQL Database et SQL Data Warehouse
* Informations contextuelles

## <a name="virtual-machine-behavioral-analysis"></a>Analyse comportementale de la machine virtuelle
Azure Security Center peut utiliser l’analyse comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux d’événements. Par exemple, les événements de création de processus et les événements de connexion. En outre, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée.

> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, voir [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Analyse des événements
Security Center utilise une analyse avancée pour identifier les ressources compromises en se basant sur l’analyse des journaux d’événements de la machine virtuelle. Par exemple, les événements de création de processus et les événements de connexion. En outre, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée.

* **Exécution de processus suspects détectée** : les attaquants tentent souvent d’exécuter un code malveillant sans être détectés en se faisant passer pour des processus sans gravité. Ces alertes indiquent que l’exécution d’un processus correspond à l’un des modèles suivants :
    * Un processus connu pour être utilisé à des fins malveillantes a été exécuté. Alors que des commandes individuelles peuvent sembler sans gravité, l’alerte est basée sur une agrégation de ces commandes.
    * Un processus a été exécuté à partir d’un emplacement rare.
    * Un processus a été exécuté à partir d’un emplacement en commun avec des fichiers suspects connus.
    * Un processus a été exécuté à partir d’un chemin d’accès suspect.
    * Un processus a été exécuté dans un contexte anormal.
    * Un processus a été exécuté à partir d’un compte inhabituel
    * Un processus avec une extension suspecte a été exécuté.
    * Un processus avec une double extension suspecte a été exécuté.
    * Un processus avec un caractère de droite à gauche (RLO) suspect dans son nom de fichier a été exécuté.
    * Un processus dont le nom est très similaire mais différent d’un processus très courant a été exécuté
    * Un processus dont le nom correspond à un outil d’attaquant connu a été exécuté.
    * Un processus avec un nom aléatoire a été exécuté.
    * Un processus avec une extension suspecte a été exécuté.
    * Un fichier masqué a été exécuté.
    * Un processus a été exécuté comme un élément enfant d’un autre processus non lié.
    * Un processus inhabituel a été créé par un processus système.
    * Un processus anormal a été lancé par le service Windows Update.
    * Un processus a été exécuté avec une ligne de commande inhabituelle. Cela a été associé à des processus légitimes détournés pour l’exécution d’un contenu malveillant.
    * Une tentative de démarrage de tous les fichiers exécutables (*.exe) d’un répertoire a été faite à partir d’une ligne de commande.
    * Un processus a été exécuté par l’utilitaire PsExec, pouvant être utilisé pour exécuter des processus à distance.
    * Le fichier exécutable parent de Apache Tomcat® (Tomcat#.exe) a été utilisé pour lancer des processus enfant suspects, pouvant héberger ou lancer des commandes nuisibles.
    * L’assistant de compatibilité des programmes (pcalua.exe) de Microsoft Windows a été utilisé pour lancer un code exécutable, pouvant être malveillant.
    * Une rafale de fin du processus suspecte a été détectée.
    * Le processus système SVCHOST a été exécuté dans un contexte anormal.
    * Le processus système SVCHOST a été exécuté dans un groupe de services rare.
    * Une ligne de commande suspecte a été exécutée.
    * Un script PowerShell possède des caractéristiques communes à des scripts suspects connus.
    * Une applet de commande Powersploit de PowerShell connu comme étant malveillant a été exécutée.
    * Un utilisateur SQL intégré a exécuté un processus qu'il n’exécute pas normalement
    * Un exécutable codé en base 64 a été détecté, pouvant indiquer qu’un intrus tente d’éviter une détection en construisant un exécutable à la volée avec une séquence de commandes.

* **Activité de ressource RDP suspecte** : les attaquants ciblent souvent des ports de gestion ouverts comme RDP avec des attaques par force brute. Ces alertes indiquent une activité de connexion Bureau à distance suspecte :
    * Des connexions Bureau à distance ont été tentées.
    * Des connexions Bureau à distance ont été tentées à l’aide de comptes non valides.
    * Des connexions Bureau à distance ont été tentées, certaines d'entre-elles ont pu se connecter à la machine.
* **Activité de ressource SSH suspecte** : les attaquants ciblent souvent des ports de gestion ouverts comme SSH avec des attaques par force brute. Ces alertes indiquent une activité de connexion SSH suspecte :
    * Des connexion SSH ont échoué.
    * Des connexions SSH ont été tentées, certaines d’entre-elles ont réussi.
* **Valeur de registre WindowPosition suspecte** : cette alerte indique qu’une modification de configuration de registre WindowPosition a été tentée, pouvant indiquer un masquage de fenêtres d’application dans des sections non visible du bureau.
* **Tentative de contournement de AppLocker**: AppLocker peut être utilisé pour limiter les processus pouvant s’exécuter sur Windows, limitant l’exposition à des programmes malveillants. Cette alerte indique une possible tentative de contournement des restrictions de AppLocker à l’aide d’exécutables approuvés (autorisées par la stratégie AppLocker) pour exécuter un code non fiable.
* **Communications de canal nommé suspectes** : cette alerte indique que les données ont été écrites dans un canal nommé local à partir d’une commande de console Windows. Les canaux nommés sont connus pour être utilisés par des attaquants afin d’attribuer et de communiquer avec un implant malveillant.
* **Décodage d’un fichier exécutable à l’aide de l’outil certutil.exe intégré** : cette alerte indique qu’un utilitaire administrateur intégré, certutil.exe, a été utilisé pour décoder un fichier exécutable. Les attaquants sont connus pour abuser des fonctionnalités d’outils d’administration légitimes afin d’effectuer des actions malveillantes, par exemple en utilisant un outil comme certutil.exe pour décoder un fichier exécutable qui sera ensuite exécuté.
* **Un journal des événements a été effacé** : cette alerte indique une opération suspecte d’effacement de journal, souvent utilisée par des attaquants pour essayer de couvrir leurs traces.
* **Désactivation et suppression des fichiers de journaux IIS** : cette alerte indique que des fichiers de journaux IIS ont été désactivés ou supprimés, ce qui est souvent utilisé par des attaquants pour essayer de couvrir leurs traces.
* **Suppression de fichier suspecte** : cette alerte indique une suppression suspecte de fichiers, ce qui peut être utilisé par un attaquant pour supprimer des preuves de binaires malveillants.
* **Tous les clichés instantanés de fichiers ont été supprimés** : cette alerte indique que les clichés instantanés ont été supprimés.
* **Commandes suspectes de nettoyage de fichier** : cette alerte indique une combinaison de commandes systeminfo utilisée pour effectuer une activité de nettoyage automatique après compromission.  Alors que *systeminfo.exe* est un outil Windows légitime, une exécution deux fois de suite, suivie d’une commande de suppression comme survenue ici est rare.
* **Création de compte suspecte** : cette alerte indique qu’un compte a été créé avec une proche ressemblance avec un compte de privilège Administrateur intégré. Cette technique peut être utilisée par des pirates pour créer un compte non autorisé sans être détecté.
* **Activité suspecte de copie de clichés instantanés de volume** : cette alerte indique une activité de suppression de copie de cliché instantané sur la ressource. Le cliché instantané de volume (VSC) est un artefact important qui stocke des clichés instantanés de données. Cette activité est généralement associée à un ransowmare, mais elle peut aussi être légitime.
* **Méthode de persistance du registre Windows** : cette alerte indique une tentative de conservation d’un fichier exécutable dans le registre Windows. Les logiciels malveillants utilisent souvent cette technique pour survivre à un démarrage.
* **Nouvelle règle de pare-feu suspecte** : cette alerte indique qu’une règle de pare-feu a été ajoutée via *netsh.exe* pour autoriser un trafic à partir d’un fichier exécutable situé dans un emplacement suspect.
* **Exécutions suspectes XCOPY**: cette alerte indique une série d’exécutions de XCOPY pouvant signaler la compromission d’une de vos machines et son utilisation pour propager des logiciels malveillants.
* **Suppression de notice légale affichée aux utilisateurs lors de la connexion** : cette alerte indique une modification de la clé de registre qui contrôle l’affichage d’une notice légale aux utilisateurs quand ils se connectent. Il s’agit d’une activité courante utilisée par des attaquants après avoir compromis un hôte.
* **Détection d’une combinaison anormale de caractères minuscules et majuscules en ligne de commande** : cette alerte indique l’utilisation d’une combinaison de majuscules et minuscules en ligne de commande, une technique utilisée par des attaquants pirates pour passer outre la règle de la fonction de hachage ou le respect de la casse de la machine.
* **Ligne de commande obscurcie** : cette alerte montre la détection d’indicateurs suspects d’obscurcissement dans la ligne de commande.
* **Interrogations de plusieurs comptes de domaine** : les attaquants interrogent souvent des comptes de domaine AD lors de l’exécution de la reconnaissance des utilisateurs, comptes d’administrateur de domaine, contrôleurs de domaine et relations d’approbation entre des domaines. Cette alerte indique qu’un nombre inhabituel de comptes de domaine distincts ont été interrogés sur une courte période.
* **Activité de reconnaissance locale possible** : cette alerte indique l’exécution d’une combinaison de commandes systeminfo associées à une activité de reconnaissance.  Bien que *systeminfo.exe* soit un outil Windows légitime, il est rare qu’il soit exécuté deux fois de suite.
* **Exécution possible d’un exécutable keygen** : cette alerte indique l’exécution d’un processus dont le nom indique un outil keygen. Ces outils sont généralement utilisés pour contrer les mécanismes de gestion des licences des logiciels, mais leur téléchargement comprend souvent d’autres logiciels malveillants.
* **Exécution suspecte de rundll32.exe** : cette alerte indique que rundll32.exe était utilisé pour exécuter un processus avec un nom rare, correspondant à un modèle d’affectation de noms de processus utilisé par des attaquants pour installer un implant de première étape sur un hôte compromis.
* **Combinaison suspecte de HTA et PowerShell** : cette alerte indique l’exécution de commandes PowerShell par un hôte d’application HTML (HTA) de Microsoft. Il s’agit d’une technique utilisée par les pirates pour lancer des scripts PowerShell malveillants.
* **Remplacement par une clé de registre pouvant être utilisée pour contourner l’UAC** : cette alerte indique qu’une clé de registre pouvant être utilisée pour contourner l’UAC (contrôle des comptes d’utilisateur) a été modifiée, ce qui est souvent utilisé par les pirates pour passer d’un accès non privilégié (utilisateur standard) à un accès privilégié (administrateur par exemple) sur un hôte compromis.
* **Utilisation d’un nom de domaine suspect au sein d’une ligne de commande** : cette alerte indique l’utilisation d’un nom de domaine suspect, ce qui peut être une preuve du fait qu’un attaquant héberge des outils malveillants et en tant que points de terminaison pour la commande et le contrôle ainsi que l’exfiltration de données.
* **Un compte a été créé sur plusieurs hôtes sur une période de 24 heures** : cette alerte indique une tentative de création du même compte d’utilisateur sur plusieurs hôtes, ce qui peut être une preuve du fait qu’une personne malveillante se déplace latéralement sur le réseau après la compromission d’une ou plusieurs entités de réseau.
* **Utilisation suspecte des CACLS pour réduire l’état de sécurité du système** : cette alerte indique une modification de la liste de contrôle d’accès des changements (CACLS). Cette technique est souvent utilisée par les attaquants pour donner un accès complet au système à des binaires tels que ftp.exe, net.exe, wscript.exe, etc.
* **Paramètres d’attaque de golden ticket Kerberos suspects** : cette alerte indique que des paramètres de ligne de commande cohérents avec une attaque Golden Ticket Kerberos ont été exécutés. Une clé krbtgt compromise peut être utilisée par un attaquant pour emprunter l’identité de l’utilisateur de leur choix.
* **Activation de la clé de registre WDigest UseLogonCredential** : cette alerte indique que la clé de registre a été modifiée pour permettre le stockage d’informations d’authentification en texte clair dans la mémoire LSA, pouvant être ensuite récoltées à partir de la mémoire.
* **Utilisation potentiellement suspecte de l’outil Telegram** : cette alerte indique l’installation de Telegram, un service de messagerie instantanée libre sur le cloud, utilisé par des attaquants pour transférer des fichiers binaires malveillants sur n’importe quel ordinateur, téléphone ou tablette.
* **Création d’un nouveau point ASEP** : cette alerte indique la création d’un nouveau point ASEP (point d’extensibilité de départ automatique), entraînant le démarrage automatique du nom du processus identifié dans la ligne de commande, pouvant être utilisé par un attaquant pour obtenir une persistance.
* **Modifications suspectes de Set-ExecutionPolicy et de WinRM** : cette alerte indique des modifications de configuration, associées à l’utilisation de la webshell malveillante ChinaChopper.
* **Désactivation de services essentiels** : cette alerte indique l’utilisation de la commande « net.exe stop » pour arrêter des services essentiels tels que SharedAccess ou le Centre de sécurité Windows.
* **Utilisation suspecte de la bascule -s de FTP** : cette alerte indique l’utilisation de la bascule de FTP «-s », qui peut être utilisé par un logiciel malveillant pour se connecter à un serveur FTP et télécharger des fichiers binaires malveillants supplémentaires.
* **Exécution suspecte de la commande VBScript.Encode**: cette alerte indique l’exécution de la commande *VBScript.Encode*, qui encode des scripts en un texte illisible, rendant le code difficilement vérifiable par les utilisateurs.
* **Allocation d’objet HTTP VBScript**  : cette alerte indique la création d’un fichier VBScript à l’aide d’une invite de commandes, pouvant être utilisé pour télécharger des fichiers malveillants.
* **Attaque de touches rémanentes** : cette alerte indique qu’un attaquant est peut-être en train de compromettre un binaire d’accessibilité (par exemple les touches rémanentes, le clavier visuel, le narrateur) afin de fournir un accès par une porte dérobée.
* **Indicateurs de ransomware Petya** : cette alerte indique que des techniques associées au ramsomware Petya ont été observées.
* **Tentative de désactivation AMSI** : cette alerte indique une tentative de désactivation de l’interface d’analyse de logiciels anti-programme malveillant (AMSI), qui désactiverait la détection de logiciels anti-programme malveillant.
* **Indicateurs de ransomware** : cette alerte indique une activité suspecte généralement associée aux ransomware par écran de verrouillage et chiffrement.
* **Fichiers de sortie et collection de traces suspects** : cette alerte indique qu’une trace (d’une activité réseau par exemple) a été collectée et sortie vers un type de fichier inhabituel.
* **Logiciel à haut risque** : cette alerte indique l’utilisation d’un logiciel associé à l’installation de logiciels malveillants. Les attaquants mêlent souvent un logiciel malveillant avec des outils sans gravité tel que celui indiqué dans cette alerte, et installent de façon cachée des logiciels malveillants en arrière-plan.
* **Création de fichier suspecte** : cette alerte indique la création ou l’exécution d’un processus utilisé par des attaquants pour télécharger des logiciels malveillants supplémentaires ver un hôte compromis après l’ouverture d’une pièce jointe dans un document de hameçonnage.
* **Informations d’identification suspectes en ligne de commande** : cette alerte indique un mot de passe suspect utilisé pour exécuter un fichier. Cette technique a été utilisée par des attaquants pour exécuter le logiciel malveillant Pirpi.
* **Exécution possible d’une pipette de logiciel malveillant** : cette alerte indique un nom de fichier utilisé par des attaquants pour installer un logiciel malveillant.
* **Exécution suspecte via rundll32.exe** : cette alerte indique l’utilisation de rundll32.exe pour exécuter notepad.exe ou reg.exe, cohérente avec la technique d’injection de processus utilisée par des attaquants.
* **Arguments de ligne de commande suspects** : cette alerte indique que des arguments de ligne de commande suspects ont été utilisés conjointement avec un interpréteur de commandes inverse utilisé par le groupe d’activités HYDROGEN.
* **Informations d’identification de document suspectes** : cette alerte indique qu’un hachage de mot de passe précalculé, commun et suspect est utilisé par des programmes malveillants pour exécuter un fichier.
* **Construction de script dynamique PS**: cette alerte indique la construction dynamique d’un script PowerShell. Les attaquants utilisent cette technique pour générer un script progressivement afin d’échapper aux systèmes IDS.
* **Indicateurs Metaploit** : cette alerte indique une activité associée à l’infrastructure Metasploit, qui fournit une gamme d’outils et de fonctionnalités à un attaquant.
* **Activité de compte suspecte** : cette alerte indique une tentative de connexion à une machine à l’aide d’un compte récemment compromis.
* **Création d’un compte** : cette alerte indique la création d’un nouveau compte sur la machine.


### <a name="crash-analysis"></a>Analyse des incidents


L’analyse de la mémoire de vidage sur incident est une méthode utilisée pour détecter les programmes malveillants sophistiqués capables d’échapper aux solutions de sécurité traditionnelles. Différentes formes de programmes malveillants tentent de réduire le risque de détection par des produits antivirus en n’écrivant jamais sur le disque ou en chiffrant les composants logiciels écrits sur le disque. Cette technique rend les programmes malveillants difficiles à détecter à l’aide des logiciels anti-programmes malveillants traditionnels. Toutefois, ce type de logiciel malveillant peut être détecté à l’aide de l’analyse de mémoire, car le programme malveillant laisse des traces en mémoire pour pouvoir fonctionner.

Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident. L’incident peut être provoqué par un programme malveillant, une application générale ou des problèmes système. En analysant la mémoire dans le vidage sur incident, Azure Security Center peut détecter les techniques utilisées pour exploiter la vulnérabilité des logiciels, accéder aux données confidentielles et persister subrepticement au sein d’un ordinateur compromis. Ces opérations ont peu d’impact sur les performances des hôtes, car l’analyse est effectuée au cœur d’Azure Security Center.

* **Injection de code découverte** : une injection de code est l’insertion de modules exécutables dans des processus ou threads en cours d’exécution. Cette technique est utilisée par les programmes malveillants pour accéder aux données, masquer ou empêcher leur suppression (par exemple, la persistance). Cette alerte indique qu’un module injecté est présent dans l’image mémoire. Les développeurs de logiciels légitimes procèdent parfois à une injection de code pour des raisons non malveillantes, comme la modification ou l’extension d’une application existante ou d’un composant de système d’exploitation. Pour distinguer les modules injectés malveillants et non malveillants, Azure Security Center vérifie si le module injecté est conforme à un profil de comportement suspect. Le résultat de cette vérification est indiqué par le champ « SIGNATURE » de l’alerte et est répercuté dans le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte.
* **Segment de code suspect** : l’alerte de segment de code suspect indique qu’un segment de code a été alloué à l’aide des méthodes non standard, comme celles utilisées par l’injection par réflexion et le « creusage » de processus (process hollowing). D’autres caractéristiques du segment de code sont traitées pour fournir un contexte relatif aux fonctionnalités et aux comportements du segment de code indiqué.
* **Shellcode découvert** : un shellcode est la charge utile exécutée après qu’un programme malveillant a exploité une vulnérabilité logicielle. Cette alerte indique que l’analyse de vidage sur incident a détecté du code exécutable présentant un comportement souvent associé à des charges utiles malveillantes. Bien que des logiciels non malveillants puissent présenter aussi ce comportement, il n’est pas typique des pratiques de développement logiciel normales.
* **Module de piratage découvert** : Windows s’appuie sur des bibliothèques de liens dynamiques (DLL) pour exploiter les fonctionnalités système courantes. Un détournement de DLL se produit lorsqu’un programme malveillant modifie l’ordre de chargement des DLL pour charger des charges utiles malveillantes dans la mémoire, où du code arbitraire peut être exécuté. Cette alerte indique que l’analyse de vidage sur incident a détecté qu’un module portant un nom similaire a été chargé à partir de deux chemins d’accès différents. L’un des chemins d’accès chargés provient d’un emplacement binaire système Windows commun. Les développeurs de logiciels légitimes modifient parfois l’ordre de chargement des DLL pour des raisons non malveillantes, telles que l’instrumentation ou l’extension du système d’exploitation Windows ou des applications Windows. Pour distinguer les modifications malveillantes et les modifications potentiellement bénignes apportées à l’ordre de chargement des DLL, Security Center vérifie si un module chargé est conforme à un profil suspect.
* **Usurpation de module Windows détecté** : Les programmes malveillants peuvent utiliser des noms courants de fichiers binaires système (par exemple, SVCHOST.EXE) ou de modules (par exemple, NTDLL.DLL) Windows pour se fondre et cacher la nature malveillante des logiciels aux administrateurs système. Cette alerte indique que le fichier de vidage sur incident contient des modules qui utilisent des noms de modules système Windows, mais ne remplissent pas les autres critères typiques des modules Windows. L’analyse de la copie sur disque du module d’usurpation d’identité peut fournir des informations supplémentaires quant à la nature légitime ou malveillante de ce dernier.
* **Fichier binaire système modifié détecté** : Les programmes malveillants peuvent modifier des fichiers binaires système de base pour accéder de façon dissimulée aux données ou persister subrepticement sur un système compromis. Cette alerte indique que l’analyse de vidage sur incident a détecté que des fichiers binaires de base du système d’exploitation Windows ont été modifiés dans la mémoire ou sur le disque. Les développeurs de logiciels légitimes modifient parfois des modules système dans la mémoire pour des raisons non malveillantes, par exemple avec Detours ou pour la compatibilité des applications. Pour distinguer les modules malveillants et les modules potentiellement légitimes, Security Center vérifie si le module modifié est conforme à un profil suspect.

## <a name="network-analysis"></a>Analyse du réseau
La détection des menaces sur le réseau assurée par Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir du trafic IPFIX (Internet Protocol Flow Information Export) Azure. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces.

* **Tentatives possibles d’attaque SQL par force brute** : l’analyse de trafic réseau a détecté une communication SQL entrante suspecte. Cette activité est cohérente avec les tentatives d’attaque par force brute contre les serveurs SQL.
* **Activité réseau entrante RDP suspecte provenant de plusieurs sources** : l’analyse du trafic réseau a détecté une communication entrante anormale du protocole RDP (Remote Desktop Protocol) à partir de plusieurs sources. Plus précisément, les données réseau échantillonnées montrent des adresses IP uniques se connectant à votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre point de terminaison RDP à partir de plusieurs hôtes (Botnet).
* **Activité réseau entrante RDP suspecte** : l’analyse du trafic réseau a détecté une communication entrante anormale du protocole RDP (Remote Desktop Protocol). Plus précisément, les données réseau échantillonnées montrent un grand nombre de connexions entrantes vers votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre point de terminaison RDP.
* **Activité réseau sortante RDP suspecte vers plusieurs destinations** : l’analyse du trafic réseau a détecté une communication sortante anormale du protocole RDP (Remote Desktop Protocol) vers plusieurs destinations. Cette activité peut indiquer que votre machine a été compromise et qu’elle est désormais utilisée pour attaquer par force brute des points de terminaison RDP externes. Notez que ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.
* **Activité réseau sortante RDP suspecte** : l’analyse du trafic réseau a détecté une communication sortante anormale du protocole RDP (Remote Desktop Protocol). Plus précisément, les données réseau échantillonnées montrent un grand nombre de connexions sortantes depuis votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer que votre machine a été compromise et qu’elle est désormais utilisée pour attaquer par force brute des points de terminaison RDP externes. Notez que ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.
* **Activité réseau entrante SSH suspecte** : l’analyse du trafic réseau a détecté une communication entrante anormale du protocole SSH. Plus précisément, les données réseau échantillonnées montrent un grand nombre de connexions entrantes vers votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre point de terminaison SSH.
* **Activité réseau entrante SSH suspecte à partir de plusieurs sources** : l’analyse du trafic réseau a détecté une communication entrante anormale du protocole SSH. Plus précisément, les données réseau échantillonnées montrent des adresses IP uniques se connectant à votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre point de terminaison SSH à partir de plusieurs hôtes (Botnet).
* **Activité réseau sortante SSH suspecte** : l’analyse du trafic réseau a détecté une communication sortante anormale du protocole SSH. Plus précisément, les données réseau échantillonnées montrent un grand nombre de connexions sortantes depuis votre machine, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer que votre machine a été compromise et qu’elle est désormais utilisée pour attaquer par force brute des points de terminaison SSH externes. Notez que ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.
* **Activité réseau sortante SSH suspecte vers plusieurs destinations** : l’analyse du trafic réseau a détecté une communication sortante anormale du protocole SSH vers plusieurs destinations. Plus précisément, les données réseau échantillonnées montrent que votre machine se connecte à des adresses IP uniques, ce qui est considéré comme anormal pour cet environnement. Cette activité peut indiquer que votre machine a été compromise et qu’elle est désormais utilisée pour attaquer par force brute des points de terminaison SSH externes. Notez que ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.
* **Communication réseau avec un ordinateur malveillant détectée** : l’analyse du trafic réseau indique que votre ordinateur a communiqué avec ce qui semble être un centre de contrôle et de commande.
* **Possible machine compromise détectée** : l’analyse du trafic réseau a détecté une activité sortante, pouvant indiquer que la machine agit en tant que partie d’un botnet. L’analyse est basée sur des adresses IP accédés par votre ressource ainsi que des enregistrements DNS publics.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Analyse de SQL Database et SQL Data Warehouse

Dans Security Center, l’analyse des ressources se concentre sur les services PaaS, comme l’intégration avec la [détection des menaces de la base de données SQL Azure](../sql-database/sql-database-threat-detection.md) et Azure SQL Data Warehouse. La fonctionnalité de détection des menaces SQL détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à utiliser des bases de données ou à exploiter leurs failles de sécurité, et déclenche les alertes suivantes :

* **Vulnérabilité aux injections SQL** : cette alerte est déclenchée lorsqu’une application génère une instruction SQL défectueuse dans la base de données. Cela peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Deux raisons possibles expliquent la génération d’une instruction défectueuse :
    * Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
    * Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
* **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée vers l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.
* **Access from unusual location** (Accès à partir d’un emplacement inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
* **Access from unusual Azure data center** (Accès à partir d’un centre de données Azure inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un centre de données Azure inhabituel observé sur ce serveur récemment. Dans certains cas, l’alerte détecte une action légitime (votre nouvelle application dans Azure, Power BI, l’éditeur de requête SQL Azure). Dans d’autres cas, l’alerte détecte une action malveillante provenant d’une ressource/d’un service Azure (ancien employé, attaquant externe).
* **Access from unfamiliar principal** (Accès à partir d’un principal inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à l’aide d’un principal inhabituel (utilisateur SQL). Dans certains cas, l’alerte détecte une action légitime (nouvelle application, opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
* **Access from a potentially harmful application** (Accès à partir d’une application potentiellement dangereuse) : cette alerte est déclenchée lorsqu’une application potentiellement dangereuse est utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque à l’aide d’outils d’attaque courants.
* **Brute force SQL credentials** (Informations d’identification par force brute) : cette alerte est déclenchée lorsqu’il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.

## <a name="contextual-information"></a>Informations contextuelles
Dans le cadre d’une investigation, les analystes ont besoin d’un contexte supplémentaire pour aboutir à un verdict sur la nature de la menace et sur les moyens de l’atténuer.  Par exemple, si une anomalie de réseau a été détectée, mais que l’on ne connaît pas les autres événements qui se produisent sur le réseau ou en ce qui concerne la ressource ciblée, il est très difficile de déterminer les mesures à prendre en conséquence. Pour faciliter cette opération, un incident de sécurité peut inclure des artefacts, des événements associés et des informations susceptibles d’aider l’investigateur. La disponibilité des informations supplémentaires varie en fonction du type de menace détecté et de la configuration de votre environnement, et ces informations ne sont pas disponibles pour tous les incidents de sécurité.

Si des informations supplémentaires sont disponibles, elles s’affichent dans la section « Incident de sécurité » sous la liste des alertes. Cette section peut contenir des informations telles que :

- Événements d’effacement de journal
- Appareil Plug-and-Play branché à partir d’un appareil inconnu
- Alertes non actionnables
- Création d’un compte
- Fichier décodé à l’aide de l’outil certutil 

![Alerte d’accès inhabituel](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté les différents types d’alertes de sécurité dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md)
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.
