# How to use a custom domain for azure container registry

Azure docker registries has a typical login url of the format `*.azurecr.io`. A customer might like to have a custom domain that associate with its own organization. The following is the guide on how to achieve that.

## Prerequisites

To setup custom domain for azure container registry, the following prerequisites are needed:

* A front end docker host which is deployed on the custom domain. We will run an nginx docker image on it. In this guide we would use the example: `registry.contoso.com`
* SSL certificate for `registry.contoso.com` with filename `cert.crt`. Private key file, with filename `private.key`. The pass-phrase of the certificate stored in a text file, with filename `pwd`
* An instance of Azure Docker Registry service as the backend. In this example we would use `peterhsuacr-microsoft.azurecr.io`

## Steps

1. On `registry.contoso.com`, copy [setup files](custom-id/) into it.
2. Create a sub-directory named `ssl`, copy `cert.crt`, `private.key`, and `pwd` into this directory. The location of these files can be changed but volume entries in the [docker-compose.yml](custom-id/docker-compose.yml#L11-L13) would then need to be updated to reflect the changes.
3. Edit [custom_domain.env](custom-domain/custom_domain.env) to point to your front end docker host (E.g. `registry.contoso.com`) and backend Azure Docker Registry (E.g. `peterhsuacr-microsoft.azurecr.io`).
4. Run `docker-compose up -d` and this should bring up the nginx service that would forward all docker requests from your front end service to the backend Azure Docker Registry.

## Quick verification

A simple way to test the setup is to call `docker login` to quickly confirm that the requests are properly forwarded.

`docker login registry.contoso.com -u <username> -p <password>`