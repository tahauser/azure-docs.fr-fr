---
title: Préparer des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs
description: Explique comment préparer le certificat pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: c195cc0bacd9eea7e75fa35cd155845f03dd21cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Préparer des certificats PKI Azure Stack pour le déploiement
Les fichiers de certificat [obtenus auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) doivent être importés et exportés avec des propriétés correspondant aux exigences de certificat d’Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Préparer les certificats pour le déploiement
Suivez ces étapes pour préparer et valider les certificats PKI Azure Stack : 

1.  Copiez les versions du certificat d’origine [obtenues auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) dans un répertoire de l’ordinateur hôte de déploiement. 
  > [!WARNING]
  > Ne copiez pas les fichiers qui ont déjà été importés, exportés ou modifiés d’une façon ou d’une autre à partir des fichiers fournis directement par l’autorité de certification.

2.  Importez les certificats dans le magasin de certificats de l’ordinateur Local :

    a.  Cliquez avec le bouton droit sur le certificat et sélectionnez **Installer PFX**.

    b.  Dans **l’Assistant Importation de certificat**, sélectionnez **Ordinateur local** en tant qu’emplacement d’importation. Sélectionnez **Suivant**.

    ![Emplacement d’importation sur l’ordinateur local](.\media\prepare-pki-certs\1.png)

    c.  Sélectionnez **Suivant** sur la page **Choisir le fichier à importer**.

    d.  Sur la page **Protection de la clé privée**, entrez le mot de passe correspondant à vos fichiers de certificat, puis activez l’option **Marquer cette clé comme exportable. Cela vous permet de sauvegarder ou transporter vos clés ultérieurement**. Sélectionnez **Suivant**.

    ![Marquer la clé comme exportable](.\media\prepare-pki-certs\2.png)

    e.  Sélectionnez **Placer tous les certificats dans le magasin suivant**, puis sélectionnez **Approbation de l’entreprise** comme emplacement. Cliquez sur **OK** pour fermer la boîte de dialogue de sélection du magasin de certificats, puis cliquez sur **Suivant**.

    ![Configurer le magasin de certificats](.\media\prepare-pki-certs\3.png)

  f.    Cliquez sur **Terminer** pour terminer l’Assistant Importation de certificat.

  g.    Répétez le processus pour tous les certificats que vous fournissez pour votre déploiement.

3. Exportez le certificat au format de fichier PFX en respectant les exigences d’Azure Stack :

  a.    Ouvrez la console MMC du Gestionnaire de certificats et connectez-vous au magasin de certificats de l’ordinateur Local.

  b.    Accédez au répertoire **Approbation de l’entreprise**.

  c.    Sélectionnez l’un des certificats que vous avez importés à l’étape 2 ci-dessus.

  d.    Dans la barre des tâches de la console Gestionnaire de certificats, sélectionnez **Actions** > **Toutes les tâches** > **Exporter**.

  e.    Sélectionnez **Suivant**.

  f.    Sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

  g.    Dans la section Format de fichier d’exportation, sélectionnez **Exporter toutes les propriétés étendues**, puis cliquez sur **Suivant**.

  h.    Sélectionnez **Mot de passe** et indiquez un mot de passe pour les certificats. N’oubliez pas ce mot de passe car il sera utilisé comme paramètre de déploiement. Sélectionnez **Suivant**.

  i.    Choisissez un nom de fichier et l’emplacement du fichier pfx à exporter. Sélectionnez **Suivant**.

  j.    Sélectionnez **Terminer**.

  k.    Répétez ce processus pour tous les certificats que vous avez importés pour votre déploiement à l’étape 2 ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
[Validate PKI certificates](azure-stack-validate-pki-certs.md) (Valider des certificats d’infrastructure à clé publique)