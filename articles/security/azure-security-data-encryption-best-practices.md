---
title: "Bonnes pratiques en matière de chiffrement et de sécurité des données | Microsoft Docs"
description: "Cet article détaille les meilleures pratiques en matière de chiffrement et de sécurité des données, à l’aide de capacités Azure intégrées."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 0cebc7ae5279b720e8fd0d6c986e1706d944476f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Meilleures pratiques en matière de chiffrement et de sécurité des données - Azure
Pour assurer la protection des données dans le cloud, l’un des facteurs clés consiste à tenir compte des états que les données peuvent présenter, mais aussi des contrôles disponibles pour ces états. Dans le cadre des meilleures pratiques en termes de chiffrement et de sécurité des données d’Azure, les recommandations que nous proposons s’articulent autour des états suivants des données :

* Au repos : cela inclut tous les objets de stockage, conteneurs et types d’informations présents de manière statique sur un support physique, qu’il s’agisse d’un disque magnétique ou d’un disque optique.
* En transit : lorsque des données sont transférées entre des composants, des emplacements ou des programmes (sur le réseau, par exemple) via un bus de service (depuis un emplacement local vers le cloud, ou vice-versa, y compris via des connexions hybrides comme ExpressRoute), ou lors d’un processus d’entrée/sortie, on parle de données « en transit ».

Dans cet article, nous aborderons différentes meilleures pratiques d’Azure en matière de chiffrement et de sécurité des données. Ces meilleures pratiques sont issues de notre expérience dans le domaine du chiffrement et de la sécurité des données Azure, mais également de celle des clients, comme vous.

Pour chaque meilleure pratique, nous allons détailler les éléments suivants :

* Nature de la meilleure pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la meilleure pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plate-forme Azure disponibles lors de la rédaction. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Les meilleures pratiques en matière de chiffrement et de sécurité des données Azure qui sont abordées dans le cadre de cet article sont les suivantes :

* Application de l’authentification multifacteur via Azure Multi-Factor Authentication
* Utilisation du contrôle d’accès en fonction du rôle (RBAC)
* Chiffrement des machines virtuelles Azure
* Utilisation de modèles de sécurité matériels
* Gestion de stations de travail sécurisées
* Activation du chiffrement de données SQL
* Protection des données en transit
* Application du chiffrement des données au niveau fichier

## <a name="enforce-multi-factor-authentication"></a>Application de l’authentification multifacteur via Azure Multi-Factor Authentication
Pour permettre le contrôle et l’octroi d’un accès aux données dans Microsoft Azure, il est avant tout nécessaire d’authentifier l’utilisateur. [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) est une méthode permettant de vérifier l’identité de l’utilisateur grâce à d’autres moyens que les seuls nom d’utilisateur et mot de passe. Cette méthode contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur, qui souhaite bénéficier d’un processus d’authentification simple.

En activant Azure MFA pour vos utilisateurs, vous ajoutez une deuxième couche de sécurité aux connexions et transactions des utilisateurs. Dans ce cas, une transaction peut accéder à un document situé sur un serveur de fichiers ou sur votre site SharePoint Online. Azure MFA permet également au département informatique de réduire le risque d’accès aux données de l’organisation par un compte compromis.

Par exemple : si vous appliquez l’authentification multi-facteur d’Azure MFA pour vos utilisateurs et la configurez afin qu’elle utilise un appel téléphonique ou un message texte à titre de vérification, un pirate informatique ayant frauduleusement obtenu les informations d’identification de l’utilisateur ne peut pas accéder aux ressources, car il ne dispose pas du téléphone de cet utilisateur. Les organisations qui n’ajoutent pas cette couche supplémentaire de protection d’identité sont plus sensibles au vol d’informations d’identification, susceptible de compromettre des données.

Les organisations souhaitant conserver le contrôle de l’authentification localement peuvent recourir au [serveur Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), ou « MFA local ». Grâce à cette méthode, vous pourrez toujours appliquer l’authentification multi-facteur, tout en conservant le serveur MFA en local.

