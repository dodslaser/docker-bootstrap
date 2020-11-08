Various init scripts for bootstraping docker containers

**USAGE:** Set `command` to `/bin/sh -c "curl -fsSL [init-script-url] | /bin/sh"`

# init-dns

Bootstrap a local DNS based on pihole

- **INGRESS_IP** IP of the ingress
- **INGRESS_DOMAIN** base domain of the ingress

# init-pki

Bootstrap a PKI based on smallstep/step-ca from environment variables

- **NAME** Name of the CA
- **DNS** Comma-separated DNS names and/or IPs of the CA
- **PROVISIONER** Name of the default provisioner

The ca-password is randomly generated in `/home/step/secrets/password`

An ACME provisioner is created by default (accessible at `https://[ca-url]/acme/acme/directory`)

The config file is patched to enable the `badgerV2` database with `fileIO` to prevent breaking on armv7 (See [issue #279](https://github.com/smallstep/certificates/issues/279) in smallstep/certificates).

**Note:** Your ingress will need to trust the generated `root_ca.crt` to obtain certificates with ACME. For traefik this can be achieved by mounting the `pki-certs` volume from the above example to `/certs` and setting `LEGO_CA_CERTIFICATES` to `/certs/root_ca.crt`
