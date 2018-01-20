# openvpn

This role installs OpenVPN, configures it as a server and can optionally create client certificates.


## Requirements

This role requires an apt based system.


## Role Variables

| Variable                           | Default / Mandatory                              | Description                                                                                                                                                     |
|------------------------------------|--------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| openvpn_base_dir                   | `/etc/openvpn`                                   | Path where your OpenVPN config will be stored                                                                                                                   |
| openvpn_key_dir                    | `/etc/openvpn/keys`                              | Path where your server private keys and CA will be stored                                                                                                       |
| openvpn_port                       | `1194`                                           | The port you want OpenVPN to run on.                                                                                                                            |
| openvpn_server_hostname            | `{{inventory_hostname}}`                         | The server name to place in the client configuration file (if different from the `inventory_hostname`)                                                          |
| openvpn_proto                      | `udp`                                            | The protocol you want OpenVPN to use                                                                                                                            |
| openvpn_dualstack                  | `true`                                           | Whether or not to use a dualstack (IPv4 + v6) socket                                                                                                            |
| openvpn_config_file                | `openvpn_{{ openvpn_proto }}_{{ openvpn_port }}` | The config file name you want to   use                                                                                                                          |
| openvpn_rsa_bits                   | `2048`                                           | Number of bit used to protect generated certificates                                                                                                            |
| openvpn_service_name               | `openvpn`                                        | Name of the service. Used by systemctl to start the service                                                                                                     |
| openvpn_use_pregenerated_dh_params | `false`                                          | DH params are generted with the install by default                                                                                                              |
| openvpn_use_modern_tls             | `true`                                           | Use modern Cipher for TLS encryption                                                                                                                            |
| openvpn_verify_cn                  | `false`                                          | Check that the CN of the certificate matches the FQDN                                                                                                           |
| openvpn_redirect_gateway           | `true`                                           | OpenVPN gateway push                                                                                                                                            |
| openvpn_set_dns                    | `true`                                           | Will push DNS to the client
| openvpn_enable_management          | `true`                                           |                                                                                                                                                                 |
| openvpn_management_bind            | `/var/run/openvpn/management unix`               | The interface to bind on for the management interface. Can be unix or TCP socket.                                                                               |
| openvpn_management_client_user     | `root`                                           | Use this user when using a Unix socket for management interface.                                                                                                |
| openvpn_server_network             | `10.9.0.0`                                       | Private network used by OpenVPN service                                                                                                                         |
| openvpn_server_netmask             | `255.255.255.0`                                  | Netmask of the private network                                                                                                                                  |
| openvpn_tls_auth_required          | `true`                                           | Ask the client to push the generated ta.key of the server during the   connection                                                                               |
| openvpn_server_ipv6_network        | `false`                                          | If set, the network address and prefix of an IPv6 network to assign to   clients. If True, IPv4 still used too.                                                 |
| openvpn_ca_key                     |                                                  | CA key containing both crt and the private key. If not set, CA cert and key will be automatically generated on the target system.                               |
| openvpn_tls_auth_key               |                                                  | Single item with a pre-generated TLS authentication key.                                                                                                        |
| openvpn_topology                   | `false`                                          | the "topology" keyword will be set in the server config with   the specified value.                                                                             |
| openvpn_push                       | `empty`                                          | Set here a list of string that will be placed as "push   "<string>". E.g `- route 10.20.30.0 255.255.255.0` will generate push "route 10.20.30.0 255.255.255.0" |
| openvpn_use_ldap                   | `false`                                          | Active LDAP backend for authentication. Client certificate not needed   anymore                                                                                 |
| openvpn_ldap                       |                                                  | Dictionary that contains LDAP configuration                                                                                                                     |
| openvpn_crl_path                   |                                                  | Define a path to the CRL file for revokations.                                                                                                                  |
| openvpn_use_crl                    |                                                  | Configure OpenVPN server to honor certificate revocation list.                                                                                                  |
| openvpn_client_register_dns        | `true`                                           | Add `register-dns` option to client config (Windows only).                                                                                                      |
| openvpn_duplicate_cn               | `false`                                          | Add `duplicate-cn` option to server config - this allows clients to connect multiple times with the one key.                                                    |
| openvpn_clients                    | `[]`                                             | List of client names for which certificates should be generated                                                                                                 |
| openvpn_bridge_dev                 | `br0`                                            | Name of the bridge interface beeing used for bridging                                                                                                           |
| openvpn_bridge                     |                                                  | Bridge settings to be configured for defaults see below                                                                                                         |
| openvpn_dns_servers                | `["8.8.8.8","8.8.4.4"]`                          | List of DNS servers to push to the client                                                                                                                       |
| openvpn_cipher                     | `AES-256-CBC`                                    | Cipher to use.                                                                                                                                                  |
| openvpn_auth_hash_algo             | `SHA256`                                         | Algorithm to use for auth.                                                                                                                                      |
| openvpn_openssl_digest             | `sha256`                                         | Digest Algorithm to use when signing and creating certs.                                                                                                        |
| openvpn_openssl_days               | `3650`                                           | How many days are the certs valid.                                                                                                                              |
| openvpn_use_lzo                    | `true`                                           | Enable or disable compression.                                                                                                                                  |

### LDAP object

| Variable            | Default / Mandatory                       | Description                                                                                    |
|---------------------|-------------------------------------------|------------------------------------------------------------------------------------------------|
| url                 | `ldap://host.example.com`                 | Address of you LDAP backend with syntax ldap[s]://host[:port]                                  |
| anonymous_bind      | `False`                                   | This is not an Ansible boolean but a string that will be pushed into the configuration file |
| bind_dn             | `uid=Manager,ou=People,dc=example,dc=com` | Bind DN used if "anonymous_bind" set to "False"                                                |
| bind_password       | `mysecretpassword`                        | Password of the bind_dn user                                                                   |
| tls_enable          | `no`                                      | Force TLS encryption. Not necessary with ldaps addresses                                       |
| tls_ca_cert_file    | `/etc/openvpn/auth/ca.pem`                | Path to the CA ldap backend. This must have been pushed before                             |
| base_dn             | `ou=People,dc=example,dc=com`             | Base DN where the backend will look for valid user                                             |
| search_filter       | `(&(uid=%u)(accountStatus=active))`       | Filter the ldap search                                                                         |
| require_group       |                                           | This is not an Ansible boolean but a string that will be pushed into the configuration file |
| group_base_dn       | `ou=Groups,dc=example,dc=com`             | Precise the group to look for. Required if require_group is set to   "True"                    |
| group_search_filter | `((cn=developers)(cn=artists))`           | Precise valid groups                                                                           |

### openvpn_bridge

| Variable   | Default / Mandatory | Description                                  |
|------------|---------------------|----------------------------------------------|
| address    | `192.168.0.1`       | Address of the vpn server in the network     |
| netmask    | `255.255.255.0`     | Netmask of the bridge network                |
| network    | `192.168.0.0`       | Address of the bridge network                |
| broadcast  | `192.168.0.255`     | Broadcast address                            |
| dhcp_start | `192.168.0.2`       | IP address where the dhcp range should start |
| dhcp_end   | `192.168.0.254`     | IP address where the dhcp range should end   |

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).

## Author Information

- [Fritz Otlinghaus (Scriptkiddi)](https://github.com/scriptkiddi) _fritz.otlinghaus@stuvus.uni-stuttgart.de_
