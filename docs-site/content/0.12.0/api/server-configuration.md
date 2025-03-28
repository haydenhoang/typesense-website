---
sitemap:
  priority: 0.3
---

# Server Configuration

## Using Command Line Arguments

Command line arguments can be passed to the server as `--parameter=value`.

| Parameter      | Required    |Description                                            |
| -------------- | ----------- |-------------------------------------------------------| 
|`--config`         | false       |Path to the configuration file. If you use this argument, you can define all of the other command line arguments in a configuration file. See the "Configuring Typesense" section for more details.|
|`--api-key`	|true	|API key that allows all operations.|
|`--data-dir`	|true	|Path to the directory where data will be stored on disk.|
|`--api-address`	|false	|Address to which Typesense API service binds. Default: `0.0.0.0`|
|`--api-port`	|false	|Port on which Typesense API service listens. Default: `8108`|
|`--peering-address`	|false	|Internal IP address to which Typesense peering service binds. If this parameter is not specified, Typesense will attempt to use the first available internal IP.|
|`--peering-port`	|false	|Port on which Typesense peering service listens. Default: `8107`|
|`--nodes`	|false	|Path to file containing comma separated string of all nodes in the cluster.|
|`--search-only-api-key`	|false	|API key that allows only searches (i.e. restricted to the `/collections/collection_name/documents/search` end-point). Use this to make search requests directly from Javascript, without exposing your primary API key.|
|`--log-dir`	|false	|By default, Typesense logs to stdout and stderr. To enable logging to a file, provide a path to a logging directory.|
|`--ssl-certificate`	|false	|Path to the SSL certificate file. You must also define `ssl-certificate-key` to enable HTTPS.|
|`--ssl-certificate-key`	|false	|Path to the SSL certificate key file. You must also define `ssl-certificate` to enable HTTPS.|
|`--enable-cors`	|false	|Allow JavaScript client to access Typesense directly from the browser.|

## Using a Configuration File

As an alternative to command line arguments, you can also configure Typesense server through a configuration file or via environment variables.

Command line arguments are given the highest priority, while environment variables are given the least priority.

<Tabs :tabs="['Shell']">
  <template v-slot:Shell>

```bash
./typesense-server --config=/etc/typesense/typesense-server.ini
```

  </template>
</Tabs>

Our Linux DEB/RPM packages install the configuration file at `/etc/typesense/typesense-server.ini`.

The configuration file uses a simple INI format:

<Tabs :tabs="['INI']">
  <template v-slot:INI>

```ini
; /etc/typesense/typesense-server.ini

[server]

api-key = Rhsdhas2asasdasj2
data-dir = /tmp/ts
log-dir = /tmp/logs
api-port = 9090
```
  </template>
</Tabs>

## Using Environment Variables

If you wish to use environment variables, you can do that too. The environment variables map to the command line arguments documented above: just use CAPS and underscores instead of hyphens, and prefix the variable names with `TYPESENSE_`.

For example, use `TYPESENSE_DATA_DIR` for the `--data-dir` argument.

<Tabs :tabs="['Shell']">
  <template v-slot:Shell>

```bash
TYPESENSE_DATA_DIR=/tmp/ts TYPESENSE_API_KEY=AS3das2awQ2 ./typesense-server
```
  </template>
</Tabs>
