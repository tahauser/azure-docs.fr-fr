Les tailles de machine virtuelle à stockage optimisé offrent un débit et des E/S de disque élevés, et sont idéales pour les bases de données Big Data, SQL et NoSQL. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur les performances de réseau et de débit de stockage pour chaque taille de ce regroupement. 

La série Ls offre jusqu’à 32 processeurs virtuels, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Cette série propose les mêmes performances de processeur que celles de la série G/GS, associées à 8 Gio de mémoire par processeur virtuel.  

## <a name="ls-series"></a>Série Ls

ACU : 180-240
 
| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Nombre max de cartes réseau / Performance réseau attendue (Mbits/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5 000 / 125                               | 2 / 4 000       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA / NA (0)          | 10 000 / 250                              | 4 / 8 000  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA / NA (0)          | 20 000 / 500                              | 8 / 6 000 à 16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5,630 | 64             | NA / NA (0)          | 40 000 / 1 000                            | 8 / 20 000 | 
 

Le débit de disque maximal possible avec des machines virtuelles de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/virtual-machines/windows/premium-storage.md). 

*L’instance est isolée sur un matériel dédié à un client unique.

