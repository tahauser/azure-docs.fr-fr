---
title: "Protéger Active Directory et DNS avec Azure Site Recovery | Documents Microsoft"
description: "Cet article décrit comment implémenter une solution de récupération d'urgence pour Active Directory à l'aide d'Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: 5db2d424c176428d31fb99fd8288120f18fcac7a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protéger Active Directory et DNS avec Azure Site Recovery
Les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lorsque vous créez une solution de récupération d’urgence pour des applications, vous devez souvent récupérer Active Directory et DNS avant les autres composants d’application pour vous assurer que l’application fonctionne correctement.

À l’aide de Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisé complet pour Active Directory. En cas d’interruption, vous pouvez lancer un basculement en quelques secondes, où que vous soyez, et bénéficier d’un Active Directory opérationnel en quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications, telles que SharePoint et SAP dans votre site principal et que vous souhaitez basculer le site intégral, vous pouvez basculer Active Directory en premier lieu à l’aide de Site Recovery, puis basculer les autres applications en utilisant des plans de récupération spécifiques aux applications.

Cet article explique comment créer une solution de récupération d’urgence pour Active Directory, effectuer des basculements à l’aide d’un plan de récupération d’urgence accessible en un clic, de configurations prises en charge et de conditions préalables.  Vous devez être capable d’utiliser Active Directory et Azure Site Recovery avant de commencer.

## <a name="prerequisites"></a>configuration requise
* Un coffre Recovery Services dans un abonnement Microsoft Azure.
* Si vous répliquez vers Azure, [préparez](tutorial-prepare-azure.md) des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
* Examinez les spécifications de prise en charge de tous les composants.

## <a name="replicating-domain-controller"></a>Réplication d’un contrôleur de domaine

