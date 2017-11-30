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
|maxOutstandingRequests|-1|Nombre maximal de requêtes en attente qui ont lieu à un moment donné (-1 signifie illimité). La limite inclut les requêtes qui sont en file d’attente, mais dont l’exécution n’a pas démarré, ainsi que les exécutions en cours. Toutes les requêtes entrantes au-delà de cette limite sont rejetées avec une réponse 429 « Trop occupé ». Les appelants peuvent utiliser cette réponse pour avoir recours à des stratégies de nouvelles tentatives à durée définie. Ce paramètre contrôle uniquement la mise en file d’attente qui se produit dans le chemin d’accès d’exécution de l’hôte de travail. Les autres files d’attente, telles que la file d’attente des requêtes ASP.NET, ne sont pas concernées par ce paramètre. |
|maxConcurrentRequests|-1|Nombre maximal de fonctions HTTP exécutées en parallèle (-1 signifie illimité). Par exemple, vous pouvez définir une limite si vos fonctions HTTP utilisent trop de ressources système lorsque l’accès concurrentiel est élevé. Si vos fonctions effectuent des requêtes sortantes vers un service tiers, il se peut que ces appels doivent être à fréquence limitée.|
|dynamicThrottlesEnabled|false|Provoque la vérification périodique des compteurs de performances système par le pipeline de traitement des requêtes. Les compteurs incluent les connexions, les threads, les processus, la mémoire et le processeur. Si des compteurs dépassent un seuil prédéfini (80 %), les requêtes sont rejetées avec une réponse 429 « Trop occupé » jusqu’à ce que le ou les compteurs retrouvent des niveaux normaux.|
