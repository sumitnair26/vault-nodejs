
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
