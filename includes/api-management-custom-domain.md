## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Réponse du serveur proxy APIM avec les certificats SSL durant la négociation TLS

### <a name="clients-calling-with-sni-header"></a>Clients appelant avec l’en-tête SNI
Si le client possède un ou plusieurs domaines personnalisés configurés pour le proxy, APIM peut répondre aux requêtes HTTPS des domaines personnalisés (par exemple, contoso.com), ou des domaines par défaut (par exemple, apim-nom-du-service.azure-API.NET). Selon les informations contenues dans l’en-tête Indication du nom du serveur (SNI), APIM répond avec le certificat de serveur approprié.

### <a name="clients-calling-without-sni-header"></a>Clients appelant sans l’en-tête SNI
Si le client utilise un client, qui n’envoie pas l’en-tête [SNI](https://tools.ietf.org/html/rfc6066#section-3), APIM crée des réponses en fonction de la logique suivante :

* Si le service n’a qu’un seul domaine personnalisé configuré pour le proxy, le certificat par défaut est le certificat ayant été émis pour le domaine personnalisé du proxy.
* Si le service a configuré plusieurs domaines personnalisés pour le proxy (uniquement pris en charge au niveau **Premium**), le client peut désigner le certificat par défaut. Pour définir le certificat par défaut, la propriété [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#definitions_hostnameconfiguration) doit être définie sur true (« defaultSslBinding » : « true »). Si le client ne définit pas la propriété, le certificat par défaut est le certificat émis pour le domaine du proxy par défaut hébergé sur *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Prise en charge pour la requête PUT/POST avec une charge utile de grande taille

Le serveur proxy APIM prend en charge la requête avec une charge utile de grande taille lors de l’utilisation de certificats côté client dans le protocole HTTPS (par exemple, charge utile > 40 Ko). Pour éviter que la requête du serveur ne gèle, les clients peuvent définir la propriété [« negotiateClientCertificate » : « true »](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#definitions_hostnameconfiguration) sur le nom d’hôte du proxy. Si la propriété est définie sur true, le certificat client est demandé au moment de la connexion SSL/TLS, avant tout échange de requête HTTP. Étant donné que le paramètre s’applique au niveau du **Nom d’hôte du proxy** toutes les demandes de connexion demandent le certificat client. Les clients peuvent configurer jusqu’à 20 domaines personnalisés pour le proxy (uniquement pris en charge au niveau **Premium**) et contourner cette limitation.

