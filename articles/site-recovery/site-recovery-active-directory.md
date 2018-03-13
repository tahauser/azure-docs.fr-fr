---
title: "Protéger Active Directory et DNS avec Azure Site Recovery | Documents Microsoft"
description: "Cet article décrit comment implémenter une solution de récupération d’urgence pour Active Directory à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: e07b868883b0154ad38ba2f7f51dd2db663525a0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Utiliser Azure Site Recovery pour protéger Active Directory et DNS

Les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lorsque vous configurez une récupération d’urgence pour des applications, pour vous assurer que l’application fonctionne correctement, vous devez souvent récupérer Active Directory et DNS avant d’autres composants d’application.

Vous pouvez utiliser [Site Recovery](site-recovery-overview.md) pour créer une récupération d’urgence pour Active Directory. Quand une interruption se produit, vous pouvez déclencher un basculement. Vous pouvez avoir un Active Directory opérationnel quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications dans votre site principal, par exemple pour SharePoint et SAP, vous pouvez basculer le site complet. Vous pouvez commencer par basculer Active Directory à l’aide de Site Recovery. Basculez ensuite les autres applications à l’aide des plans de récupération spécifiques à l’application.

Cet article explique comment créer une solution de récupération d’urgence pour Active Directory. Il inclut les conditions préalables et les instructions de basculement. Avant de commencer, vous devez vous familiariser avec Active Directory et Site Recovery.

## <a name="prerequisites"></a>configuration requise

* Si vous procédez à une récupération vers Azure, [préparez les ressources Azure](tutorial-prepare-azure.md), y compris un abonnement, un réseau virtuel Azure, un compte de stockage et un coffre Recovery Services.
* Vérifiez les [exigences de prise en charge](site-recovery-support-matrix-to-azure.md) pour tous les composants.

## <a name="replicate-the-domain-controller"></a>Répliquer le contrôleur de domaine

