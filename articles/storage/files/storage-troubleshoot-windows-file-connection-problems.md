---
title: "Résoudre les problèmes liés à Azure Files sous Windows | Microsoft Docs"
description: "Résoudre les problèmes liés à Azure Files sous Windows"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: 073d163e139c9fd400e4b3177c26d4ddb6228ed0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Résoudre les problèmes liés à Azure Files sous Windows

Cet article liste les problèmes courants liés à Microsoft Azure Files en cas de connexion à partir de clients Windows. Il fournit également les causes possibles et les solutions de ces problèmes. En plus des étapes de résolutions présentées dans cet article, vous pouvez aussi utiliser [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) pour être sûr que l’environnement du client Windows est configuré correctement. AzFileDiagnostics détecte automatiquement la plupart des problèmes mentionnés dans cet article et vous aide à configurer votre environnement pour que les performances soient optimales. Vous pouvez également trouver ces informations dans [l’utilitaire de résolution des problèmes de partages Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares), qui indique la procédure à suivre pour vous aider en cas de problèmes de connexion/mappage/montage de partages Azure Files.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Les messages « Erreur 53 », « Erreur 67 » ou « Erreur 87 » s’affichent lorsque vous montez ou démontez un partage de fichiers Azure

Lorsque vous essayez de monter un partage de fichiers depuis un centre de données local ou un centre de données différent, les erreurs suivantes pourraient survenir :

- Erreur système 53. Le chemin d’accès réseau est introuvable.
- Erreur système 67. Le nom du réseau est introuvable.
- Erreur système 87. Le paramètre est incorrect.

### <a name="cause-1-unencrypted-communication-channel"></a>Cause 1 : Canal de communication non chiffrée

Pour des raisons de sécurité, les connexions aux partages de fichiers Azure sont bloquées si le canal de communication n’est pas chiffré et si la tentative de connexion n’est pas effectuée depuis le centre de données sur lequel résident les partages de fichiers Azure. Le chiffrement du canal de communication n’est fourni que si le système d’exploitation client de l’utilisateur prend en charge le chiffrement SMB.

Windows 8, Windows Server 2012 et les versions ultérieures de chaque demande de négociation système incluant SMB 3.0, prenant en charge le chiffrement.

### <a name="solution-for-cause-1"></a>Solution pour la cause 1

Connectez-vous depuis un client qui :

- Dispose de la configuration requise de Windows 8 et Windows Server 2012 ou versions ultérieures
- Se connecte depuis une machine virtuelle située dans le même centre de données que le compte de stockage Azure utilisé pour le partage de fichiers Azure

### <a name="cause-2-port-445-is-blocked"></a>Cause 2 : Le Port 445 est bloqué

