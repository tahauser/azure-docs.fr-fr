```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier. Par défaut, le runtime Functions traite plusieurs messages simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `maxConcurrentCalls` sur 1. | 
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance MessageReceiver sous-jacente.| 
|autoRenewTimeout|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.| 
