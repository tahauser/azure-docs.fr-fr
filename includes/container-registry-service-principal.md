## <a name="create-a-service-principal"></a>Créer un principal du service

Pour créer un principal de service ayant accès à votre registre de conteneurs, vous pouvez utiliser le script suivant. Mettez à jour la variable `ACR_NAME` avec le nom de votre registre de conteneurs et éventuellement avec la valeur `--role` dans la commande [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] pour accorder des autorisations différentes. L’[interface de ligne de commande Azure](/cli/azure/install-azure-cli) doit être installée pour que vous puissiez utiliser ce script.

Après avoir exécuté le script, notez l’**ID** et le **mot de passe** du principal de service. Une fois que vous avez noté les informations d’identification, vous pouvez configurer vos applications et services afin qu’ils s’authentifient auprès de votre registre de conteneurs en tant que principal du service.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>Utiliser un principal de service existant

Pour accorder l’accès au registre à un principal de service existant, vous devez assigner un nouveau rôle au principal de service. Comme lorsque vous créez un principal de service, vous pouvez accorder un accès en extraction, en envoi et en extraction, ou un accès propriétaire.

Le script suivant utilise la commande [az role assignment create][az-role-assignment-create] pour accorder des autorisations en *extraction* à un principal de service que vous spécifiez dans la variable `SERVICE_PRINCIPAL_ID`. Ajustez la valeur `--role` si vous souhaitez accorder un niveau d’accès différent.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create