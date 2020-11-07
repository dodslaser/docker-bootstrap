# step-pki-bootstrap

Bootstrap a PKI based on smallstep/step-ca from environment variables

- **NAME** Name of the CA
- **DNS** Comma-separated DNS names and/or IPs of the CA
- **PROVISIONER** Name of the default provisioner

The ca-password is randomly generated in `/home/step/secrets/password`

An ACME provisioner is created by default (accessible at `https://[ca-url]/acme/acme/directory`)

The config file is patched to enable the `badgerV2` database with `fileIO` to prevent breaking on armv7 (See [issue #279](https://github.com/smallstep/certificates/issues/279) in smallstep/certificates).

Minimal `docker-compose` example:

```yaml
version: '3.7'

services:
  pki:
    image: smallstep/step-ca:latest
    command:  /bin/sh -c "curl -fsSL https://raw.githubusercontent.com/dodslaser/step-pki-bootstrap/main/init-pki | /bin/sh"
    environment:
      NAME: 'Example'
      DNS: 'localhost,example.mydomain'
      PROVISIONER: 'example@pki.mydomain'
    ports:
      - 9000:443/tcp
    volumes:
      - pki-data:/home/step
      - pki-certs:/home/step/certs
      - /etc/localtime:/etc/localtime:ro
```

**Note:** Your ingress will need to trust the generated `root_ca.crt` to obtain certificates with ACME. For traefik this can be achieved by mounting the `pki-certs` volume from the above example to `/certs` and setting `LEGO_CA_CERTIFICATES` to `/certs/root_ca.crt`
