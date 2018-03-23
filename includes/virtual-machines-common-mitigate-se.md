


**Dernière mise à jour du document** : 6 mars, 10h00 PST.

La divulgation récente d’une [nouvelle classe de vulnérabilités de processeur](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) appelées attaques par canal latéral de l’exécution spéculative a généré des questions de la part de clients recherchant plus d’explications.  

L’infrastructure qui exécute Azure et isole les différentes charges de travail des clients est protégée.  Cela signifie que d’autres clients utilisant Azure ne peuvent pas attaquer votre application avec ces vulnérabilités.

> [!NOTE] 
> Fin février 2018, Intel Corporation a publié un [Guide de révision du microcode](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) mis à jour sur l’état de ses versions de microcode, qui permettent d’améliorer la stabilité et d’atténuer les vulnérabilités récentes communiquées par [l’équipe Project Zero de Google](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Les solutions d’atténuation des risques Azure présentées le [3 janvier 2018](https://azure.microsoft.com/en-us/blog/securing-azure-customers-from-cpu-vulnerability/) ne sont pas affectées par la mise à jour du microcode d’Intel. Microsoft a déjà mis en place des mesures d’atténuation solides pour protéger les clients Azure d’autres locataires Azure.  
>
> Le microcode d’Intel cible la variante 2 de Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715)) pour vous protéger contre les attaques applicables uniquement à l’emplacement où vous exécutez des charges de travail partagées ou non approuvées dans vos machines virtuelles sur Azure. Nos ingénieurs testent la stabilité du microcode pour minimiser son impact sur les performances, avant de le mettre à la disposition des clients Azure.  Comme très peu de clients exécutent des charges de travail non approuvées dans leurs machines virtuelles, la plupart des clients n’ont pas besoin d’activer cette fonctionnalité une fois publiée. 
>
> Cette page sera mise à jour dès que d’autres informations seront disponibles.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Maintien à jour de vos systèmes d’exploitation

Alors qu’une mise à jour du système d’exploitation n’est pas nécessaire pour isoler vos applications s’exécutant sur Azure des autres clients utilisant Azure, il est toujours judicieux de maintenir vos versions de système d’exploitation à jour. 

Vous trouverez ci-dessous nos actions recommandées pour mettre à jour votre système d’exploitation en fonction des offres suivantes : 

<table>
<tr>
<th>Offre</th> <th>Action recommandée </th>
</tr>
<tr>
<td>Services cloud Azure </td>  <td>Activez la mise à jour automatique ou vérifiez que vous exécutez le dernier système d’exploitation invité.</td>
</tr>
<tr>
<td>Machines virtuelles Linux Azure</td> <td>Installez les mises à jour de votre fournisseur de système d’exploitation dès qu’elles sont disponibles. </td>
</tr>
<tr>
<td>Machines virtuelles Windows Azure </td> <td>Vérifiez que vous exécutez une application antivirus prise en charge avant d’installer les mises à jour du système d’exploitation. Contactez votre fournisseur de logiciel antivirus pour obtenir des informations sur la compatibilité.<p> Installez le [correctif cumulatif de sécurité de janvier](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Autres services PaaS Azure</td> <td>Aucune action n’est requise de la part des clients utilisant ces services. Azure maintient automatiquement à jour les versions du système d’exploitation. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Conseils supplémentaires si vous exécutez du code non approuvé 

Aucune action supplémentaire n’est requise de la part du client, sauf si vous exécutez du code non approuvé. Si vous autorisez l’utilisation de code non approuvé (par exemple, vous autorisez l’un de vos clients à charger un extrait de code ou un fichier binaire que vous exécutez ensuite dans le cloud au sein de votre application), vous devez suivre les étapes supplémentaires suivantes.  


### <a name="windows"></a>Windows 
Si vous utilisez Windows et hébergez du code non approuvé, vous devez également activer une fonctionnalité Windows appelée Kernel Virtual Address (KVA) Shadowing (Copie shadow d’adresse virtuelle du noyau), qui fournit une protection supplémentaire contre les vulnérabilités par canal latéral de l’exécution spéculative. Par défaut, cette fonctionnalité est désactivée ; en cas d’activation, elle peut avoir des conséquences sur les performances. Suivez les instructions de [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour l’activation des protections sur le serveur. Si vous exécutez Azure Cloud Services, vérifiez que vous utilisez WA-GUEST-OS-5.15_201801-01 ou WA-GUEST-OS-4.50_201801-01 (disponible à partir du 10 janvier 2018) et activez la clé de Registre via une tâche de démarrage.


### <a name="linux"></a>Linux
Si vous utilisez Linux et hébergez du code non approuvé, vous devez également mettre à jour Linux vers une version plus récente qui implémente KPTI (Kernel Page Table Isolation, isolation de tables de pages du noyau) séparant les tables de pages utilisées par le noyau de celles appartenant à l’espace utilisateur. Ces solutions d’atténuation nécessitent une mise à jour du système d’exploitation Linux et peuvent être obtenues auprès de votre fournisseur de solutions de distribution dès qu’elles sont disponibles. Votre fournisseur de système d’exploitation peut vous indiquer si les protections sont activées ou désactivées par défaut.



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protéger les clients Azure des vulnérabilités de processeur).