Vous devez configurer la [réplication Site Recovery](#enable-protection-using-site-recovery) sur au moins une machine virtuelle hébergeant un contrôleur de domaine ou DNS. Si vous avez [plusieurs contrôleurs de domaine](#environment-with-multiple-domain-controllers) dans votre environnement, vous devez également configurer un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible. Le contrôleur de domaine supplémentaire peut être dans Azure ou dans un centre de données local secondaire.

### <a name="single-domain-controller"></a>Contrôleur de domaine unique
Si vous n’avez que quelques applications et un seul contrôleur de domaine, vous pouvez souhaiter basculer le site entier. Dans ce cas, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le site cible (soit dans Azure, soit dans un centre de données local secondaire). Vous pouvez utiliser le même contrôleur de domaine répliqué ou une machine virtuelle de DNS pour [tester le basculement](#test-failover-considerations).

### <a name="multiple-domain-controllers"></a>Contrôleurs de domaines multiples
Si vous avez beaucoup d’applications et plus d’un contrôleur de domaine dans votre environnement, ou si vous envisagez de basculer plusieurs applications à la fois, outre la réplication de la machine virtuelle du contrôleur de domaine avec Site Recovery, nous vous recommandons de configurer un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible (soit dans Azure, soit dans un centre de données local secondaire). Pour [tester le basculement](#test-failover-considerations), vous pouvez utiliser le contrôleur de domaine répliqué par Site Recovery. Pour le basculement, vous pouvez utiliser le contrôleur de domaine supplémentaire sur le site cible.

## <a name="enable-protection-with-site-recovery"></a>Activer la protection à l'aide de Site Recovery

Vous pouvez utiliser le Site Recovery pour protéger la machine virtuelle qui héberge le contrôleur de domaine ou le DNS.

### <a name="protect-the-vm"></a>Protéger la machine virtuelle
Le contrôleur de domaine répliqué à l’aide Site de Recovery est utilisé pour [tester le basculement](#test-failover-considerations). Assurez-vous qu’il présente la configuration requise suivante :

1. Le contrôleur de domaine est un serveur de catalogue global.
2. Le contrôleur de domaine doit être le propriétaire du rôle FSMO pour les rôles qui sont nécessaires pendant un test de basculement. Autrement, ces rôles doivent être [saisis](http://aka.ms/ad_seize_fsmo) après le basculement.

### <a name="configure-vm-network-settings"></a>Configurer les paramètres des réseaux de machines virtuelles
Pour la machine virtuelle hébergeant le contrôleur de domaine ou le DNS, dans Site Recovery, configurez les paramètres réseau sous les paramètres **Calcul et réseau** de l’ordinateur virtuel répliqué. Cela garantit que la machine virtuelle est attachée au réseau approprié après le basculement.

## <a name="protect-active-directory"></a>Protéger Active Directory

### <a name="site-to-site-protection"></a>Protection de site à site
Créez un contrôleur de domaine sur le site secondaire. Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le nom du même domaine que celui utilisé sur le site principal. Vous pouvez utiliser le composant logiciel enfichable **Sites et services Active Directory** pour configurer les paramètres sur l'objet du lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de sites, vous pouvez contrôler le moment auquel la réplication a lieu entre au moins deux sites, ainsi que sa fréquence. Pour plus d’informations, voir [Planification de la réplication entre sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Protection de site vers Azure
Commencez par [créer un contrôleur de domaine de réplication dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine que celui utilisé sur le site principal.

Ensuite, [reconfigurez le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) afin de l’utiliser dans Azure.

![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Protection Azure vers Azure
Commencez par [créer un contrôleur de domaine de réplication dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine que celui utilisé sur le site principal.

Ensuite, [reconfigurez le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) afin de l’utiliser dans Azure.

## <a name="test-failover-considerations"></a>Considérations en matière de test de basculement
Pour éviter tout impact sur les charges de travail de production, le test de basculement est effectué dans un réseau isolé du réseau de production.

La plupart des applications nécessitent la présence d’un contrôleur de domaine ou d’un serveur DNS. Par conséquent, avant que l’application bascule, vous devez créer dans le réseau isolé un contrôleur de domaine à utiliser pour le test de basculement. Pour ce faire, le méthode la plus simple consiste à utiliser Site Recovery pour répliquer une machine virtuelle hébergeant un contrôleur de domaine ou un DNS. Ensuite, effectuez un test de basculement de la machine virtuelle du contrôleur de domaine avant d’exécuter un test de basculement du plan de récupération pour l’application. Voici comment procéder :

1. Utilisez Site Recovery pour [répliquer](site-recovery-replicate-vmware-to-azure.md) la machine virtuelle hébergeant le contrôleur de domaine ou DNS.
2. Créez un réseau isolé. Par défaut, tout réseau virtuel que vous créez dans Azure est isolé d’autres réseaux. Nous vous recommandons d’utiliser pour ce réseau la plage d’adresses IP que vous utilisez dans votre réseau de production. N’activez pas la connectivité de site à site sur ce réseau.
3. Fournissez une adresse IP DNS dans le réseau isolé. Utilisez l’adresse IP que vous prévoyez pour la machine virtuelle du DNS. Si vous répliquez sur Azure, fournissez l’adresse IP de la machine virtuelle utilisée lors du basculement. Pour entrer l’adresse IP, dans la machine virtuelle répliquée, dans les paramètres **Calcul et réseau**, sélectionnez les paramètres **Adresse IP cible**.

    ![Réseau de test Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery tente de créer les machines virtuelles de test dans un sous-réseau du même nom, et en utilisant l’adresse IP fournie dans les paramètres **Calcul et réseau** de la machine virtuelle. Si aucun sous-réseau du même nom n’est disponible dans le réseau virtuel Azure fourni pour le test de basculement, la machine virtuelle de test est créée dans le sous-réseau en première position dans l’ordre alphabétique.
    >
    > Si l’adresse IP cible fait partie du sous-réseau sélectionné, Site Recovery tente de créer la machine virtuelle pour le test de basculement à l’aide de l’adresse IP cible. Si l’adresse IP cible ne fait pas partie du sous-réseau sélectionné, la machine virtuelle pour le test de basculement est créée à l’aide de l’adresse IP disponible suivante dans le sous-réseau sélectionné.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Tester le basculement vers un site secondaire

1. Si vous répliquez sur un autre site local et utilisez le protocole DHCP, [configurez DNS et DHCP pour le test de basculement](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Effectuez un test de basculement de la machine virtuelle du contrôleur de domaine qui s’exécute dans le réseau isolé. Pour tester le basculement, utilisez le dernier point de récupération *cohérent par rapport à l’application* disponible de la machine virtuelle du contrôleur de domaine.
3. Exécutez un test de basculement pour le plan de récupération contenant les machines virtuelles sur lesquelles l’application s’exécute.
4. Une fois le test terminé, *nettoyez le test de basculement* sur la machine virtuelle du contrôleur de domaine. Cette étape supprime le contrôleur de domaine qui a été créé pour le test de basculement.


### <a name="remove-references-to-other-domain-controllers"></a>Supprimer les références à d’autres contrôleurs de domaine
Lorsque vous lancez un test de basculement, n’incluez pas tous les contrôleurs de domaine dans le réseau de test. Pour supprimer les références à d’autres contrôleurs de domaine existant dans votre environnement de production, vous devez peut-être [saisir les rôles FSMO Active Directory](http://aka.ms/ad_seize_fsmo) et effectuer [un nettoyage des métadonnées](https://technet.microsoft.com/library/cc816907.aspx) pour les contrôleurs de domaine manquants.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problèmes causés par les dispositifs de protection de la virtualisation

> [!IMPORTANT]
> Certaines des configurations décrites dans cette section suivante ne sont pas des configurations de contrôleur de domaine standard ou par défaut. Si vous ne souhaitez pas apporter ces modifications à un contrôleur de domaine de production, vous pouvez créer un contrôleur de domaine dédié pour Site Recovery, à utiliser pour le test de basculement. Apportez ces modifications uniquement à ce contrôleur de domaine.  
>
>

Depuis la publication de Windows Server 2012, [les dispositifs de protection supplémentaires sont intégrés dans Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ces dispositifs protègent les contrôleurs de domaine virtualisés contre les restaurations de l’USN si la plateforme de l’hyperviseur sous-jacent prend en charge **VM-GenerationID**. Azure prend en charge **VM-GenerationID**. C’est pourquoi les contrôleurs de domaine qui exécutent Windows Server 2012 ou version ultérieure sur des machines virtuelles Azure disposent de ces dispositifs de protection supplémentaires.


Quand **VM-GenerationID** est réinitialisé, la valeur **InvocationID** de la base de données AD DS l’est également. En outre, le pool RID est supprimé et SYSVOL est marqué comme ne faisant pas autorité. Pour plus d’informations, voir [Présentation de la virtualisation d’Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) et [Safely Virtualizing DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/) (Virtualisation DFSR sécurisée).

Un basculement vers Azure peut entraîner une réinitialisation de **VM-GenerationID**. La réinitialisation de **VM-GenerationID** déclenche ses dispositifs de protection supplémentaires au démarrage de la machine virtuelle du contrôleur de domaine dans Azure. Cela peut *retarder sensiblement* le moment où il est possible de se connecter à la machine virtuelle du contrôleur de domaine.

Étant donné que ce contrôleur de domaine n’est utilisé que pour le test de basculement, aucun dispositif de protection de la virtualisation n’est nécessaire. Pour vous assurer que la valeur **VM-GenerationID** pour la machine virtuelle du contrôleur de domaine ne change pas, vous pouvez modifier la valeur de DWORD suivante en **4** dans le contrôleur de domaine local :


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptômes des dispositifs de protection de la virtualisation

Si des dispositifs de protection de la virtualisation sont déclenchés après un test de basculement, il se peut que vous observiez un ou plusieurs des symptômes suivants :  

* La valeur **GenerationID** change.

    ![Modification de l’ID de génération](./media/site-recovery-active-directory/Event2170.png)

* La valeur **InvocationID** change.

    ![Modification de l’ID d’appel](./media/site-recovery-active-directory/Event1109.png)

* Les partages SYSVOL et NETLOGON ne sont pas disponibles.

    ![Partage SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![NTFRS SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Les bases de données DFSR sont supprimées.

    ![Les bases de données DFSR sont supprimées](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Résoudre les problèmes liés au contrôleur de domaine survenant pendant un test de basculement

> [!IMPORTANT]
> Certaines des configurations décrites dans la section suivante ne sont pas des configurations de contrôleur de domaine standard ou par défaut. Si vous ne souhaitez pas appliquer ces modifications à un contrôleur de domaine de production, vous pouvez créer un contrôleur de domaine dédié au test de basculement Site Recovery. Apportez les modifications uniquement à ce contrôleur de domaine dédié.  
>
>

1. À l’invite de commandes, exécutez la commande suivante pour vérifier si les dossiers SYSVOL et NETLOGON sont partagés :

    `NET SHARE`

2. À l’invite de commandes, exécutez la commande suivante pour vous assurer que le contrôleur de domaine fonctionne correctement :

    `dcdiag /v > dcdiag.txt`

3. Dans le journal de sortie, recherchez le texte suivant. Le texte confirme que le contrôleur de domaine fonctionne correctement.

    * « passed test Connectivity »
    * « passed test Advertising »
    * « passed test MachineAccount »

Si les conditions ci-dessus sont remplies, il est probable que le contrôleur de domaine fonctionne correctement. Autrement, effectuez les tâches suivantes :

1. Effectuez une restauration faisant autorité du contrôleur de domaine. Gardez à l’esprit les informations suivantes :
    * Bien que nous ne recommandions pas une [réplication FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), si vous utilisez une telle réplication, procédez comme pour une restauration faisant autorité. Le processus est décrit dans [Utilisation de la clé de Registre BurFlags pour réinitialiser le service de réplication de fichiers](https://support.microsoft.com/kb/290762).

        Pour plus d’informations sur BurFlags, voir le billet de blog concernant [D2 et D4](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Si vous utilisez une réplication DFSR, procédez comme pour une restauration faisant autorité. Le processus est décrit dans [Comment faire pour forcer une synchronisation faisant autorité et ne faisant pas autorité pour SYSVOL de réplication DFSR (comme « D4/D2 » pour FRS)](https://support.microsoft.com/kb/2218556).

        Vous pouvez également utiliser les fonctions PowerShell. Pour plus d’informations, voir [Fonctions PowerShell de restauration faisant autorité/ne faisant pas autorisé pour DFSR-SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Contournez l’obligation de synchronisation initiale en définissant la clé de Registre suivante sur **0** dans le contrôleur de domaine local. Si la valeur DWORD n’existe pas, vous pouvez la créer sous le nœud **Paramètres**.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Pour plus d’informations, voir [Résoudre les problèmes liés à l’ID d’événement DNS 4013 : le serveur DNS n’a pas pu charger les zones DNS intégrées d’Active Directory](https://support.microsoft.com/kb/2001093).

3. Désactivez l’exigence qu’un serveur de catalogue global soit disponible pour valider la connexion de l’utilisateur. Pour ce faire, dans le contrôleur de domaine local, définissez la clé de Registre suivante sur **1**. Si la valeur DWORD n’existe pas, vous pouvez la créer sous le nœud **Lsa**.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Pour plus d’informations, voir [Désactiver l’exigence qu’un serveur de catalogue global soit disponible pour valider les connexions de l’utilisateur](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS et contrôleur de domaine sur différentes machines
Si le DNS figure sur une machine virtuelle différente de celle du contrôleur de domaine, vous devez créer une machine virtuelle du DNS pour le test de basculement. Si le DNS et le contrôleur de domaine ne figurent pas sur le même machine virtuelle, vous pouvez ignorer cette section.

Vous pouvez utiliser un nouveau serveur DNS et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS portant le nom contoso.com. Les entrées correspondant à Active Directory doivent être mises à jour dans le DNS comme suit :

1. Vérifiez que ces paramètres sont en place avant tout démarrage d’une autre machine virtuelle dans le plan de récupération :
   * La zone doit être nommée en fonction du nom racine de la forêt.
   * La zone doit être sauvegardée dans un fichier.
   * La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
   * Le programme de résolution de la machine virtuelle hébergeant le contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle du DNS.

2. Exécutez la commande suivante sur la machine virtuelle hébergeant le contrôleur de domaine :

    `nltest /dsregdns`

3. Exécutez les commandes suivantes pour ajouter une zone sur le serveur DNS, autoriser les mises à jour non sécurisées et ajouter une entrée pour la zone au DNS :

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [protection des charges de travail d’entreprise avec Azure Site Recovery](site-recovery-workload.md).
