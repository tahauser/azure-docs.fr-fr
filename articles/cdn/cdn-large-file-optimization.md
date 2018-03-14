---
title: "Optimisation via Azure CDN de téléchargement de fichiers volumineux"
description: "Optimisation des téléchargements de fichiers volumineux expliquée en profondeur"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 6e6266fdaaac6a1a1a5d3a5595c10f79fd9f01a7
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="large-file-download-optimization-via-azure-cdn"></a>Optimisation via Azure CDN de téléchargement de fichiers volumineux

Les tailles des fichiers distribués sur Internet ne cessent de croître en raison du perfectionnement des fonctionnalités, de l’amélioration des graphismes et de l’enrichissement des contenus multimédias. Cette croissance résulte de divers facteurs : pénétration de la large bande, augmentation de la capacité des périphériques de stockage bon marché, prolifération des vidéos haute définition et objets connectés à Internet (IoT). Un mécanisme de distribution rapide et efficace des fichiers volumineux est essentiel pour garantir au consommateur une expérience fluide et agréable.

La distribution de fichiers volumineux présente plusieurs difficultés. Premièrement, le temps moyen de téléchargement d’un fichier volumineux peut être important parce que certaines applications peuvent ne pas télécharger pas toutes les données de manière séquentielle. Certaines applications peuvent télécharger la dernière partie d’un fichier avant la première. Lorsque la demande ne porte que sur une petite partie d’un fichier ou quand un utilisateur suspend un téléchargement, le téléchargement peut échouer. Le téléchargement peut également être retardé jusqu'au moment où le réseau de distribution de contenu (CDN) a récupéré la totalité du fichier à partir du serveur d’origine. 

Deuxièmement, la latence entre l’ordinateur d’un utilisateur et le fichier détermine la vitesse à laquelle le contenu peut s’afficher. Par ailleurs, des problèmes de surcharge et de capacité du réseau affectent également le débit. Les distances plus importantes entre les serveurs et les utilisateurs créent des possibilités supplémentaires de perte de paquets, ce qui affecte la qualité. La réduction de la qualité due à débit limité et à une perte accrue de paquets peut augmenter le temps d’attente pour que le téléchargement d’un fichier s’achève. 

Troisièmement, de nombreux fichiers volumineux ne sont pas distribués dans leur intégralité. Les utilisateurs peuvent annuler un téléchargement en plein milieu ou ne regarder que les premières minutes d’une longue vidéo MP4. Par conséquent, les sociétés de distribution de logiciels et de contenus multimédias souhaitent distribuer uniquement la portion demandée d’un fichier. Une distribution efficace des portions demandées réduit le trafic sortant du serveur d’origine. Une distribution efficace réduit également la mémoire et la pression d’E/S sur le serveur d’origine. 

Le réseau Azure Content Delivery Network d’Akamai offre désormais une fonctionnalité qui distribue des fichiers volumineux efficacement à des utilisateurs dans le monde entier à grande échelle. La fonctionnalité réduit les latences, car elle réduit la charge sur les serveurs d’origine. Cette fonctionnalité est disponible au niveau de tarification Standard d’Akamai.

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurer un Point de terminaison CDN pour optimiser la distribution de fichiers volumineux

Vous pouvez configurer votre point de terminaison CDN pour optimiser la distribution de fichiers volumineux via le portail Azure. Pour ce faire, vous pouvez également utiliser nos API REST ou tout SDK client. Les étapes suivantes montrent le processus via le portail Azure.

