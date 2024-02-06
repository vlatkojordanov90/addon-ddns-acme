# Home Assistant Add-on: DDNS-ACME

## Installation

Follow these steps to get the add-on installed on your system:

1. Navigate in your Home Assistant frontend to **Supervisor** -> **Add-on Store**.
2. Find the "DynuDNS" add-on and click it.
3. Click on the "INSTALL" button.

## How to use

1. Visit [dynu.com](https://www.dynu.com) and create an account by logging in through any of the available account services (Google, Github, Twitter, Persona, Reddit).
2. In the `Control Panel` area, click on the `DDNS services` section, click `Add`. Choose amongst the available domains, and type the name of the subdomain .
3. If registration was a success, the subdomain is listed in the `DDNS services` section along with `current ip` being the public IP address of the device you are currently using to access `dynu.com`. The IP address will be updated by the DynuDNS add-on.
4. In the DynuDNS add-on configuration, perform the following:
    - Copy the DynuDNS API key (in the control panel, go to `API credentials`, reveal and copy) from `dynu.com` and paste into the `token` option.
    - Update the `domains` option with the full domain name you registered. E.g., `my-domain.ddnsfree.com`.

## Configuration

Add-on configuration:

```yaml
options:
  acme_provider:
    provider_name: "lets_encrypt"
    accept_terms: false
    certfile: "fullchain.pem"
    keyfile: "privkey.pem"
    renew_wait: 43200
  dns_provider:    
    provider_name: ""
    dns_api_token: ""
  domains: ["my-domain.dynudns.org"]
  aliases: []
  ip_update_wait_seconds: 300
```

Additionally, you'll need to configure the Home Assistant Core to pick up the SSL certificates. This is done by setting the following configuration for the [HTTP][HTTP] integration configuration in your `configuration.yaml`:

```yaml
http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem
```

### Option group `acme_provider`

The following options are for the option group: `acme_provider`. These settings
only apply to ACME SSL certificates.

#### Option `acme_provider.accept_terms`

Once you have read and accepted the Let's Encrypt[Subscriber Agreement](https://letsencrypt.org/repository/), change value to `true` in order to use Let's Encrypt services.

#### Option `acme_provider.certfile`

The name of the certificate file generated by Let's Encrypt. The file is used for SSL by Home Assistant add-ons and is recommended to keep the filename as-is (`fullchain.pem`) for compatibility.

**Note**: _The file is stored in `/ssl/`, which is the default for Home Assistant_

#### Option `acme_provider.keyfile`

The name of the private key file generated by Let's Encrypt. The private key file is used for SSL by Home Assistant add-ons and is recommended to keep the filename as-is (`privkey.pem`) for compatibility.

**Note**: _The file is stored in `/ssl/`, which is the default for Home Assistant_

#### Option: `ipv4_fixed` (optional)

This option allows you to override the auto-detection and specify an
IPv4 address manually.

#### Option: `ipv6_fixed` (optional)

This option allows you to override the auto-detection and specify an
IPv6 address manually.

### Option group `dns_provider`

The following options are for the option group: `dns_provider`. These settings
only apply to DNS API Settings.

#### Option `dns_provider.dns_api_token`

The API Token for your DNS Provider

#### Option `dns_provider.provider_name`

The name of your dns provider, for example: dynu

### Option: `domains`

A list of DynuDNS subdomains registered under your account.

#### Option: `aliases` (optional)

A list aliases of domains configured on the `domains` option.
This is useful in cases where you would like to use your own domain.
Create a CNAME record to point at the DynuDNS subdomain and set this value accordingly.

For example:

```yaml

```

Don't add your custom domain name to the `domains` array. For certificate creation, all unique domains and aliases are used.

Also, don't forget to make sure the dns-01 challenge can reach Duckdns. It might be required to add a specific CNAME for that:

```
CNAME _acme-challenge.<own-domain>    _acme-challenge.<domain>.dynudns.org
CNAME                 <own-domain>                    <domain>.dynudns.org
```

#### Option: `ip_update_wait_seconds`

The number of seconds to wait before updating DynuDNS subdomains and renewing Let's Encrypt certificates.

## Known issues and limitations

- A free DynuDNS account is limited to four subdomains.

## Credit

This add-on was heavily re-implemented using bits from a few different places:
[upstream addon repo]: https://github.com/koying/ha-addons/blob/main/dynudns-addon/data/run.sh
[upstream run.sh]: https://github.com/home-assistant/addons/blob/master/duckdns/data/run.sh
[upstream hook.sh]: https://github.com/home-assistant/addons/blob/master/duckdns/data/hook.sh
[upstream dnsdynu.sh]: https://github.com/acmesh-official/acme.sh/blob/master/dnsapi/dns_dynu.sh

## Support

- The Home Assistant [Community Forum][forum].

In case you've found a bug, please [open an issue on our GitHub][issue].

[forum]: https://community.home-assistant.io
[issue]: https://github.com/vittorio88/addon-ddns-acme/issues
[dynudns]: https://www.dynudns.org
[dynudns-faq]: https://www.dynu.com/FAQ/Dynamic-DNS-Service
[dynudns-api-docs]: https://www.dynu.com/Support/API
[HTTP]: https://www.home-assistant.io/integrations/http/