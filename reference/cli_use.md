# Using aio CLI

Before using the `aio` CLI, you have to configure it. Essentially, you will be setting the credentials needed to talk with Adobe I/O on behalf of your organization.

## Configure the authentication

To create the credentials, you have to go to [Adobe I/O Console](https://console.adobe.io) and create an integration that is configured to access the **I/O Management API.** Note that you need System Administrator or Developer Role permissions to create this integration.

After you&rsquo;ve created the integration, create a `config.json` file on your computer and navigate to the integration Overview page. From this page, copy the `client_id` and `client_secret` values to the config file; if you navigate to the JWT tab in Console, you&rsquo;ll get the value for the `jwt_payload`.

```
//config.json 
{
  "client_id": "value from your CLI integration (String)",
  "client_secret": "value from your CLI integration (String)",
  "jwt_payload": { value from your CLI integration (JSON Object Literal) },
  "token_exchange_url": "https://ims-na1.adobelogin.com/ims/exchange/jwt",
  "console_get_orgs_url":"https://api.adobe.io/console/organizations",
  "console_get_namespaces_url":"https://api.adobe.io/runtime/admin/namespaces/"
}
```

The last bit you need to have at hand is the private certificate you&rsquo;ve used to create the integration; you need the private key, not the public one. Now, you are ready to configure the `aio` CLI.

First, configure the credentials:

```
aio config:set jwt-auth PATH_TO_CONFIG_JSON_FILE --file --json
```

Then, configure the private certificate:

```
aio config:set jwt-auth.jwt_private_key PATH_TO_PRIVATE_KEY_FILE
```

Note that this just stores the path to your private key in the CLI configuration.

To test that aio CLI can actually authenticate against Adobe I/O, run this command to list all the integrations that your organization has:

```
aio console:list-integrations
```

## Using aio 

Now you can use the `aio` CLI to select the namespace you want to use for deploying actions. Any integration you&rsquo;ve created can be used as a namespace; there is a 1:1 relationship between an integration and a namespace. 

You can't manage multiple namespaces at the same time. You can only have one namespace active always, and all the actions you performed are against the active namespace.

To list the available integrations, run:

```
$ aio console:list-integrations
Namespace     Name                  Status
NUMBER_NUMBER Integration Name 1    ENABLED
NUMBER_NUMBER Integration Name 2    ENABLED (currently selected)
NUMBER_NUMBER Integration Name 3    ENABLED
```

This command will list all available integrations in your org. Pipe the command to [`less`](https://en.wikipedia.org/wiki/Less_(Unix)) or [`more`](https://en.wikipedia.org/wiki/More_(command)) to page the results. 

If you want to select an integration as the current namespace, run this command and pass as the argument the <NUMBER_NUMBER> you see listed when running `aio console:list-integrations`:

```
aio console:select-integration <NUMBER_NUMBER>
```

This command will generate the `.wskprops` file in your user directory (or overwrite if the file already exists) for accessing that given namespace. Now, you can use the `wsk` CLI to manage this namespace: create/invoke actions and so forth.

### Reseting the auth credentials for a namespace

If you want to change the credentials used to manage a namespace (the hash stored in the `.wskprops` file), you can run this command:
```
aio console:reset-integration INTEGRATION_ID
```
You can retrieve the `INTEGRATION_ID` from I/O Console or by listing all the integrations and look for the one you need (`aio console:list-integrations`) - the second number on each row represents is the `INTEGRATION_ID`.