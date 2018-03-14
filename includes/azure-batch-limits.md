| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Comptes Batch par région et par abonnement | 1 à 3 |50 |
| Cœurs dédiés par compte Batch | 10 à 100 | N/A<sup>1</sup> |
| Cœurs à priorité basse par compte Batch | 10 à 100 | N/A<sup>2</sup> |
| Travaux et planifications de travaux actifs<sup>3</sup> par compte Batch | 20 à 100 | 5000<sup>4</sup> |
| Pools par compte Batch | 20 à 100 | 2 500 |

> [!NOTE]
> Les limites par défaut varient selon le type d’abonnement que vous utilisez pour créer un compte Batch. Les quotas de cœurs affichés correspondent aux comptes Batch en mode service Batch. [Affichez les quotas dans votre compte Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> Vous pouvez augmenter le nombre de cœurs dédiés par compte Batch, mais le nombre maximal n’est pas spécifié. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

<sup>2</sup> Vous pouvez augmenter le nombre de cœurs à priorité basse par compte Batch, mais le nombre maximal n’est pas spécifié. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

<sup>3</sup> Les travaux et planifications de travaux terminés ne sont pas limités.

<sup>4</sup> Contactez le service clientèle Azure si vous voulez dépasser cette limite.
