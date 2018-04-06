Transactions de clé (transactions maximales autorisées dans les 10 secondes, par coffre par région<sup>1</sup>) :

|Type de clé|Clé HSM<br>Clé CREATE|Clé HSM<br>Toutes les autres transactions|Clé logicielle<br>Clé CREATE|Clé logicielle<br>Toutes les autres transactions|
|:---|---:|---:|---:|---:|
|RSA 2 048 bits|5.|1 000|10|2000|
|RSA 3 072 bits|5.|250|10|500|
|RSA 4 096 bits|5.|125|10|250|
|ECC P-256|5.|1 000|10|2000|
|ECC P-384|5.|1 000|10|2000|
|ECC P-521|5.|1 000|10|2000|
|ECC SECP256K1|5.|1 000|10|2000|
|

Secrets, clés de compte de stockage géré et transactions de coffre :
| Type de transaction | Transactions maximales autorisées dans les 10 secondes, par coffre par région<sup>1</sup> |
| --- | --- |
| Toutes les transactions |2000 |
|

Consultez la page [Aide sur la limitation de requêtes Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) pour plus d’informations sur la façon de gérer la limitation en cas de dépassement de ces limites.

<sup>1</sup> La limite d’abonnement pour tous les types de transaction est fixée à 5x par limite de coffre de clés. Par exemple, HSM - autres transactions par abonnement sont limitées à 5000 transactions en 10 secondes par abonnement.
