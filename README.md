
# Vault NodeJS
Setting up Vault with NodeJS

## Start Vault in Dev Mode

`docker run --name vault -p 8200:8200 vault:1.7.3`

Head over to http://localhost:8200 and you’ll see the login screen below on the Vault web UI.

## Enable KV Secret Backend

Enter your root token (copied from the previous step) and hit “Sign In.”

You can see that there is already a KV backend enabled at path secret. This comes enabled in dev mode by default.

## Configure AppRole Auth Method

We’ll now configure the AppRole auth method, which our Node.js application will use to retrieve the secrets from our key value backend.

Select Access from the top menu. You’ll see only the token method enabled. 

Click Enable New Method and select AppRole. Leave the settings to default and click Enable Method.

## Create Policy for Secret Access

We’ll create a policy that allows read-only access to the KV secret backend.

Select Policies from the top menu and click Create ACL Policy.

Enter name as readonly-kv-backend, and enter following content for Policy.

`path "secret/data/mysql/webapp" {
  capabilities = [ "read" ]
}`

Following the principle of least privilege, this policy will only give read access to secrets at the specific path.

Hit Create Policy to save it.

## Create AppRole for Node.js Application

We’re going to switch gears and use Vault CLI to finish setting up our demo. There are two ways to access Vault CLI; you can download the Vault binary, or you can exec into Vault container and access the CLI. For this demo we’ll use the latter.

`docker exec -it vault /bin/sh`

We’ll then set up the VAULT_ADDR and VAULT_TOKEN environment variables.

`export VAULT_ADDR=http://localhost:8200 export VAULT_TOKEN=<ROOT TOKEN>`


Now let’s create an AppRole and attach our policy to this role. 

`vault write auth/approle/role/node-app-role \
    token_ttl=1h \
    token_max_ttl=4h \
    token_policies=readonly-kv-backend`

You should be able to see it being created successfully.

`Success! Data written to: auth/approle/role/node-app-role`

Each AppRole has a RoleID and SecretID, much like a username and password. The application can exchange this RoleID and SecretID for a token, which can then be used in subsequent requests.

## Get RoleID and SecretID

Now we’ll fetch the RoleID pertaining to the node-app-role via the following command:

`vault read auth/approle/role/node-app-role/role-id`

Next we’ll fetch the `SecretID`:

vault write -f auth/approle/role/node-app-role/secret-id

Make sure you store these values somewhere safe, as we’ll use them in our Node.js application.

Response Wraping : https://developer.hashicorp.com/vault/tutorials/auth-methods/approle#response-wrap-the-secretid

## Create a Secret

For testing in node JS application add Secrets 

`vault kv put secret/mysql/webapp db_name="users" username="admin" password="passw0rd"`

# Here we have finished all setup steps now we can proceed with node JS Code
`node index.js`