Vous devez configurer la [réplication Site Recovery](#enable-protection-using-site-recovery) sur au moins une machine virtuelle qui héberge le contrôleur de domaine et le DNS. Si votre environnement contient [plusieurs contrôleurs de domaine](#environment-with-multiple-domain-controllers), en plus de répliquer la machine virtuelle de contrôleur de domaine, vous devez également configurer un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible (Azure ou un centre de données local secondaire).

### <a name="single-domain-controller-environment"></a>Environnement avec un seul contrôleur de domaine
Si vous avez quelques applications et un seul contrôleur de domaine, et que vous souhaitez basculer l’ensemble du site en même temps, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le site cible (Azure ou un centre de données local secondaire). La même machine virtuelle de contrôleur de domaine/DNS répliquée peut également être utilisée à des fins de [test de basculement](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Environnement avec plusieurs contrôleurs de domaine
Si vous avez beaucoup d’applications et plus d’un contrôleur de domaine dans l’environnement, ou si vous envisagez de basculer plusieurs applications à la fois, outre la réplication de la machine virtuelle du contrôleur de domaine avec Site Recovery, nous vous recommandons de configurer un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible (Azure ou un centre de données local secondaire). Pour effectuer un [test de basculement](#test-failover-considerations), vous pouvez utiliser le contrôleur de domaine répliqué par Site Recovery et pour le basculement, le contrôleur de domaine supplémentaire du site cible.

## <a name="enable-protection-using-site-recovery"></a>Activer la protection à l'aide de Site Recovery
### <a name="protect-the-virtual-machine"></a>Protéger la machine virtuelle
Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans Site Recovery. Le contrôleur de domaine répliqué à l’aide Site de Recovery est utilisé pour effectuer le [test de basculement](#test-failover-considerations). Assurez-vous qu’il répond aux exigences suivantes :

1. Le contrôleur de domaine est un serveur de catalogue global
2. Le contrôleur de domaine doit être le propriétaire du rôle FSMO pour les rôles qui seront nécessaires pendant un test de basculement (sinon, ces rôles devront être [pris](http://aka.ms/ad_seize_fsmo) après le basculement)

### <a name="configure-virtual-machine-network-settings"></a>Configurer les paramètres réseau de la machine virtuelle
Pour la machine virtuelle du contrôleur de domaine/DNS, configurez les paramètres réseau dans Site Recovery, sous les paramètres Calcul et réseau de la machine virtuelle répliquée. Vous avez ainsi la garantie que la machine virtuelle sera associée au bon réseau après le basculement.

## <a name="protect-active-directory-with-active-directory-replication"></a>Protéger Active Directory avec la réplication Active Directory
### <a name="site-to-site-protection"></a>Protection de site à site
Créez un contrôleur de domaine sur le site secondaire. Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le nom du même domaine que celui utilisé sur le site principal. Vous pouvez utiliser le composant logiciel enfichable **Sites et services Active Directory** pour configurer les paramètres sur l'objet du lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de sites, vous pouvez contrôler le moment où la réplication a lieu entre deux sites ou plus, ainsi que la fréquence. Pour plus d’informations, consultez [Planification de la réplication entre des sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Protection de site vers Azure
Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine que celui utilisé sur le site principal.

[Reconfigurez ensuite le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)afin de l’utiliser dans Azure.

![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Protection Azure vers Azure
Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine que celui utilisé sur le site principal.

[Reconfigurez ensuite le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)afin de l’utiliser dans Azure.

## <a name="test-failover-considerations"></a>Considérations en matière de test de basculement
Le test de basculement est effectué dans un réseau isolé du réseau de production pour éviter tout impact sur les charges de travail de production.

La plupart des applications requièrent également la présence d’un contrôleur de domaine et d’un serveur DNS pour fonctionner. Par conséquent, avant que l’application ne soit basculée, un contrôleur de domaine doit être créé dans le réseau isolé à utiliser pour le test de basculement. Pour ce faire, le plus simple consiste à répliquer une machine virtuelle du contrôleur de domaine/DNS avec Site Recovery. Exécutez ensuite un test de basculement de la machine virtuelle du contrôleur de domaine avant d’exécuter un test de basculement du plan de récupération de l’application. Voici comment procéder :

1. [Répliquez](site-recovery-replicate-vmware-to-azure.md) la machine virtuelle du contrôleur de domaine/DNS avec Site Recovery.
2. Créez un réseau isolé. Tout réseau virtuel créé dans Azure par défaut est isolé des autres réseaux. Il est recommandé que la plage d’adresses IP pour ce réseau soit identique à celle de votre réseau de production. N’activez pas la connectivité de site à site sur ce réseau.
3. Fournissez une adresse IP DNS dans le réseau créé, comme l'adresse IP que vous attendez que la machine virtuelle du DNS obtienne. Si vous effectuez une réplication vers Azure, indiquez l’adresse IP de la machine virtuelle utilisée lors du basculement dans le champ **Adresse IP cible** des paramètres **Calcul et réseau** de la machine virtuelle répliquée.

    ![Réseau de test Azure](./media/site-recovery-active-directory/azure-test-network.png)

> [!TIP]
> Site Recovery tente de créer les machines virtuelles de test dans un sous-réseau du même nom et à l’aide de la même adresse IP que dans les paramètres **Calcul et réseau** de la machine virtuelle. Si le réseau virtuel Azure indiqué pour le test de basculement ne contient pas de sous-réseau du même nom, la machine virtuelle de test est créée dans le premier sous-réseau dans l’ordre alphabétique. Si l’adresse IP cible fait partie du sous-réseau choisi, Site Recovery tente alors de créer la machine virtuelle de test de basculement à l’aide de l’adresse IP cible. Si l’adresse IP cible ne fait pas partie du sous-réseau choisi, alors la machine virtuelle de test de basculement est créée à l’aide de la prochaine adresse IP disponible dans le sous-réseau sélectionné.
>
>


1. Si vous répliquez vers un autre site local et que vous utilisez DHCP, suivez les instructions pour [configurer DNS et DHCP pour le test de basculement](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
2. Effectuez un test de basculement de la machine virtuelle du contrôleur de domaine dans le réseau isolé. Utilisez le dernier point de récupération **cohérent avec l’application** disponible de la machine virtuelle du contrôleur de domaine pour effectuer le test de basculement.
3. Exécutez un test de basculement pour le plan de récupération contenant des machines virtuelles de l’application.
4. Une fois le test terminé, **nettoyez le test de basculement** sur la machine virtuelle du contrôleur de domaine. Cette étape supprime le contrôleur de domaine qui a été créé pour le test de basculement.


### <a name="removing-reference-to-other-domain-controllers"></a>Suppression de la référence à d’autres contrôleurs de domaine
Lorsque vous effectuez un test de basculement, vous n’utilisez pas tous les contrôleurs de domaine dans le réseau de test. Pour supprimer la référence des autres contrôleurs de domaine qui existent dans votre environnement de production, vous devez peut-être [prendre les rôles FSMO Active Directory](http://aka.ms/ad_seize_fsmo) et effectuer [un nettoyage des métadonnées](https://technet.microsoft.com/library/cc816907.aspx) pour les contrôleurs de domaine absents.



> [!IMPORTANT]
> Certaines des configurations décrites dans la section suivante ne sont pas les configurations de contrôleur de domaine standard/par défaut. Si vous ne souhaitez pas appliquer ces modifications à un contrôleur de domaine de production, vous pouvez créer un contrôleur de domaine dédié, qui sera utilisé pour le test de basculement Site Recovery, et appliquer ces modifications à celui-ci.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problèmes en raison de dispositifs de protection de la virtualisation

À partir de Windows Server 2012, [des dispositifs de protection supplémentaires ont été intégrés dans Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ces dispositifs protègent les contrôleurs de domaine virtualisés contre les restaurations d’USN, à condition que la plateforme de l’hyperviseur sous-jacent prenne en charge VM-GenerationID. Azure prend en charge VM-GenerationID, ce qui signifie que les contrôleurs de domaine qui exécutent Windows Server 2012 ou version ultérieure sur des machines virtuelles Azure disposent de dispositifs de protection supplémentaires.


Lorsque le VM-GenerationID est réinitialisé, l’invocationID de la base de données AD DS est également réinitialisé, le pool RID est supprimé et SYSVOL est marqué comme ne faisant pas autorité. Pour plus d’informations, consultez [Présentation de la virtualisation de Services (AD DS) de domaine Active Directory (niveau 100)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) et [Safely Virtualizing DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/) (Virtualisation DFSR sécurisée).

Le basculement vers Azure peut entraîner la réinitialisation de VM-GenerationID et cela déclenche les dispositifs de protection supplémentaires au démarrage de la machine virtuelle du contrôleur de domaine dans Azure. Cela peut entraîner un **délai important** de connexion de l’utilisateur à la machine virtuelle du contrôleur de domaine. Étant donné que ce contrôleur de domaine doit être utilisé uniquement dans un test de basculement, les dispositifs de protection de la virtualisation ne sont pas nécessaires. Pour vous assurer que le VM-GenerationID de la machine virtuelle du contrôleur de domaine ne change pas, vous pouvez modifier la valeur DWORD suivante sur 4 dans le contrôleur de domaine local.


        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptômes des dispositifs de protection de la virtualisation

Si les dispositifs de protection de la virtualisation sont intervenus après un test de basculement, vous pouvez observer un ou plusieurs des symptômes suivants :  

Modification de l’ID de génération

![Modification de l’ID de génération](./media/site-recovery-active-directory/Event2170.png)

Modification de l’ID d’appel

![Modification de l’ID d’appel](./media/site-recovery-active-directory/Event1109.png)

Les partages Sysvol et Netlogon ne sont pas disponibles

![Partage Sysvol](./media/site-recovery-active-directory/sysvolshare.png)

![Sysvol Ntfrs](./media/site-recovery-active-directory/Event13565.png)

Les bases de données DFSR sont supprimées

![Suppression de base de données DFSR](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Certaines des configurations décrites dans la section suivante ne sont pas les configurations de contrôleur de domaine standard/par défaut. Si vous ne souhaitez pas appliquer ces modifications à un contrôleur de domaine de production, vous pouvez créer un contrôleur de domaine dédié, qui sera utilisé pour le test de basculement Site Recovery, et appliquer ces modifications à celui-ci.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Résolution des problèmes liés à un contrôleur de domaine survenant pendant un test de basculement


Dans une invite de commandes, exécutez la commande suivante pour vérifier si les dossiers SYSVOL et NETLOGON sont partagés :

    NET SHARE

Dans l’invite de commandes, exécutez la commande suivante pour vous assurer que le contrôleur de domaine fonctionne correctement.

    dcdiag /v > dcdiag.txt

Dans le journal de sortie, recherchez le texte suivant pour vérifier que le contrôleur de domaine fonctionne correctement.

* « passed test Connectivity »
* « passed test Advertising »
* « passed test MachineAccount »

Si les conditions ci-dessus sont remplies, il est probable que le contrôleur de domaine fonctionne correctement. Si ce n’est pas le cas, essayez les étapes ci-après.


* Effectuez une restauration faisant autorité du contrôleur de domaine.
    * Bien qu’il soit [déconseillé d’utiliser la réplication FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), si vous l’utilisez encore, suivez les étapes indiquées [ici](https://support.microsoft.com/kb/290762) pour effectuer une restauration faisant autorité. Vous trouverez plus d’informations sur les éléments Burflags dont il est question dans le lien précédent [ici](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Si vous utilisez la réplication DFSR, suivez les étapes disponibles [ici](https://support.microsoft.com/kb/2218556) pour effectuer une restauration faisant autorité. Vous pouvez également utiliser les fonctions PowerShell disponibles [ici](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) à cet effet.

* Contournez l’obligation de synchronisation initiale en définissant la clé de Registre suivante sur 0 dans le contrôleur de domaine local. Si cette valeur DWORD n’existe pas, vous pouvez la créer sous le nœud « Paramètres ». Vous trouverez plus d’informations à ce sujet [ici](https://support.microsoft.com/kb/2001093).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Désactivez l’exigence de disponibilité d’un serveur de catalogue global pour valider la connexion utilisateur en définissant la clé de Registre suivante sur 1 dans le contrôleur de domaine local. Si cette valeur DWORD n’existe pas, vous pouvez la créer sous le nœud « Lsa ». Vous trouverez plus d’informations à ce sujet [ici](http://support.microsoft.com/kb/241789).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS et contrôleur de domaine sur différentes machines
Si le DNS figure sur une machine virtuelle différente de celle du contrôleur de domaine, vous devez créer une machine virtuelle du DNS pour le test de basculement. Si le DNS et le contrôleur de domaine figurent sur la même machine virtuelle, vous pouvez ignorer cette section.

Vous pouvez utiliser un nouveau serveur DNS et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS portant le nom contoso.com. Les entrées correspondant à Active Directory doivent être mises à jour dans le système DNS, comme suit :

1. Vérifiez que ces paramètres sont en place avant toute apparition d’une autre machine virtuelle du plan de récupération :

   * La zone doit être nommée en fonction du nom racine de la forêt.
   * La zone doit être sauvegardée dans un fichier.
   * La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
   * Le programme de résolution de la machine virtuelle du contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle du DNS.
2. Exécutez la commande suivante sur la machine virtuelle du contrôleur de domaine :

    `nltest /dsregdns`
3. Ajoutez une zone sur le serveur DNS, autorisez les mises à jour non sécurisées et ajoutez une entrée pour ce dernier dans le service DNS :

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>étapes suivantes
[En savoir plus](site-recovery-workload.md) sur la protection des charges de travail d’entreprise avec Azure Site Recovery.