1. Pour ajouter un nouveau point de terminaison, dans la page **Profil CDN**, sélectionnez **Point de terminaison**.

    ![Nouveau point de terminaison](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. Dans la liste déroulante **Optimisé pour**, sélectionnez **Téléchargement de fichiers volumineux**.

    ![Optimisation des fichiers volumineux sélectionnée](./media/cdn-large-file-optimization/02_Creating.png)


Une fois le point de terminaison CDN créé, il applique les optimisations de fichiers volumineux pour tous les fichiers correspondant à certains critères. La section suivante décrit ce processus.

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>Optimiser pour la distribution de fichiers volumineux avec le réseau Azure Content Delivery Network d’Akamai

La fonctionnalité de type d’optimisation des fichiers volumineux active les optimisations et les configurations de réseaux pour distribuer les fichiers volumineux plus rapidement et de façon plus réactive. La distribution web générale avec Akamai met en cache uniquement les fichiers d’une taille inférieure à 1,8 Go et peut « tunneler » (sans mise en cache) des fichiers jusqu'à 150 Go. L’optimisation des fichiers volumineux met en cache les fichiers jusqu'à 150 Go.

L’optimisation des fichiers volumineux est efficace lorsque certaines conditions sont réunies. Ces conditions incluent la manière dont le serveur d’origine opère, ainsi que les tailles et types de fichiers demandés. Avant de passer aux détails sur ces sujets, vous devez comprendre fonctionne l’optimisation. 

### <a name="object-chunking"></a>Segmentation d’objet 

Le réseau Azure Content Delivery Network d’Akamai utilise une technique appelée segmentation des objets. Quand un fichier volumineux est demandé, le CDN récupère des éléments plus petits du fichier à partir de l’origine. Lorsque le serveur de périphérie/POP du CDN reçoit une demande de fichier complet ou de plage d’octets de fichier, il vérifie si le type de fichier est pris en charge pour cette optimisation. Il vérifie également si le type de fichier satisfait les exigences de taille de fichier. Si la taille du fichier est supérieure à 10 Mo, le serveur de périphérie du CDN demande à recevoir le fichier de l’origine en blocs de 2 Mo. 

Quand un segment arrive à la périphérie du CDN, il est mis en cache et immédiatement servi à l’utilisateur. Le CDN prérécupère alors le bloc suivant en parallèle. Cette prérécupération garantit que le contenu a un bloc d’avance sur l’utilisateur, ce qui a réduit la latence. Ce processus se poursuit jusqu'à ce que le fichier entier soit téléchargé (si nécessaire), que toutes les plages d’octets soient disponibles (si nécessaire), ou que le client mette fin à la connexion. 

Pour plus d’informations sur la demande de plage d’octets, voir [RFC 7233](https://tools.ietf.org/html/rfc7233).

Le CDN met en cache les blocs au fur et à mesure de leur réception. Le fichier entier ne doit être mis en cache sur le cache du CDN. Les demandes suivantes du fichier ou des plages d’octets sont servies à partir du cache du CDN. Si tous les blocs sont mis en cache sur le CDN, une prérécupération est utilisée pour demander des blocs de l’origine. Cette optimisation s’appuie sur la capacité du serveur d’origine à prendre en charge des demandes de plages d’octets. _Si le serveur d’origine ne prend pas en charge les demandes de plages d’octets, cette optimisation n’est pas effective._ 

### <a name="caching"></a>Mise en cache
L’optimisation des fichiers volumineux utilise des temps d’expiration de mise en cache par défaut différents de ceux d’une livraison web générale. Il établit la distinction entre la mise en cache positive et négative basée sur les codes de réponse HTTP. Si le serveur d’origine spécifie un délai d’expiration via un en-tête Cache-control ou Expires dans la réponse, le CDN respecte cette valeur. Lorsque le serveur d’origine ne spécifie rien de tel et que le fichier remplit les conditions de type et de taille de fichier pour ce type d’optimisation, le CDN utilise les valeurs par défaut pour l’optimisation des fichiers volumineux. Autrement, le CDN utilise les paramètres par défaut pour une livraison web générale.


|    | Livraison web générale | Optimisation des fichiers volumineux 
--- | --- | --- 
Mise en cache : positive <br> HTTP 200, 203, 300, <br> 301, 302 et 410 | 7 jours |1 jour  
Mise en cache : négative <br> HTTP 204, 305, 404, <br> et 405 | Aucun | 1 seconde 

### <a name="deal-with-origin-failure"></a>Traitement des défaillances de l’origine

La longueur du délai d'expiration de lecture de l’origine augmente de deux secondes pour une livraison web générale, à deux minutes pour le type d’optimisation des fichiers volumineux. Cette augmentation s’explique pour les fichiers plus volumineux afin d’éviter une expiration prématurée de connexion.

Quand une connexion arrive à expiration, le CDN retente de l’établir un certain nombre de fois avant d’envoyer une erreur « 504 - Dépassement du délai de la passerelle » au client. 

### <a name="conditions-for-large-file-optimization"></a>Conditions d’optimisation des fichiers volumineux

Le tableau suivant répertorie l’ensemble de critères à satisfaire pour l’optimisation des fichiers volumineux :

Condition | Valeurs 
--- | --- 
Types de fichiers pris en charge | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Taille minimale du fichier | 10 Mo 
Taille maximale du fichier | 150 Go 
Caractéristiques du serveur d’origine | Doit prendre en charge des demandes de plages d’octets 

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>Optimiser pour la distribution de fichiers volumineux avec le réseau Azure Content Delivery Network de Verizon

Le réseau Azure Content Delivery Network de Verizon distribue des fichiers volumineux sans limite de taille. Des fonctionnalités supplémentaires sont activées par défaut pour accélérer la distribution de fichiers volumineux.

### <a name="complete-cache-fill"></a>Remplissage du cache complet

La fonctionnalité de remplissage du cache complet par défaut permet au CDN d’extraire un fichier dans le cache en cas d’abandon ou de perte d’une demande initiale. 

Le remplissage du cache complet est très utile pour les ressources volumineuses. En règle générale, les utilisateurs ne téléchargent pas celles-ci du début à la fin. Ils utilisent un téléchargement progressif. Le comportement par défaut consiste à forcer le serveur de périphérie à lancer une récupération en arrière-plan de la ressource à partir du serveur d’origine. Ensuite, la ressource se trouve dans le cache local du serveur de périphérie. Une fois l’objet complet dans le cache, le serveur de périphérie satisfait les demandes de plages d’octets adressées au CDN pour l’objet mis en cache.

Le comportement par défaut peut être désactivé via le moteur de règles dans le niveau Premium de Verizon.

### <a name="peer-cache-fill-hot-filing"></a>Remplissage à chaud du cache d'homologue

La fonctionnalité de remplissage à chaud du cache d'homologue par défaut utilise un algorithme propriétaire sophistiqué. Elle utilise des serveurs de mise en cache de périphérie supplémentaires en fonction de la bande passante et des métriques de demandes agrégées pour répondre aux demandes de clients pour des objets très populaires volumineux. Cette fonctionnalité empêche une situation dans laquelle de grands nombres de demandes supplémentaires seraient envoyées au serveur d’origine d’un client. 

### <a name="conditions-for-large-file-optimization"></a>Conditions d’optimisation des fichiers volumineux

Les fonctionnalités d’optimisation pour Verizon sont activées par défaut. Il n’existe aucune limite à la taille de fichier maximale. 

## <a name="additional-considerations"></a>Considérations supplémentaires

Prenez en considération les aspects supplémentaires suivants pour ce type d’optimisation.
 
### <a name="azure-content-delivery-network-from-akamai"></a>Réseau Azure Content Delivery Network d’Akamai

- Le processus de segmentation génère des demandes supplémentaires au serveur d’origine. Toutefois, le volume total de données distribuées à partir de l’origine est beaucoup plus petit. La segmentation entraîne de meilleures caractéristiques de mise en cache au niveau du CDN.

- La pression sur les E/S et la mémoire est réduite au niveau de l’origine, car des éléments plus petits du fichier sont distribués.

- Pour les blocs mis en cache au niveau du CDN, il n’y a pas de demande supplémentaire adressée à l’origine jusqu'à ce que le contenu expire ou soit supprimé du cache.

- Les utilisateurs peuvent adresser au CDN des demandes de plages qui sont traitées comme tout fichier normal. L’optimisation s’applique uniquement si le type de fichier est valide et si la taille de la plage d’octets est comprise entre 10 Mo et 150 Go. Si la taille moyenne des fichiers demandés est inférieure à 10 Mo, vous pouvez utiliser une livraison web générale à la place.

### <a name="azure-content-delivery-network-from-verizon"></a>Réseau Azure Content Delivery Network de Verizon

Le type d’optimisation de livraison web générale permet de distribuer des fichiers volumineux.