Une erreur système 53 ou 67 peut se produire si la communication sortante du port 445 vers le centre de données Azure Files est bloquée. Pour afficher le récapitulatif des FAI qui autorisent ou interdisent l’accès depuis le port 445, consultez [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pour comprendre s’il s’agit de la raison pour laquelle le message « Erreur système 53 » s’affiche, vous pouvez utiliser Portqry pour interroger le point de terminaison TCP:445. Si le point de terminaison TCP:445 est affiché comme filtré, le port TCP est bloqué. Voici un exemple de requête :

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si le port TCP 445 est bloqué par une règle sur le chemin d’accès réseau, vous verrez la sortie suivante :

  `TCP port 445 (microsoft-ds service): FILTERED`

Pour plus d’informations sur l’utilisation de Portqry, consultez [Description de l’utilitaire de ligne de commande Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Solution pour la cause 2

Contactez votre service informatique pour ouvrir le port 445 sortant aux [plages IP Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Cause 3 : NTLMv1 est activé

Les messages Erreur système 53 ou Erreur système 87 peuvent également s’afficher si la communication NTLMv1 est activée sur le client. Azure Files prend uniquement en charge l’authentification NTLMv2. Le fait d’activer NTLMv1 crée un client moins sécurisé. Par conséquent, les communications sont bloquées pour Azure Files. 

Pour déterminer s’il s’agit de la cause de l’erreur, vérifiez que la sous-clé de Registre suivante est définie sur une valeur de 3 :

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Pour plus d’informations, consultez la rubrique [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) sur TechNet.

### <a name="solution-for-cause-3"></a>Solution pour la cause 3

Rétablissez la valeur **LmCompatibilityLevel** à la valeur par défaut 3 dans la sous-clé de Registre suivante :

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Le message Erreur 1816 « Quota disponible insuffisant pour effectuer cette commande » s’affiche lorsque vous copiez vers un partage de fichiers Azure

### <a name="cause"></a>Cause :

L’erreur 1816 se produit lorsque vous atteignez la limite autorisée de descripteurs ouverts simultanément pour un fichier sur l’ordinateur où le partage de fichiers est monté.

### <a name="solution"></a>Solution

Réduisez le nombre de handles ouverts simultanément en fermant certains d’entre eux, puis réessayez. Pour plus d’informations, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Ralentissement des copies de fichiers vers et à partir d’Azure Files sous Windows

Les performances peuvent être ralenties lorsque vous essayez de transférer des fichiers vers le service Azure File.

- Si vous n’avez pas d’exigence de taille d’E/S minimum spécifique, nous vous recommandons d’utiliser une taille d’E/S de 1 Mo pour des performances optimales.
-   Si vous connaissez la taille finale d’un fichier que vous étendez avec des écritures, et si votre logiciel ne présente aucun problème de compatibilité lorsque la fin non écrite du fichier contient des zéros, définissez la taille du fichier à l’avance au lieu que chaque écriture soit une écriture d’extension.
-   Utilisez la méthode de copie appropriée :
    -   Utilisez [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour les transferts entre deux partages de fichiers.
    -   Utilisez [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre des partages de fichiers sur un ordinateur local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Informations pour Windows 8.1 ou Windows Server 2012 R2

Pour les clients qui exécutent Windows 8.1 ou Windows Server 2012 R2, assurez-vous que le correctif logiciel [KB3114025](https://support.microsoft.com/help/3114025) est installé. Ce correctif logiciel améliore les performances de création et d’ouverture des handles.

Vous pouvez exécuter le script suivant pour vérifier si le correctif logiciel a été installé :

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si le correctif logiciel est installé, la sortie suivante s’affiche :

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Les images de Windows Server 2012 R2 dans Azure Marketplace ont le correctif logiciel KB3114025 installé par défaut à compter de décembre 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Aucun dossier avec une lettre de lecteur dans **Poste de travail**

Si vous mappez un partage de fichiers Azure en tant qu’administrateur via Net use, le partage n’apparaît pas.

### <a name="cause"></a>Cause :

Par défaut, l’Explorateur Windows ne s’exécute pas en tant qu’administrateur. Si vous exécutez Net use depuis une invite de commandes administrateur, vous mappez le lecteur réseau en tant qu’administrateur. Étant donné que les lecteurs mappés sont centrés sur l’utilisateur, le compte d’utilisateur qui est connecté n’affiche pas les lecteurs s’ils sont montés sous un compte d’utilisateur différent.

### <a name="solution"></a>Solution
Montez le partage à partir d’une ligne de commande non administrateur. Vous pouvez également suivre [cette rubrique TechNet](https://technet.microsoft.com/library/ee844140.aspx) pour configurer la valeur de Registre **EnableLinkedConnections**.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>La commande Net use échoue si le compte de stockage contient une barre oblique

### <a name="cause"></a>Cause :

La commande Net use interprète une barre oblique (/) comme une option de ligne de commande. Si le nom de votre compte utilisateur commence par une barre oblique, le mappage du lecteur échoue.

### <a name="solution"></a>Solution

Vous pouvez utiliser l’une des étapes suivantes pour contourner le problème :

- Exécutez la commande PowerShell suivante :

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Depuis un fichier de commandes, vous pouvez exécuter la commande de cette façon :

  `Echo new-smbMapping ... | powershell -command –`

- Placez des guillemets doubles autour de la clé pour résoudre ce problème, sauf si la barre oblique est le premier caractère. Si c’est le cas, utilisez le mode interactif et entrez votre mot de passe séparément, ou régénérez vos clés pour obtenir une clé qui ne commence pas par une barre oblique.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>L’application ou service ne peut pas accéder à un lecteur Azure Files monté

### <a name="cause"></a>Cause :

Les lecteurs sont montés par l’utilisateur. Si vous n’exécutez pas l’application ou service depuis le compte utilisateur ayant monté le lecteur, l’application ou service ne verra pas le lecteur.

### <a name="solution"></a>Solution

Utilisez l'une des solutions suivantes :

-   Montez le lecteur depuis le compte utilisateur qui dispose de l’application. Vous pouvez utiliser un outil tel que PsExec.
- Transmettez le nom et la clé du compte de stockage dans les paramètres de nom d’utilisateur et de mot de passe de la commande Net use.
- Utilisez la commande cmdkey pour ajouter les informations d’identification dans le Gestionnaire d’informations d’identification. Effectuez cette procédure à partir d’une ligne de commande dans le contexte du compte de service, via une connexion interactive ou avec RunAs.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappez le partage directement sans l’aide d’une lettre de lecteur mappée. Certaines applications ne se reconnectent pas correctement à la lettre de lecteur. Il est donc conseillé d’utiliser le chemin d’accès UNC complet qui est plus fiable. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Après avoir suivi ces instructions, vous pourriez recevoir le message d’erreur suivant en exécutant Net use sur le compte de service réseau ou système : « Erreur système 1312. Une session ouverte spécifiée n’existe pas. Il se peut qu’elle ait été déjà fermée. » Si cela se produit, vérifiez que le nom d’utilisateur transmis à Net use inclut des informations de domaine (par exemple : « [nom du compte de stockage].file.core.windows.net »).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le chiffrement »

Lorsqu’un fichier est copié sur le réseau, le fichier est déchiffré sur l’ordinateur source, transmis en clair, puis de nouveau chiffré une fois à destination. Toutefois, vous pourriez rencontrer l’erreur suivante en essayant de copier un fichier chiffré : « Vous copiez le fichier vers une destination qui ne prend pas en charge le chiffrement. »

### <a name="cause"></a>Cause :
Ce problème peut se survenir si vous utilisez le système de fichiers EFS (Encrypting File System). Les fichiers chiffrés BitLocker peuvent être copiés vers Azure Files. Toutefois, Azure Files ne prend pas en charge le système de fichiers EFS NTFS.

### <a name="workaround"></a>Solution de contournement
Pour copier un fichier sur le réseau, vous devez d’abord le déchiffrer. Utilisez l’une des méthodes suivantes :

- Utilisez la commande **copy /d**. Les fichiers chiffrés peuvent ainsi être enregistrés comme fichiers déchiffrés une fois à destination.
- Définissez la clé de Registre suivante :
  - Chemin d’accès = HKLM\Software\Policies\Microsoft\Windows\System
  - Type de valeur = DWORD
  - Nom = CopyFileAllowDecryptedRemoteDestination
  - Valeur = 1

Notez bien que la définition de la clé de Registre affecte toutes les opérations de copie sur les partages réseau.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème.
