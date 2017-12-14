Les tailles de machine virtuelle à stockage optimisé offrent un débit et des E/S de disque élevés, et sont idéales pour les bases de données Big Data, SQL et NoSQL. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

La série Ls offre jusqu’à 32 processeurs virtuels, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Cette série propose les mêmes performances de processeur que celles de la série G/GS, associées à 8 Gio de mémoire par processeur virtuel.  

## <a name="ls-series"></a>Série Ls

ACU : 180-240
 
| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max : E/S par seconde / Mbits/s | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbits/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20 000 / 200   | 10 000 / 250        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1 388 | 32   | 40 000 / 400   | 20 000 / 500       | 4 / 8 000  | 
| Standard_L16s  | 16   | 128  | 2 807 | 64   | 80 000 / 800   | 40 000 / 1 000       | 8 / 6 000 - 16 000 &#8224; | 
| Standard_L32s* | 32   | 256  | 5,630 | 64   | 160 000 / 1 600   | 80 000 / 2 000     | 8 / 20 000 | 
 

Le débit de disque maximal possible avec des machines virtuelles de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/virtual-machines/windows/premium-storage.md). 

*L’instance est isolée sur un matériel dédié à un client unique.

