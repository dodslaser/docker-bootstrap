Various init scripts for bootstraping docker containers

# init-dns

Bootstrap a local DNS based on pihole

```yaml
[...]
image: pihole/pihole
entrypoint: ''
command: /bin/bash -c "curl -fsSL [init-dns URL] | /bin/bash" # This needs to be bash for pihole
environment:
  - INGRESS_DOMAIN: mydomain.local # Local domain used by ingress
  - INGRESS_IP: 192.168.1.100 # Ingress IP address
[...]
```

# init-pki

Bootstrap a PKI based on smallstep/step-ca from environment variables

```yaml
[...]
image: traefik
entrypoint: ''
command: /bin/sh -c "curl -fsSL [init-pki URL] | /bin/sh"
environment:
  - NAME: MyCA # The name of the CA
  - DNS: 'pki,pki.mydomain.local' # DNS names where the PKI will be reachable 
  - PROVISIONER: 'admin@mydomain.local' # Name of the first provisioner
[...]
```

**Note**
- Your ingress will need to trust the generated `root_ca.crt` to obtain certificates with ACME. For traefik this can be achieved by mounting the `pki-certs` volume from the above example to `/certs` and setting `LEGO_CA_CERTIFICATES` to `/certs/root_ca.crt`
The ca-password is randomly generated in `/home/step/secrets/password`
- An ACME provisioner is created by default (accessible at `https://[ca-url]/acme/acme/directory`)
- The config file is patched to enable the `badgerV2` database with `fileIO` to prevent breaking on armv7 (See [issue #279](https://github.com/smallstep/certificates/issues/279) in smallstep/certificates).
