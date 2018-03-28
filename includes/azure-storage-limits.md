| Ressource | Limite par défaut |
| --- | --- |
| Nombre de comptes de stockage par abonnement | 200<sup>1</sup> |
| Capacité maximale du compte de stockage | 500 Tio<sup>2</sup> |
| Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage | Aucune limite |
| Taux de requête maximal par compte de stockage | 20 000 requêtes par seconde<sup>2</sup> |
| Entrée max.<sup>3</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 20 Gbit/s pour LRS<sup>2</sup> |
| Sortie max.<sup>3</sup> par compte de stockage (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS/ZRS<sup>4</sup> est activé, 30 Gbit/s pour LRS<sup>2</sup> |
| Entrée max.<sup>3</sup> par compte de stockage (régions hors États-Unis) | 5 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 10 Gbit/s pour LRS<sup>2</sup> |
| Sortie max.<sup>3</sup> par compte de stockage (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS/ZRS<sup>4</sup> est activé, 15 Gbit/s pour LRS<sup>2</sup> |

<sup>1</sup>Inclut les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage, sollicitez le [Support Azure](https://azure.microsoft.com/support/faq/)pour obtenir une assistance. L’équipe Azure Storage examinera votre cas d’entreprise et pourra approuver jusqu’à 250 comptes de stockage. 

<sup>2</sup> Si vous avez besoin d’étendre les limites de votre compte de stockage, contactez le [support Azure](https://azure.microsoft.com/support/faq/). L’équipe du service Stockage Azure examine la requête et peut approuver une augmentation des limites au cas par cas. Les comptes à usage général et les comptes de stockage Blob prennent en charge l’augmentation de la capacité, les entrées/sorties et le taux de requête par requête. Pour connaître les nouvelles valeurs maximales des comptes de stockage Blob, consultez [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> Limité seulement par les limites d’entrées/sorties du compte. *Entrée* désigne toutes les données (demandes) envoyées à un compte de stockage. *Sortie* désigne toutes les données (réponses) reçues d'un compte de stockage.  

<sup>4</sup> Les options de redondance de Stockage Azure sont :
* **RA-GRS**: stockage géo-redondant avec accès en lecture. Si RA-GRS est activé, les cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
* **GRS** : stockage géo-redondant. 
* **ZRS**: stockage redondant dans une zone. Uniquement disponible pour les objets blob de blocs. 
* **LRS**: stockage localement redondant. 
