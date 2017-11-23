```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxBatchSize|64|Nombre d’événements maximal reçu par boucle de réception.|
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance EventProcessorHost sous-jacente.| 
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.| 
