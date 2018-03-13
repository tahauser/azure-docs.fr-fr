---
title: "Dépannage d’Azure Site Recovery en cas de problème ou d’erreur de réplication Azure vers Azure | Microsoft Docs"
description: "Dépannage des erreurs et des problèmes lors de la réplication de machines virtuelles Azure pour la récupération d’urgence"
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: sujayt
ms.openlocfilehash: 7292948c40b184a58eb3e27aecac28e2227a29f8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Résoudre les problèmes de réplication de machine virtuelle Azure vers Azure

Cet article décrit les problèmes courants dans Azure Site Recovery lors de la réplication et de la récupération de machines virtuelles Azure d’une région vers une autre, et explique comment les résoudre. Pour plus d’informations sur les configurations prises en charge, consultez la [matrice de prise en charge pour la réplication des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problèmes liés aux quotas de ressources Azure (code d’erreur 150097)
Votre abonnement doit être activé pour créer des machines virtuelles Azure dans la région cible que vous prévoyez d’utiliser comme région de récupération d’urgence. De plus, votre abonnement doit avoir un quota suffisant activé pour créer des machines virtuelles de taille spécifique. Par défaut, Site Recovery sélectionne pour la machine virtuelle cible la même taille que la machine virtuelle source. Si la taille correspondante n’est pas disponible, la taille la plus proche possible est choisie automatiquement. S’il n’existe pas de taille correspondante qui prend en charge la configuration de la machine virtuelle source, ce message d’erreur s’affiche :

**Code d’erreur** | **Causes possibles** | **Recommandation**
--- | --- | ---
150097<br></br>**Message** : Impossible d’activer la réplication pour la machine virtuelle nom_machine_virtuelle. | - Votre ID d’abonnement n’est peut-être pas activé pour créer des machines virtuelles dans la région cible.</br></br>- Votre ID d’abonnement n’est peut-être pas activé ou ne dispose pas d’un quota suffisant pour créer des tailles de machine virtuelle spécifiques à l’emplacement dans la région cible.</br></br>- Une taille de machine virtuelle cible appropriée qui correspond au nombre de cartes réseau de la machine virtuelle source (2) est introuvable pour l’ID d’abonnement dans la région cible.| Contactez le [support de facturation Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour activer la création de machines virtuelles pour les tailles de machine virtuelle requises à l’emplacement cible pour votre abonnement. Ensuite, réessayez l’opération.

### <a name="fix-the-problem"></a>Résoudre le problème
Vous pouvez contacter le [support de facturation Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour activer votre abonnement et créer des machines virtuelles de tailles requises à l’emplacement cible.

Si l’emplacement cible a une contrainte de capacité, désactivez la réplication et activez-la à un autre emplacement où votre abonnement a un quota suffisant pour créer des machines virtuelles des tailles requises.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificats racines approuvés (code d’erreur 151066)

Si tous les certificats racines approuvés les plus récents ne sont pas présents sur la machine virtuelle, le travail d’activation de la réplication risque d’échouer. Sans les certificats, l’authentification et l’autorisation des appels du service Site Recovery à partir de la machine virtuelle échouent. Le message d’erreur signalant l’échec du travail d’activation de la réplication Site Recovery s’affiche :

**Code d’erreur** | **Cause possible** | **Recommandations**
--- | --- | ---
151066<br></br>**Message** : Échec de la configuration de Site Recovery. | Les certificats racines approuvés nécessaires pour l’autorisation et l’authentification ne sont pas présents sur la machine. | - Pour une machine virtuelle exécutant le système d’exploitation Windows, vérifiez que les certificats racines approuvés sont présents sur la machine. Pour plus d’informations, consultez [Configurer des racines de confiance et des certificats non autorisés](https://technet.microsoft.com/library/dn265983.aspx).<br></br>- Pour une machine virtuelle exécutant le système d’exploitation Linux, suivez les instructions relatives aux certificats racines approuvés publiées par le distributeur de la version du système d’exploitation Linux.

### <a name="fix-the-problem"></a>Résoudre le problème
**Windows**

Installez toutes les mises à jour de Windows les plus récentes sur la machine virtuelle pour que tous les certificats racines approuvés soient présents sur la machine. Si vous êtes dans un environnement déconnecté, suivez le processus de mise à jour de Windows standard en vigueur dans votre organisation pour obtenir les certificats. Si les certificats nécessaires ne sont pas présents sur la machine virtuelle, les appels au service Site Recovery échouent pour des raisons de sécurité.

Suivez le processus classique de gestion des mises à jour de Windows ou de gestion des mises à jour de certificats en vigueur dans votre organisation pour obtenir tous les certificats racines les plus récents et la liste de révocation de certificats mise à jour sur les machines virtuelles.

Pour vérifier que le problème est résolu, accédez à login.microsoftonline.com à partir d’un navigateur sur votre machine virtuelle.

**Linux**

Suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.

SuSE Linux utilisant des liens symboliques pour tenir à jour une liste de certificats, vous devez effectuer les étapes suivantes :

1.  Connectez-vous en tant qu’utilisateur racine.

2.  Exécutez cette commande pour modifier le répertoire.

      ``# cd /etc/ssl/certs``

3. Vérifiez si le certificat d’autorité de certification racine Symantec est présent.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Si le certificat d’autorité de certification racine Symantec est introuvable, exécutez la commande suivante pour télécharger le fichier. Vérifiez les erreurs et suivez l’action recommandée pour les défaillances du réseau.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Vérifiez si le certificat d’autorité de certification racine Baltimore est présent.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Si le certificat d’autorité de certification racine Baltimore est introuvable, téléchargez le certificat.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Vérifiez si le certificat DigiCert_Global_Root_CA est présent.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Si le certificat DigiCert_Global_Root_CA est introuvable, exécutez les commandes suivantes pour télécharger le certificat.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Exécutez le script rehash pour mettre à jour les hachages de sujet des certificats qui viennent d’être téléchargés.

    ``# c_rehash``

10. Vérifiez si les hachages de sujet en tant que liens symboliques sont créés pour les certificats.

    - Commande

      ``# ls -l | grep Baltimore``

    - Sortie

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Commande

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Sortie

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Commande

      ``# ls -l | grep DigiCert_Global_Root``

    - Sortie

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

11. Créez une copie du fichier VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem et donnez-lui le nom b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Créez une copie du fichier Baltimore_CyberTrust_Root.pem et donnez-lui le nom 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Créez une copie du fichier DigiCert_Global_Root_CA.pem et donnez-lui le nom 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Vérifiez si les fichiers sont présents.  

    - Commande

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Sortie

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connectivité sortante pour les plages d’adresses IP ou les URL Site Recovery (code d’erreur 151037 ou 151072)

Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez recevoir l’un de ces messages d’erreur :

**Codes d’erreur** | **Causes possibles** | **Recommandations**
--- | --- | ---
151037<br></br>**Message** : Échec de l’inscription de la machine virtuelle Azure à Site Recovery. | - Vous utilisez un groupe de sécurité réseau pour contrôler l’accès sortant sur la machine virtuelle et les plages d’adresses IP requises ne figurent pas dans la liste verte pour l’accès sortant.</br></br>- Vous utilisez des outils de pare-feu tiers et les URL/plages d’adresses IP requises ne figurent pas dans la liste verte.</br>| - Si vous utilisez un proxy de pare-feu pour contrôler la connectivité réseau sortante sur la machine virtuelle, vérifiez que les URL ou les plages d’adresses IP du centre de données prérequises figurent dans la liste verte. Pour plus d’informations, consultez ces [instructions relatives aux proxy de pare-feu](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité réseau sortante sur la machine virtuelle, vérifiez que les plages d’adresses IP du centre de données prérequises figurent dans la liste verte. Pour plus d’informations, consultez ces [instructions relatives aux groupes de sécurité réseau](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Message** : Échec de la configuration de Site Recovery. | Impossible d’établir la connexion aux points de terminaison de service Site Recovery. | - Si vous utilisez un proxy de pare-feu pour contrôler la connectivité réseau sortante sur la machine virtuelle, vérifiez que les URL ou les plages d’adresses IP du centre de données prérequises figurent dans la liste verte. Pour plus d’informations, consultez ces [instructions relatives aux proxy de pare-feu](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité réseau sortante sur la machine virtuelle, vérifiez que les plages d’adresses IP du centre de données prérequises figurent dans la liste verte. Pour plus d’informations, consultez ces [instructions relatives aux groupes de sécurité réseau](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Résoudre le problème
Pour mettre les [URL requises](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou les [plages d’adresses IP requises](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) dans la liste verte, suivez les étapes fournies dans ce [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disque introuvable sur la machine (code d’erreur 150039)

Un nouveau disque attaché à la machine virtuelle doit être initialisé.

**Code d’erreur** | **Causes possibles** | **Recommandations**
--- | --- | ---
150039<br></br>**Message** : Le disque de données Azure (nom_disque) (URI_disque) avec le numéro d’unité logique (LUN) (valeur_LUN) n’a pas été mappé au disque correspondant signalé à partir de la machine virtuelle ayant la même valeur LUN. | - Un nouveau disque de données a été attaché à la machine virtuelle, mais il n’a pas été initialisé.</br></br>- Le disque de données à l’intérieur de la machine virtuelle ne signale pas correctement la valeur de numéro d’unité logique (LUN) à laquelle le disque a été attaché à la machine virtuelle.| Vérifiez que les disques de données sont initialisés, puis réessayez l’opération.</br></br>Pour Windows : [Attacher et initialiser un nouveau disque](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pour Linux : [Initialiser un nouveau disque de données sous Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Résoudre le problème
Vérifiez que les disques de données ont été initialisés, puis réessayez l’opération :

- Pour Windows : [Attacher et initialiser un nouveau disque](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pour Linux : [Ajouter un nouveau disque de données dans Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Si le problème persiste, contactez le support technique.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Impossible de sélectionner la machine virtuelle Azure dans « Activer la réplication »

Si vous ne voyez pas la machine virtuelle que vous souhaitez activer pour la réplication, cela peut-être dû à une configuration de récupération de site obsolète conservée sur la machine virtuelle Azure. Une configuration obsolète peut être conservée sur une machine virtuelle Azure dans les cas suivants :

- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.
- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le groupe de ressources contenant le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.

### <a name="fix-the-problem"></a>Résoudre le problème

Vous pouvez utiliser ce [script de suppression de configuration ASR obsolète](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) pour supprimer la configuration Site Recovery obsolète sur la machine virtuelle Azure. Vous devriez être en mesure de voir la machine virtuelle après la suppression de la configuration obsolète.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>L’état de provisionnement de la machine virtuelle n’est pas valide (code d’erreur 150019)

Pour activer la réplication sur la machine virtuelle, l’état de provisionnement doit être **Réussite**. Vous pouvez vérifier l’état de la machine virtuelle en suivant les étapes ci-dessous.

1.  Sélectionnez **l’Explorateur de ressources** sous **Tous les services** dans le portail Azure.
2.  Développez la liste **Abonnements** et sélectionnez votre abonnement.
3.  Développez la liste **Groupes de ressources** et sélectionnez le groupe de ressources de la machine virtuelle.
4.  Développez la liste **Ressources** et sélectionnez votre machine virtuelle
5.  Vérifiez le champ **provisioningState** dans la vue Instance à droite.

### <a name="fix-the-problem"></a>Résoudre le problème

- Si **provisioningState** a la valeur **Échec**, contactez le support avec les détails pour résoudre les problèmes.
- Si **provisioningState** a la valeur **Mise à jour**, une autre extension est peut-être en cours de déploiement. Vérifiez si des opérations sont en cours sur la machine virtuelle, attendez qu’elles se terminent et réessayez la tâche Site Recovery **Activer la réplication** qui a échoué.

## <a name="next-steps"></a>Étapes suivantes
[Répliquer des machines virtuelles Azure](site-recovery-replicate-azure-to-azure.md)