Pour en savoir plus sur Azure Multi-Factor Authentication, voir [Prise en main avec Azure Multi-Factor Authentication dans le cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Utilisation du contrôle d’accès en fonction du rôle (RBAC)
Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Ces principes sont impératifs pour les organisations qui veulent appliquer des stratégies de sécurité portant sur l’accès aux données. La fonction de contrôle d’accès en fonction du rôle (RBAC) d’Azure peut être utilisée pour affecter des autorisations aux utilisateurs, groupes et des applications, à une certaine étendue. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement.

Vous pouvez tirer parti des [rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md) dans Azure pour affecter des privilèges aux utilisateurs. Envisagez l’utilisation du rôle *Contributeur de comptes de stockage* pour les opérateurs de cloud qui ont besoin de gérer des comptes de stockage, et du rôle *Contributeur de comptes de stockage classiques* pour ceux qui gèrent des comptes de stockage classiques. Pour les opérateurs de cloud qui doivent gérer des machines virtuelles et des comptes de stockage, vous pouvez recourir au rôle *Contributeur de machines virtuelles*.

Les organisations qui n’appliquent aucun contrôle d’accès aux données via des fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. Cela risque d’entraîner la compromission des données, car les utilisateurs peuvent bénéficier d’un accès non justifié à certaines données.

Vous pouvez en savoir plus sur la fonction RBAC d’Azure en lisant l’article [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Chiffrement des machines virtuelles Azure
Pour de nombreuses organisations, le [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire du processus de gestion de la confidentialité, de la conformité et de la souveraineté des données. Azure Disk Encryption permet aux administrateurs informatiques de chiffrer des disques de machines virtuelles Windows et Linux IaaS. Azure Disk Encryption s’appuie sur la fonctionnalité standard BitLocker Windows et la fonctionnalité DM-Crypt de Linux pour assurer le chiffrement de volume du système d’exploitation et des disques de données.

Vous pouvez tirer parti d’Azure Disk Encryption pour protéger vos données, afin de répondre aux exigences de l’entreprise en matière de sécurité et de conformité. Les organisations doivent également envisager d’utiliser le chiffrement pour réduire les risques liés aux accès non autorisés. Avant d’écrire des données sensibles sur les lecteurs, il est également recommandé de chiffrer ces derniers.

Veillez à chiffrer le volume de démarrage ainsi que les volumes de données de vos machines virtuelles, afin de protéger les données au repos associées à votre compte de stockage Azure. Protégez les clés de chiffrement et les secrets en tirant parti d’[Azure Key Vault](../key-vault/key-vault-whatis.md).

Pour vos serveurs Windows Server locaux, tenez compte des meilleures pratiques suivantes en matière de chiffrement :

* Utilisez [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) pour le chiffrement des données.
* Stockez les informations de récupération dans AD DS.
* Si vous craignez que les clés BitLocker n’aient été compromises, nous vous recommandons de formater le lecteur, afin de supprimer toutes les instances de métadonnées BitLocker qu’il inclut, ou de déchiffrer, puis de chiffrer à nouveau l’intégralité du lecteur.

Les organisations qui n’appliquent aucun chiffrement des données courent davantage le risque d’être exposées à des problèmes d’intégrité des données (par exemple, des utilisateurs malveillants ou non autorisés s’efforçant de dérober des données ou d’accéder de manière frauduleuse à des données en clair, au moyen de compte compromis). Outre ces risques, les entreprises devant se conformer aux réglementations du secteur doivent prouver leur diligence ; elles utilisent les contrôles de sécurité appropriés pour améliorer la sécurité des données.

Vous pouvez en savoir plus sur Azure Disk Encryption en lisant l’article [Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utilisation de modèles de sécurité matériels
Les solutions de chiffrement du secteur utilisent des clés secrètes pour chiffrer les données. Pour cette raison, il est vital que ces clés soient stockées de manière appropriée. La gestion des clés devient partie intégrante de la protection des données, dans la mesure où elle est utilisée pour stocker les clés secrètes permettant de chiffrer les données.

Azure Disk Encryption utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour assurer le contrôle et la gestion des secrets et clés de chiffrement de disque au sein de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques de machines virtuelles sont chiffrées au repos dans votre Stockage Azure. Nous vous recommandons d’utiliser Azure Key Vault pour auditer les clés et l’utilisation de la stratégie.

Si vous ne mettez pas en place les contrôles de sécurité appropriés afin de protéger les clés secrètes utilisées pour chiffrer vos données, vous courez de nombreux risques. Si des utilisateurs malveillants ont accès aux clés secrètes, ils seront en mesure de déchiffrer les données et d’avoir accès à des informations confidentielles, le cas échéant.

Vous pouvez bénéficier de recommandations générales en matière de gestion des certificats dans Azure en lisant l’article [Certificate Management in Azure: Do’s and Don’ts (Gestion de certificats dans Azure : meilleures pratiques et écueils)](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Pour en savoir plus sur Azure Key Vault, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gestion de stations de travail sécurisées
Dans la mesure où la grande majorité des atteintes ciblent l’utilisateur final, le point de terminaison devient l’un des principaux points d’attaque. Si un pirate informatique compromet le point de terminaison, il peut exploiter les informations d’identification pour accéder aux données de l’organisation. La plupart des attaques visant le point de terminaison sont capables d’exploiter le fait que les utilisateurs finaux occupent le rôle d’administrateur dans leurs stations de travail locales.

Vous pouvez réduire ces risques en utilisant une station de travail de gestion sécurisée. Nous vous recommandons d’utiliser une [station de travail avec accès privilégié](https://technet.microsoft.com/library/mt634654.aspx) (Privileged Access Workstation, PAW) pour réduire la surface d’attaque au sein des stations de travail. Ces stations de travail de gestion sécurisées peuvent vous aider à limiter certaines attaques, afin d’optimiser la sécurité de vos données. Utilisez les stations de travail avec accès privilégié pour renforcer et optimiser la protection de votre station de travail. Il s’agit d’une étape importante de la procédure visant à assurer une sécurité élevée concernant la protection des données, ainsi que les tâches et comptes sensibles.

L’absence de protection des points de terminaison peut compromettre la sécurité de vos données. Pour cette raison, assurez-vous que des stratégies de sécurité sont mises en place sur l’ensemble des appareils utilisés pour consommer les données, quel que soit l’emplacement de ces dernières (sur le cloud ou en local).

Vous pouvez en savoir plus sur les stations de travail avec accès privilégié en lisant l’article [Securing Privileged Access (Sécurisation de l’accès privilégié)](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Activation du chiffrement de données SQL
La fonctionnalité [TDE (Transparent Data Encryption) d’Azure SQL Database](https://msdn.microsoft.com/library/dn948096.aspx) protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans qu’il soit nécessaire de modifier l’application.  TDE chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données.

Même lorsque l’ensemble du stockage est chiffré, il est très important de chiffrer la base de données également. Cela permet d’implémenter un mécanisme approfondi de défense visant à protéger les données. Si vous utilisez [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) et souhaitez protéger des données sensibles telles que des numéros de carte de crédit ou de sécurité sociale (USA), vous pouvez chiffrer vos bases de données au moyen du chiffrement AES 256 bits, conforme à la norme FIPS 140-2, qui répond aux exigences de nombreuses normes du secteur (HIPAA, PC, etc.).

Il est important de comprendre que les fichiers liés à l’[extension de pool de mémoires tampons](https://msdn.microsoft.com/library/dn133176.aspx) ne sont pas chiffrés quand une base de données est chiffrée à l’aide de TDE. Vous devez utiliser des outils de chiffrement au niveau du système de fichiers comme BitLocker ou le [système de fichiers EFS](https://technet.microsoft.com/library/cc700811.aspx) pour les fichiers liés à l’extension de pool de mémoires tampons.

Comme un utilisateur autorisé (administrateur de sécurité ou de base de données, par exemple) peut accéder aux données même si la base de données est chiffrée via TDE, vous devez également suivre les recommandations ci-dessous :

* Authentification SQL au niveau de la base de données.
* Authentification Azure AD à l’aide des rôles RBAC.
* Les utilisateurs et les applications doivent utiliser des comptes distincts pour l’authentification. De cette façon, vous pouvez limiter les autorisations accordées aux utilisateurs et aux applications et réduire les risques d’activité malveillante.
* Implémentez la sécurité au niveau de la base de données en utilisant des rôles de base de données fixes (comme db_datareader ou db_datawriter). Vous pouvez aussi créer des rôles personnalisés pour votre application, afin d’accorder des permissions explicites aux objets de base de données sélectionnés.

Les organisations qui n’appliquent aucun chiffrement au niveau de la base de données peuvent être plus vulnérables aux attaques susceptibles de compromettre les données des bases de données SQL.

Vous pouvez en savoir plus sur le chiffrement TDE de SQL en lisant l’article [Transparent Data Encryption avec Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Protection des données en transit
La protection des données en transit doit être un aspect essentiel de votre stratégie de protection des données. Comme les données transitent entre différents emplacements, dans les deux sens, nous vous recommandons d’utiliser systématiquement les protocoles SSL/TLS pour ces déplacements de données. Dans certains cas, vous souhaiterez isoler l’intégralité du canal de communication entre votre infrastructure locale et sur le cloud, via un réseau privé virtuel (VPN).

Pour les données qui transitent entre votre infrastructure locale et Azure, vous devez envisager le recours aux dispositifs de protection appropriés, comme HTTPS ou VPN.

Pour les organisations devant sécuriser l’accès à Azure à partir de plusieurs postes de travail locaux, utilisez la fonction [VPN de site à site d’Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Pour les organisations devant sécuriser l’accès à Azure à partir d’un seul poste de travail local, utilisez la fonction [VPN de point à site d’Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Les jeux de données volumineux peuvent être transmis par le biais d’une liaison réseau étendu haut débit dédiée, comme [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vous choisissez d’utiliser ExpressRoute, vous pouvez également chiffrer les données au niveau des applications par le biais de [SSL/TLS](https://support.microsoft.com/kb/257591) ou d’autres protocoles, pour optimiser la protection.

Si vous interagissez avec Azure Storage via le portail Azure, toutes les transactions se produisent via HTTPS. L’[API de stockage REST](https://msdn.microsoft.com/library/azure/dd179355.aspx) par le biais de HTTPS peut également être utilisée pour interagir avec [Stockage Azure](https://azure.microsoft.com/services/storage/) et [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Les organisations qui ne parviennent pas à protéger les données en transit sont plus sensibles aux [attaques d’intercepteur](https://technet.microsoft.com/library/gg195821.aspx), aux [écoutes électroniques](https://technet.microsoft.com/library/gg195641.aspx) et au piratage de session. Ces attaques peuvent être la première étape d’un processus visant à accéder à des données confidentielles.

Vous pouvez en savoir plus sur l’option de VPN Azure en lisant l’article [Planification et conception de la passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Application du chiffrement des données au niveau fichier
Le chiffrement du fichier lui-même, quel que soit son emplacement, constitue une autre couche de protection susceptible d’optimiser la sécurité de vos données.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utilise des stratégies de chiffrement, d’identité et d’autorisation pour vous aider à sécuriser vos fichiers et vos e-mails. Azure RMS peut fonctionner sur plusieurs appareils (téléphones, tablettes et PC), en protégeant les données au sein de votre organisation et en dehors de cette dernière. Cette fonctionnalité est possible, car Azure RMS ajoute un niveau de protection qui reste avec les données, même lorsqu’elles quittent les limites de votre organisation.

Quand vous utilisez Azure RMS pour protéger vos fichiers, vous recourez au chiffrement standard avec prise en charge complète de la norme [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Lorsque vous tirez parti d’Azure RMS pour la protection des données, vous avez l’assurance que la protection reste avec le fichier, même s’il est copié sur un stockage qui n’est pas sous le contrôle du département informatique (service de stockage cloud, par exemple). Il en va de même pour les fichiers partagés par e-mail ; ils sont protégés en tant que pièces jointes à un message électronique. Des instructions expliquant comment ouvrir la pièce jointe protégée sont fournies.

Si vous planifiez l’adoption d’Azure RMS, nous vous recommandons de suivre les conseils ci-après :

* Installez l’[application de partage RMS](https://technet.microsoft.com/library/dn339006.aspx) Cette application s’intègre avec les applications Office en installant un complément Office, afin que les utilisateurs puissent directement protéger leurs fichiers, en toute simplicité.
* Configurez des applications et des services pour prendre en charge Azure RMS.
* Créez des [modèles personnalisés](https://technet.microsoft.com/library/dn642472.aspx) qui reflètent les besoins de votre entreprise (exemple : un modèle portant sur des données ultra-secrètes, qui doit être appliqué à tous les e-mails ultra-secrets).

Les organisations bénéficiant d’une [classification](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) et d’une protection insuffisantes des données sont plus vulnérables aux fuites de données. Si la protection des fichiers n’est pas appropriée, les organisations ne pourront pas optimiser leur visibilité, ni surveiller les abus ou empêcher tout accès malveillant aux fichiers.

Vous pouvez en savoir plus sur Azure RMS en lisant l’article [Prise en main d’Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
