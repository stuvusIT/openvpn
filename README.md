# openvpn

This role installs OpenVPN, configures it as a server and can optionally create client certificates.

## Requirements

This role requires an apt based system.


## Role Variables

| Role variable                        | Default                            | Description                                                                                                                                                        |
| ------------------------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `openvpn_base_dir`                   | `/etc/openvpn`                     | Path where your OpenVPN config will be stored                                                                                                                      |
| `openvpn_key_dir`                    | `/etc/openvpn/keys`                | Path where your server private keys and CA will be stored                                                                                                          |
| `openvpn_port`                       | `1194`                             | The port you want OpenVPN to run on.                                                                                                                               |
| `openvpn_server_hostname`            | `{{inventory_hostname}}`           | The server name to place in the client configuration file (if different from the `inventory_hostname`)                                                             |
| `openvpn_proto`                      | `udp`                              | The protocol you want OpenVPN to use                                                                                                                               |
| `openvpn_dualstack`                  | `true`                             | Whether or not to use a dualstack (IPv4 + v6) socket                                                                                                               |
| `openvpn_rsa_bits`                   | `2048`                             | Number of bit used to protect generated certificates                                                                                                               |
| `openvpn_service_name`               | `openvpn`                          | Name of the service. Used by systemctl to start the service                                                                                                        |
| `openvpn_use_pregenerated_dh_params` | `false`                            | DH params are generted with the install by default                                                                                                                 |
| `openvpn_use_modern_tls`             | `true`                             | Use modern Cipher for TLS encryption                                                                                                                               |
| `openvpn_verify_cn`                  | `false`                            | Check that the CN of the certificate matches the FQDN                                                                                                              |
| `openvpn_redirect_gateway`           | `true`                             | OpenVPN gateway push                                                                                                                                               |
| `openvpn_set_dns`                    | `true`                             | Will push DNS to the client                                                                                                                                        |
| `openvpn_enable_management`          | `true`                             |                                                                                                                                                                    |
| `openvpn_management_bind`            | `/var/run/openvpn/management unix` | The interface to bind on for the management interface. Can be unix or TCP socket.                                                                                  |
| `openvpn_management_client_user`     | `root`                             | Use this user when using a Unix socket for management interface.                                                                                                   |
| `openvpn_tls_auth_required`          | `true`                             | Ask the client to push the generated ta.key of the server during the   connection                                                                                  |
| `openvpn_tunnel_ipv6_network`        | `false`                            | The network address and prefix of an IPv6 network to assign to clients. IPv4 would still be used, too.                                                             |
| `openvpn_ca_key`                     |                                    | CA key containing both crt and the private key. If not set, CA cert and key will be automatically generated on the target system.                                  |
| `openvpn_tls_auth_key`               |                                    | Single item with a pre-generated TLS authentication key.                                                                                                           |
| `openvpn_topology`                   | `false`                            | the `topology` keyword will be set in the server config with   the specified value.                                                                                |
| `openvpn_push`                       | `empty`                            | Set here a list of string that will be placed as `push "<string>"`. E.g. `- route 10.20.30.0 255.255.255.0` will generate `push "route 10.20.30.0 255.255.255.0"`. |
| `openvpn_crl_path`                   |                                    | Define a path to the CRL file for revocations.                                                                                                                     |
| `openvpn_use_crl`                    | `false`                            | Configure OpenVPN server to honor certificate revocation list.                                                                                                     |
| `openvpn_client_register_dns`        | `true`                             | Add `register-dns` option to client config (Windows only).                                                                                                         |
| `openvpn_duplicate_cn`               | `false`                            | Add `duplicate-cn` option to server config - this allows clients to connect multiple times with the one key.                                                       |
| `openvpn_clients`                    | `[]`                               | List of [client objects](#client-objects) for which certificates should be generated.                                                                              |
| `openvpn_dns_servers`                | `["8.8.8.8","8.8.4.4"]`            | List of DNS servers to push to the client                                                                                                                          |
| `openvpn_cipher`                     | `AES-256-CBC`                      | Cipher to use.                                                                                                                                                     |
| `openvpn_auth_hash_algo`             | `SHA256`                           | Algorithm to use for auth.                                                                                                                                         |
| `openvpn_openssl_digest`             | `sha256`                           | Digest Algorithm to use when signing and creating certs.                                                                                                           |
| `openvpn_openssl_days`               | `3650`                             | How many days are the certs valid.                                                                                                                                 |
| `openvpn_use_lzo`                    | `true`                             | Enable or disable compression.                                                                                                                                     |
| `openvpn_tls_cipher`                 |                                    | List of TLS Cipher to support                                                                                                                                      |
| `openvpn_fetch_configs`              | `true`                             | Download client configurations from the server.                                                                                                                    |

### Client objects

A client object is a dictionary that can contain the following keys.

| Key          | Mandatory?               | Description                                                            |
| ------------ | ------------------------ | ---------------------------------------------------------------------- |
| `name`       | :heavy_check_mark:       | Name of the client. Has to be unique.                                  |
| `ip_address` | :heavy_multiplication_x: | IP address given to the client via `ifconfig-push`                     |
| `netmask`    | :heavy_multiplication_x: | Netmask if that IP address                                             |
| `push`       | :heavy_multiplication_x: | Miscellaneous strings to be used with the `push` command to the client |

### LDAP

| Role variable      | Default | Description                                                                    |
| ------------------ | ------- | ------------------------------------------------------------------------------ |
| `openvpn_use_ldap` | `false` | Active LDAP backend for authentication. Client certificate not needed anymore. |
| `openvpn_ldap`     |         | Dictionary that contains the LDAP configuration](#the-openvpn-ldap-object)     |

#### The `openvpn_ldap` object

The contents of this dictionary are only relevant if `openvpn_use_ldap` is `true`.
It is a dictionary that can contain the following keys.

| Key                   | Mandatory?                | Example                                   | Description                                                                                 |
| --------------------- | ------------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------------- |
| `url`                 | :heavy_check_mark:        | `ldap://host.example.com`                 | Address of you LDAP backend with syntax ldap[s]://host[:port]                               |
| `anonymous_bind`      | :heavy_check_mark:        | `False`                                   | This is not an Ansible boolean but a string that will be pushed into the configuration file |
| `bind_dn`             | :heavy_check_mark:        | `uid=Manager,ou=People,dc=example,dc=com` | Bind DN used if "anonymous_bind" set to "False"                                             |
| `bind_password`       | :heavy_check_mark:        | `mysecretpassword`                        | Password of the bind_dn user                                                                |
| `tls_enable`          | :heavy_check_mark:        | `no`                                      | Enable STARTTLS. Not necessary with ldaps addresses                                         |
| `tls_ca_cert_file`    | If `tls_enable` is `true` | `/etc/openvpn/auth/ca.pem`                | Path to the CA ldap backend. This must have been pushed before                              |
| `base_dn`             | :heavy_check_mark:        | `ou=People,dc=example,dc=com`             | Base DN where the backend will look for valid user                                          |
| `search_filter`       | :heavy_check_mark:        | `(&(uid=%u)(accountStatus=active))`       | Filter the ldap search                                                                      |
| `require_group`       | :heavy_check_mark:        |                                           | This is not an Ansible boolean but a string that will be pushed into the configuration file |
| `group_base_dn`       | :heavy_check_mark:        | `ou=Groups,dc=example,dc=com`             | Precise the group to look for. Required if require_group is set to   "True"                 |
| `group_search_filter` | :heavy_check_mark:        | `((cn=developers)(cn=artists))`           | Precise valid groups                                                                        |

### Routing vs Bridging

The `openvpn_use_bridge` role variable lets you chose between [routing and bridging](https://community.openvpn.net/openvpn/wiki/BridgingAndRouting).

| Role variable        | Default | Description                                         |
| -------------------- | ------- | --------------------------------------------------- |
| `openvpn_use_bridge` | `false` | Enables bridging (TAP) as opposed to routing (TUN). |

#### Routing

The following variables are only relevant if you chose *routing* (i.e. `openvpn_use_bridge` is `false`).

| Role variable            | Default         | Description                             |
| ------------------------ | --------------- | --------------------------------------- |
| `openvpn_tunnel_network` | `10.9.0.0`      | Private network used by OpenVPN service |
| `openvpn_tunnel_netmask` | `255.255.255.0` | Netmask of the private network          |

#### Bridging

The following variables are only relevant if you chose *bridging) (i.e. `openvpn_use_bridge` is `true`).

| Role variable                      | Default         | Description                                                                         |
| ---------------------------------- | --------------- | ----------------------------------------------------------------------------------- |
| `openvpn_bridge_name`              | `br0`           | Name of the bridge                                                                  |
| `openvpn_bridge_eth_interface`     | `eth0`          | Ethernet interface that's connected to the bridge                                   |
| `openvpn_bridge_address`           |                 | IP address of the bridge interface. Defaults to no IP address being configured.     |
| `openvpn_bridge_enable_dhcp`       | `true`          | Enable OpenVPN's own DHCP server                                                    |
| `openvpn_bridge_dhcp_push_gateway` | `192.168.0.1`   | Relevant if `openvpn_bridge_enable_dhcp` is `true`. Gateway address for the clients |
| `openvpn_bridge_dhcp_push_netmask` | `255.255.255.0` | Relevant if `openvpn_bridge_enable_dhcp` is `true`. Netmask of the bridge network   |
| `openvpn_bridge_dhcp_range_start`  | `192.168.0.128` | Relevant if `openvpn_bridge_enable_dhcp` is `true`. Start of the DHCP range         |
| `openvpn_bridge_dhcp_range_end`    | `192.168.0.254` | Relevant if `openvpn_bridge_enable_dhcp` is `true`. End of the DHCP range           |

On the bridge network, client IP address allocation can be handled in two ways:

* If `openvpn_bridge_enable_dhcp` is `true`:
  Let OpenVPN run an own DHCP server on the bridge network.
* If `openvpn_bridge_enable_dhcp` is `false`:
  Use an external DHCP server that's connected through the bridged ethernet interface.
  You have to set that DHCP server up yourself.

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).

## Author Information

* [Fritz Otlinghaus (Scriptkiddi)](https://github.com/scriptkiddi) _fritz.otlinghaus@stuvus.uni-stuttgart.de_
* [Markus Mroch (Mr. Pi)](https://github.com/Mr-Pi) &lt;_markus.mroch@stuvus.uni-stuttgart.de_&gt;
