```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|routePrefix|api|Préfixe d’itinéraire qui s’applique à tous les itinéraires. Utilisez une chaîne vide pour supprimer le préfixe par défaut. |
|maxOutstandingRequests|-1|Nombre maximal de requêtes en attente qui ont lieu à un moment donné. La limite inclut les requêtes qui sont en file d’attente, mais dont l’exécution n’a pas démarré, ainsi que les exécutions en cours. Toutes les requêtes entrantes au-delà de cette limite sont rejetées avec une réponse 429 « Trop occupé ». Ainsi, les appelants peuvent appliquer des stratégies temporelles de nouvelle tentative et vous êtes en mesure de contrôler les latences maximales de requêtes. Ce paramètre contrôle uniquement la mise en file d’attente qui se produit dans le chemin d’accès d’exécution de l’hôte de script. Les autres files d’attente, telles que la file d’attente des requêtes ASP.NET, sont toujours actives et ne sont pas concernées par ce paramètre. La valeur par défaut est unbounded.|
|maxConcurrentRequests|-1|Nombre maximal de fonctions HTTP exécutées en parallèle. Ce paramètre vous permet de contrôler la concurrence, et ainsi facilite la gestion de l’utilisation des ressources. Par exemple, vous pouvez disposer d’une fonction HTTP qui utilise de nombreuses ressources système (mémoire/processeur/sockets) et qui provoque par conséquent des situations où la concurrence est trop élevée. Vous pouvez également avoir une fonction qui effectue des requête vers un service tiers, et qui émet à ce titre des appels dont le débit doit être limité. Dans ces cas, il peut être pertinent d’appliquer une limitation. La valeur par défaut est unbounded.|
|dynamicThrottlesEnabled|false|Lorsqu’il est activé, ce paramètre provoque la vérification périodique des compteurs de performance système, comme les connexions/les threads/les processus/la mémoire/le processeur, etc., par le pipeline de traitement des requêtes. Si l’un de ces compteurs dépasse un seuil supérieur intégré (80 %), les requêtes sont rejetées avec une réponse 429 (trop de demandes) jusqu’à ce qu’un niveau normal soit retrouvé.|
