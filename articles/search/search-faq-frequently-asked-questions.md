---
title: "Questions fréquentes (FAQ) sur la Recherche Azure | Microsoft Docs"
description: "Obtenez la réponse aux questions les plus fréquentes concernant le service Recherche Microsoft Azure."
services: search
author: HeidiSteen
manager: jhubbard
ms.service: search
ms.technology: search
ms.topic: article
ms.date: 08/03/2017
ms.author: heidist
ms.openlocfilehash: f61fe2930bc70e800e5d79773e0de6827621e845
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Recherche Azure - Questions fréquentes (FAQ)

 Trouvez la réponse aux questions les plus fréquemment posées sur les concepts, le code et les scénarios liés à la Recherche Azure.

## <a name="platform"></a>Plateforme

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>En quoi la Recherche Azure est-elle différente de la recherche en texte intégral de mon système de gestion de base de données (SGBD) ?

La Recherche Azure comprend les fonctionnalités suivantes : prise en charge de plusieurs sources de données, [analyse linguistique de nombreuses langues](https://docs.microsoft.com/rest/api/searchservice/language-support), [analyse personnalisée des entrées de données intéressantes et inhabituelles](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), contrôles de classement des recherches par le biais de [profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), tampon clavier, mise en surbrillance des résultats et navigation par facettes. Elle comprend également d’autres fonctionnalités, telles que les synonymes et une syntaxe de requête riche, toutefois ces fonctionnalités ne lui sont pas propres.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Quelle est la différence entre la Recherche Azure et Elasticsearch ?

Lorsqu’ils comparent les technologies de recherche, les clients demandent souvent des précisions sur les différences entre la Recherche Azure et Elasticsearch. Lorsque les clients choisissent la Recherche Azure plutôt qu’Elasticsearch pour leurs projets d’applications de recherche, c’est généralement parce qu’elle facilite l’une de leurs tâches principales ou parce qu’ils ont besoin de l’intégrer à d’autres technologies Microsoft :

+ La Recherche Azure est un service cloud entièrement géré, avec 99,9 % de contrats de niveau de service (SLA) lorsqu’elle reçoit suffisamment de redondance (2 réplicas pour l’accès en lecture, 3 réplicas pour l’accès en lecture-écriture).
+ Les [processeurs de langage naturel](https://docs.microsoft.com/rest/api/searchservice/language-support) Microsoft offrent une analyse linguistique à la pointe de la technologie.  
+ Les [indexeurs de la Recherche Azure](search-indexer-overview.md) peuvent analyser diverses sources de données Azure en vue d’une indexation initiale et incrémentielle.
+ Si vous avez besoin d’une réponse rapide aux fluctuations de volume des requêtes ou de l’indexation, vous pouvez utiliser les [curseurs](search-manage.md#scale-up-or-down) du portail Azure, ou exécuter un [script PowerShell](search-manage-powershell.md), en ignorant la gestion des partitions.  
+ Les [fonctionnalités de calcul de score et de paramétrage](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) permettent d’améliorer le classement des recherches, bien au-delà de ce que le moteur de recherche seul peut fournir.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Puis-je suspendre le service Recherche Azure et arrêter la facturation ?

Vous ne pouvez pas suspendre le service. Lorsque le service est créé, les ressources de calcul et de stockage sont allouées pour votre utilisation exclusive. Il n’est pas possible de libérer des ressources à la demande.

## <a name="indexing-operations"></a>Opérations d’indexation

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Est-il préférable d’utiliser des index de sauvegarde et de restauration (ou téléchargement et déplacement) ou des instantanés d’index ?

Même si vous pouvez [obtenir une définition d’index](https://docs.microsoft.com/rest/api/searchservice/get-index) à tout moment, il n’existe pas de fonctionnalité d’extraction d’index, de création de capture instantanée ou de sauvegarde-restauration qui permettent de télécharger un index cloud *rempli* sur un système local, ou de le déplacer vers un autre service Recherche Azure.

Les index sont générés et remplis à partir du code que vous écrivez, et s’exécutent uniquement dans le cloud, dans la Recherche Azure. En règle générale, les clients qui souhaitent déplacer un index vers un autre service modifient leur code de manière à utiliser un nouveau point de terminaison, puis réexécutent l’indexation. Si vous voulez qu’une fonctionnalité vous permette de créer une capture instantanée ou une sauvegarde d’index, votez sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Puis-je effectuer une indexation à partir de réplicas de base de données SQL ? (s’applique aux [indexeurs Azure SQL Database](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Il n’existe aucune restriction quant à l’utilisation d’un réplica principal ou secondaire comme source de données lorsque vous créez un index à partir de zéro. Toutefois, pour que l’index soit actualisé avec des mises à jour incrémentielles (basées sur les enregistrements modifiés), le réplica principal est nécessaire. Cela vient du fait que SQL Database assure uniquement le suivi des modifications sur les réplicas principaux. Si vous essayez d’utiliser des réplicas secondaires pour une charge de travail d’actualisation d’index, il n’est pas garanti que vous obteniez toutes les données.

## <a name="search-operations"></a>Opérations de recherche

### <a name="can-i-search-across-multiple-indexes"></a>Puis-je effectuer une recherche sur plusieurs index ?

Non, cette opération n’est pas prise en charge. La recherche est toujours limitée à un seul index.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Puis-je restreindre l’accès au corpus de recherche en fonction de l’identité de l’utilisateur ?

Vous pouvez implémenter des [filtres de sécurité](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) avec le filtre `search.in()`. Le filtre s’adapte parfaitement aux [services de gestion d’identité comme Azure Active Directory(AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) afin de réduire les résultats de recherche en fonction de l’appartenance à un groupe d’utilisateurs défini.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Pourquoi n’ai-je aucun résultat pour des termes dont je sais qu’ils sont valides ?

Dans la plupart des cas, l’utilisateur ne sait pas que chaque type de requête prend en charge des comportements de recherche et des niveaux d’analyse linguistique différents. La recherche en texte intégral, qui représente la principale charge de travail, comprend une phase d’analyse linguistique qui ne conserve que la racine des termes entrés. Cet aspect de l’analyse de requête permet d’élargir le nombre de correspondances possibles, car le terme peut ainsi correspondre à un plus grand nombre de variantes.

Toutefois, les requêtes de caractère générique, les requêtes partielles et les requêtes d’expression régulière ne sont pas analysées comme les requêtes de terme ou d’expression normales et peuvent entraîner un rappel médiocre si la requête ne correspond pas à la forme analysée du mot dans l’index de recherche. Pour plus d’informations sur l’analyse des requêtes et l’analyse lexicale, consultez [l’architecture de requêtes](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Mes recherches de caractère générique sont lentes.

La plupart des requêtes de recherche de caractère générique, comme les requêtes de préfixe, partielle et d’expression régulière, sont réécrites en interne avec des termes correspondants dans l’index de recherche. Ce traitement supplémentaire d’analyse de l’index de recherche augmente la latence. Par ailleurs, les requêtes de recherche large, comme `a*`, qui sont susceptibles d’être réécrites avec de nombreux termes, peuvent être très lentes. Pour des recherches performantes avec caractère générique, définissez un [analyseur personnalisé](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Pourquoi le score du classement de recherche est-il systématiquement égal à 1.0 pour tous les résultats ?

Par défaut, les résultats de la recherche sont notés en fonction des [propriétés statistiques des termes correspondants](search-lucene-query-architecture.md#stage-4-scoring), et sont classés du score le plus haut vers le score le plus bas. Cependant, certains types de requête (caractère générique, préfixe, expression régulière) contribuent toujours à un score constant dans le score général du document. Ce comportement est normal. La Recherche Azure impose un score constant pour permettre aux correspondances trouvées par le biais de l’extension de requête d’être incluses dans les résultats, sans affecter le classement.

Par exemple, supposons l’entrée « tour* » dans une recherche par caractères génériques, qui retourne les résultats « tours », « tourettes » et « tourmaline ». Étant donné la nature de ces résultats, il est impossible de déduire raisonnablement quels termes sont plus utiles que d’autres. Pour cette raison, nous ignorons les fréquences de terme lors de la notation des résultats dans les requêtes avec caractère générique, préfixe et expression régulière. Les résultats de recherche basés sur une entrée partielle reçoivent un score constant afin d’éviter que des résultats inattendus ne soient retournés.

## <a name="design-patterns"></a>Modèles de conception

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Quelle est la meilleure méthode pour implémenter une recherche localisée ?

La plupart des clients choisissent des champs dédiés plutôt qu’une collection lorsque plusieurs paramètres régionaux (langues) doivent être pris en charge dans un même index. Les champs spécifiques aux paramètres régionaux permettent d’attribuer un analyseur adapté. Par exemple, vous pouvez affecter l’analyseur de français Microsoft à un champ contenant des chaînes en langue française. Cela simplifie également le filtrage. Si vous savez qu’une requête est exécutée sur une page fr-fr, vous pouvez limiter les résultats de la recherche à ce champ. Sinon, vous pouvez aussi créer un [profil de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) afin de donner au champ un poids plus relatif. Le service Recherche Azure prend en charge plus de [50 analyseurs de langue](https://docs.microsoft.com/azure/search/search-language-support) sélectionnables.

## <a name="next-steps"></a>Étapes suivantes

Votre question concerne-t-elle une fonctionnalité manquante ? Demandez cette fonctionnalité sur le [site web UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Voir aussi

 [Stack Overflow : Recherche Azure](https://stackoverflow.com/questions/tagged/azure-search)   
 [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)  
 [Présentation de Recherche Azure](search-what-is-azure-search.md)
