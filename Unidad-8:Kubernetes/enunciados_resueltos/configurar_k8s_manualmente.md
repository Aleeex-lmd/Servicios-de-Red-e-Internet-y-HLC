# Configuracíon de k8s

## Requisitos 

Para esta instalación los requisitos seran los siguientes

| **Nodo**  | **CPU**      | **RAM**   |
| --------- | ------------ | --------- |
| master1   | 2 vCPUs      | 4 GB      |
| master2   | 2 vCPUs      | 4 GB      |
| master3   | 2 vCPUs      | 4 GB      |
| worker1   | 2 vCPUs      | 4 GB      |
| worker2   | 2 vCPUs      | 4 GB      |
| **Total** | **10 vCPUs** | **20 GB** |

Esto lo vamos a configurar en openstack, esto lo haremos en varios pasos:

## Configuracíon del router 

El primer paso va a ser generar un ruter el cual va a conectar la red externa con la red que usaremos

```bash
openstack router create RouterK8s                               

+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | UP                                   |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2025-03-03T12:54:08Z                 |
| description               |                                      |
| enable_default_route_bfd  | False                                |
| enable_default_route_ecmp | False                                |
| enable_ndp_proxy          | None                                 |
| external_gateway_info     | null                                 |
| external_gateways         | []                                   |
| flavor_id                 | None                                 |
| id                        | 200dfcac-8483-404d-a155-a784ea495ac2 |
| name                      | RouterK8s                            |
| project_id                | ccb761aa90e244e68a2d7c59ff70e79f     |
| revision_number           | 1                                    |
| routes                    |                                      |
| status                    | ACTIVE                               |
| tags                      |                                      |
| tenant_id                 | ccb761aa90e244e68a2d7c59ff70e79f     |
| updated_at                | 2025-03-03T12:54:08Z                 |
+---------------------------+--------------------------------------+
```

- Y luego lo añadimos a la red externa `ext-net`

```bash
openstack router set RouterK8s --external-gateway ext-net 
```

- Y comprobamos que se haya creado correctamente

```bash
openstack router list --long

+--------------------------------------+------------------------+--------+-------+----------------------------------+--------+--------------------------------------+--------------------+------+
| ID                                   | Name                   | Status | State | Project                          | Routes | External gateway info                | Availability zones | Tags |
+--------------------------------------+------------------------+--------+-------+----------------------------------+--------+--------------------------------------+--------------------+------+
| 03c05c8d-9cb3-4984-948b-5af48a1d618c | Router de asangom1009b | ACTIVE | UP    | ccb761aa90e244e68a2d7c59ff70e79f |        | {"network_id": "98d3f685-e398-43fa-  |                    |      |
|                                      |                        |        |       |                                  |        | 812f-80c90371269d",                  |                    |      |
|                                      |                        |        |       |                                  |        | "external_fixed_ips": [{"subnet_id": |                    |      |
|                                      |                        |        |       |                                  |        | "ac19b62e-4454-467a-bfd3-            |                    |      |
|                                      |                        |        |       |                                  |        | 1fb19693cf4c", "ip_address":         |                    |      |
|                                      |                        |        |       |                                  |        | "172.22.200.92"}], "enable_snat":    |                    |      |
|                                      |                        |        |       |                                  |        | true}                                |                    |      |
| 1e340e3f-c7df-4dab-a445-1110a90a062e | RouterPractica         | ACTIVE | UP    | ccb761aa90e244e68a2d7c59ff70e79f |        | {"network_id": "98d3f685-e398-43fa-  |                    |      |
|                                      |                        |        |       |                                  |        | 812f-80c90371269d",                  |                    |      |
|                                      |                        |        |       |                                  |        | "external_fixed_ips": [{"subnet_id": |                    |      |
|                                      |                        |        |       |                                  |        | "ac19b62e-4454-467a-bfd3-            |                    |      |
|                                      |                        |        |       |                                  |        | 1fb19693cf4c", "ip_address":         |                    |      |
|                                      |                        |        |       |                                  |        | "172.22.201.38"}], "enable_snat":    |                    |      |
|                                      |                        |        |       |                                  |        | true}                                |                    |      |
| 200dfcac-8483-404d-a155-a784ea495ac2 | RouterK8s              | ACTIVE | UP    | ccb761aa90e244e68a2d7c59ff70e79f |        | {"network_id": "98d3f685-e398-43fa-  |                    |      |
|                                      |                        |        |       |                                  |        | 812f-80c90371269d",                  |                    |      |
|                                      |                        |        |       |                                  |        | "external_fixed_ips": [{"subnet_id": |                    |      |
|                                      |                        |        |       |                                  |        | "ac19b62e-4454-467a-bfd3-            |                    |      |
|                                      |                        |        |       |                                  |        | 1fb19693cf4c", "ip_address":         |                    |      |
|                                      |                        |        |       |                                  |        | "172.22.201.16"}], "enable_snat":    |                    |      |
|                                      |                        |        |       |                                  |        | true}                                |                    |      |
+--------------------------------------+------------------------+--------+-------+----------------------------------+--------+--------------------------------------+--------------------+------+
```

### Configurar red

A continuación configuraremos la red a la cual estarán conectados los nodos de k8s

```bash
openstack network create "Red K8s"

+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | UP                                   |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2025-03-03T12:54:58Z                 |
| description               |                                      |
| dns_domain                | None                                 |
| id                        | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff |
| ipv4_address_scope        | None                                 |
| ipv6_address_scope        | None                                 |
| is_default                | False                                |
| is_vlan_transparent       | None                                 |
| mtu                       | 1442                                 |
| name                      | Red K8s                              |
| port_security_enabled     | True                                 |
| project_id                | ccb761aa90e244e68a2d7c59ff70e79f     |
| provider:network_type     | None                                 |
| provider:physical_network | None                                 |
| provider:segmentation_id  | None                                 |
| qos_policy_id             | None                                 |
| revision_number           | 1                                    |
| router:external           | Internal                             |
| segments                  | None                                 |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tags                      |                                      |
| updated_at                | 2025-03-03T12:54:58Z                 |
+---------------------------+--------------------------------------+
```

- Y la subnet que tendra las siguientes características

```bash
openstack subnet create --network "Red K8s" --subnet-range 1.0.0.0/24 --gateway 1.0.0.1 --dns-nameserver 172.22.0.1 --no-dhcp "subnet_k8s"

+----------------------+--------------------------------------+
| Field                | Value                                |
+----------------------+--------------------------------------+
| allocation_pools     | 1.0.0.2-1.0.0.254                    |
| cidr                 | 1.0.0.0/24                           |
| created_at           | 2025-03-03T12:55:31Z                 |
| description          |                                      |
| dns_nameservers      | 172.22.0.1                           |
| dns_publish_fixed_ip | None                                 |
| enable_dhcp          | False                                |
| gateway_ip           | 1.0.0.1                              |
| host_routes          |                                      |
| id                   | 093dec09-9dda-41f8-ba6d-df037e410c50 |
| ip_version           | 4                                    |
| ipv6_address_mode    | None                                 |
| ipv6_ra_mode         | None                                 |
| name                 | subnet_k8s                           |
| network_id           | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff |
| project_id           | ccb761aa90e244e68a2d7c59ff70e79f     |
| revision_number      | 0                                    |
| segment_id           | None                                 |
| service_types        |                                      |
| subnetpool_id        | None                                 |
| tags                 |                                      |
| updated_at           | 2025-03-03T12:55:31Z                 |
+----------------------+--------------------------------------+
```

Y ahora la conectamos al router

```bash 
openstack router add subnet "RouterK8s" "subnet_k8s"
```

## Crear las máquinas 

Primero vamos a empezar creaando el `cloud-init.yaml` el cual vamos a usar para todas las máquias

- **NOTA:** Encriptamos la contraseña con el comando `openssl passwd -6`

```yml
# Creación de usuarios
users:
    # Usuario administrador
  - name: admin
    gecos: "Usuario Administrador"
    primary_group: admin
    groups: 
      - sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    lock_passwd: false
    passwd: $6$gxB69Eo6slEhM49k$55vXMTmw81VzVIMU.wXdwjS174Ku7sYSmjq1FrYI080Ov2jN/3C.wE25R0xLtsGej/XBa6C.ojeycelvx9qLM.
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCfqTox9jGgrUSaq0ra/oATsNLVzSh2ntOaK2khww5gKhLzCSwxFDtQgerew29i5CFZzmvZ27qz0ONI8Dzix45/c7IafIM+j+/AzPu824RpQO1iPaPFoJPEHFTxg+KnlrMBkpyCq8nS32JGjnxZjLSBan0hBy5v6WYkFBm/4uB5b6jxkFwm9ZipqhO0RTfxKJKgL7bxV15Und+CQFFXbrGKq3H+840tRsaTK1/pjEus8/HzrKm+kFmu5zfZxw7sTScnLDcro7ThArw3ZoHqGxuS+EjJy7c1gDHvBMGHp9DwlIxEL2JA0QPwAJ/MYtEshlT6Ng/LYCIokqYeWZJP+Lu4zpQe01ZtQdB+fU5bNJaICiM3PM7vJoz+HP164M544L9jQz4FMgHHWZaImdX31CgrW22meiyiRDt3TQewldNuA9GVNtCfbMZj7jLW9lF4RO00obML0wPF9THrbefG1ZmYdf3I0+JLvopA4jtrTbmGulUE7hLM1lTsrqYoKFS7edM= alex@Riolu


  # Usuario sin privilegios
  - name: alex
    gecos: "Usuario Estándar"
    primary_group: alex
    groups: 
      - users
    shell: /bin/bash
    lock_passwd: false
    passwd: $6$fW3zTERvSGCtV/Wf$oPteyz9PHk3IBnxvivWHx3KWH9EryaeJU5PkcB85NsnUmpFyA./P0ZmHevYi1xxu6IEB.s0.Tho8iBOrJ4WpM1
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCfqTox9jGgrUSaq0ra/oATsNLVzSh2ntOaK2khww5gKhLzCSwxFDtQgerew29i5CFZzmvZ27qz0ONI8Dzix45/c7IafIM+j+/AzPu824RpQO1iPaPFoJPEHFTxg+KnlrMBkpyCq8nS32JGjnxZjLSBan0hBy5v6WYkFBm/4uB5b6jxkFwm9ZipqhO0RTfxKJKgL7bxV15Und+CQFFXbrGKq3H+840tRsaTK1/pjEus8/HzrKm+kFmu5zfZxw7sTScnLDcro7ThArw3ZoHqGxuS+EjJy7c1gDHvBMGHp9DwlIxEL2JA0QPwAJ/MYtEshlT6Ng/LYCIokqYeWZJP+Lu4zpQe01ZtQdB+fU5bNJaICiM3PM7vJoz+HP164M544L9jQz4FMgHHWZaImdX31CgrW22meiyiRDt3TQewldNuA9GVNtCfbMZj7jLW9lF4RO00obML0wPF9THrbefG1ZmYdf3I0+JLvopA4jtrTbmGulUE7hLM1lTsrqYoKFS7edM= alex@Riolu

  # Usuario profesor
  - name: profesor
    gecos: "Usuario Profesor"
    primary_group: profesor
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    lock_passwd: false
    passwd: $6$03/N0AJ/Q27csLhQ$znYF59oDrj47vSn1xEAR9vODFmL58JRMqWp/cNB8Bu9Rf27hy6amM4qKBIb35m1hAlx/tet7Cp9xzG2byRQHc0
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDf9lnBH2nCT2ezpDZnqSBeDuSsVGGFD1Kzqa4KyIVkzkrD7pNHHkkpSuO4isKcCsUcopYOcA38QtG7wB0v/qn8Jsq731N8bjaKOdQN25vqLjwVj8DpYtvGc+ZA0uaChe7TS+QBzlMC9ypwj4wf15Q/z3v/ip4FF2cORT0cQC04cNRQDgUg4p1rlOs8+ma7OPh3P3UvzlPfLhi2H1yl+/mo4XLOcAMNr/jiZCwYxom6OEOYVBNk8MZX/Zn+qRi71D0RPiKg27AcXSD/FPWdQW9hBH1Zq5xGicUFS4C9yXvHKru7cMmmxV2G80p/ArRscKWq92UT5jIJQpccmHxsxdIi6o25LhcxH1dOnZy6kHcJ2yP24CnBHK5Y3SsovCD0Th6MN1VlTySbl8Ar0ypmY+GYO+oVd4bM3ioHzL0AMqYnS29m0UtEDvFEUUoSkOoLK4uSlcvej+OIVp7X5G7oZ56nZZf+qHEgodv++a6vPmhH2ZSgoOj1sE39DK7InuKSqCE= rafa@eco
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCmjoVIoZCx4QFXvljqozXGqxxlSvO7V2aizqyPgMfGqnyl0J9YXo6zrcWYwyWMnMdRdwYZgHqfiiFCUn2QDm6ZuzC4Lcx0K3ZwO2lgL4XaATykVLneHR1ib6RNroFcClN69cxWsdwQW6dpjpiBDXf8m6/qxVP3EHwUTsP8XaOV7WkcCAqfYAMvpWLISqYme6e+6ZGJUIPkDTxavu5JTagDLwY+py1WB53eoDWsG99gmvyit2O1Eo+jRWN+mgRHIxJTrFtLS6o4iWeshPZ6LvCZ/Pum12Oj4B4bjGSHzrKjHZgTwhVJ/LDq3v71/PP4zaI3gVB9ZalemSxqomgbTlnT jose@debian
```

Ahora crearemos las fixed ip para los cinco nodos

```bash
openstack port create \
  --network "Red K8s" \
  --fixed-ip subnet=$(openstack subnet list --network "Red K8s" -f value -c ID),ip-address=1.0.0.10 \
  "node-master1-port"
+-------------------------+-------------------------------------------------------------------------+
| Field                   | Value                                                                   |
+-------------------------+-------------------------------------------------------------------------+
| admin_state_up          | UP                                                                      |
| allowed_address_pairs   |                                                                         |
| binding_host_id         | None                                                                    |
| binding_profile         | None                                                                    |
| binding_vif_details     | None                                                                    |
| binding_vif_type        | None                                                                    |
| binding_vnic_type       | normal                                                                  |
| created_at              | 2025-03-03T13:02:54Z                                                    |
| data_plane_status       | None                                                                    |
| description             |                                                                         |
| device_id               |                                                                         |
| device_owner            |                                                                         |
| device_profile          | None                                                                    |
| dns_assignment          | None                                                                    |
| dns_domain              | None                                                                    |
| dns_name                | None                                                                    |
| extra_dhcp_opts         |                                                                         |
| fixed_ips               | ip_address='1.0.0.10', subnet_id='093dec09-9dda-41f8-ba6d-df037e410c50' |
| hardware_offload_type   | None                                                                    |
| hints                   |                                                                         |
| id                      | 13668bb4-0ebd-49f6-a8b8-f06f76a5515e                                    |
| ip_allocation           | None                                                                    |
| mac_address             | fa:16:3e:ee:05:c2                                                       |
| name                    | node-master1-port                                                       |
| network_id              | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff                                    |
| numa_affinity_policy    | None                                                                    |
| port_security_enabled   | True                                                                    |
| project_id              | ccb761aa90e244e68a2d7c59ff70e79f                                        |
| propagate_uplink_status | None                                                                    |
| resource_request        | None                                                                    |
| revision_number         | 1                                                                       |
| qos_network_policy_id   | None                                                                    |
| qos_policy_id           | None                                                                    |
| security_group_ids      | 493364d6-238e-4f66-95d7-51480e98347b                                    |
| status                  | DOWN                                                                    |
| tags                    |                                                                         |
| trunk_details           | None                                                                    |
| updated_at              | 2025-03-03T13:02:54Z                                                    |
+-------------------------+-------------------------------------------------------------------------+
```

```bash
openstack port create \
  --network "Red K8s" \
  --fixed-ip subnet=$(openstack subnet list --network "Red K8s" -f value -c ID),ip-address=1.0.0.11 \
  "node-master2-port"
+-------------------------+-------------------------------------------------------------------------+
| Field                   | Value                                                                   |
+-------------------------+-------------------------------------------------------------------------+
| admin_state_up          | UP                                                                      |
| allowed_address_pairs   |                                                                         |
| binding_host_id         | None                                                                    |
| binding_profile         | None                                                                    |
| binding_vif_details     | None                                                                    |
| binding_vif_type        | None                                                                    |
| binding_vnic_type       | normal                                                                  |
| created_at              | 2025-03-03T16:05:44Z                                                    |
| data_plane_status       | None                                                                    |
| description             |                                                                         |
| device_id               |                                                                         |
| device_owner            |                                                                         |
| device_profile          | None                                                                    |
| dns_assignment          | None                                                                    |
| dns_domain              | None                                                                    |
| dns_name                | None                                                                    |
| extra_dhcp_opts         |                                                                         |
| fixed_ips               | ip_address='1.0.0.11', subnet_id='093dec09-9dda-41f8-ba6d-df037e410c50' |
| hardware_offload_type   | None                                                                    |
| hints                   |                                                                         |
| id                      | effe6a72-2caa-4d56-a106-b928a55d4f94                                    |
| ip_allocation           | None                                                                    |
| mac_address             | fa:16:3e:ed:14:5d                                                       |
| name                    | node-master2-port                                                       |
| network_id              | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff                                    |
| numa_affinity_policy    | None                                                                    |
| port_security_enabled   | True                                                                    |
| project_id              | ccb761aa90e244e68a2d7c59ff70e79f                                        |
| propagate_uplink_status | None                                                                    |
| resource_request        | None                                                                    |
| revision_number         | 1                                                                       |
| qos_network_policy_id   | None                                                                    |
| qos_policy_id           | None                                                                    |
| security_group_ids      | 493364d6-238e-4f66-95d7-51480e98347b                                    |
| status                  | DOWN                                                                    |
| tags                    |                                                                         |
| trunk_details           | None                                                                    |
| updated_at              | 2025-03-03T16:05:44Z                                                    |
+-------------------------+-------------------------------------------------------------------------+
```

```bash
openstack port create \
  --network "Red K8s" \
  --fixed-ip subnet=$(openstack subnet list --network "Red K8s" -f value -c ID),ip-address=1.0.0.13 \
  "node-master3-port"

+-------------------------+-------------------------------------------------------------------------+
| Field                   | Value                                                                   |
+-------------------------+-------------------------------------------------------------------------+
| admin_state_up          | UP                                                                      |
| allowed_address_pairs   |                                                                         |
| binding_host_id         | None                                                                    |
| binding_profile         | None                                                                    |
| binding_vif_details     | None                                                                    |
| binding_vif_type        | None                                                                    |
| binding_vnic_type       | normal                                                                  |
| created_at              | 2025-03-03T17:00:11Z                                                    |
| data_plane_status       | None                                                                    |
| description             |                                                                         |
| device_id               |                                                                         |
| device_owner            |                                                                         |
| device_profile          | None                                                                    |
| dns_assignment          | None                                                                    |
| dns_domain              | None                                                                    |
| dns_name                | None                                                                    |
| extra_dhcp_opts         |                                                                         |
| fixed_ips               | ip_address='1.0.0.13', subnet_id='093dec09-9dda-41f8-ba6d-df037e410c50' |
| hardware_offload_type   | None                                                                    |
| hints                   |                                                                         |
| id                      | 04b12f12-0cc3-49a0-84b4-6bba6dcbdcbd                                    |
| ip_allocation           | None                                                                    |
| mac_address             | fa:16:3e:ea:56:0a                                                       |
| name                    | node-master3-port                                                       |
| network_id              | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff                                    |
| numa_affinity_policy    | None                                                                    |
| port_security_enabled   | True                                                                    |
| project_id              | ccb761aa90e244e68a2d7c59ff70e79f                                        |
| propagate_uplink_status | None                                                                    |
| resource_request        | None                                                                    |
| revision_number         | 1                                                                       |
| qos_network_policy_id   | None                                                                    |
| qos_policy_id           | None                                                                    |
| security_group_ids      | 493364d6-238e-4f66-95d7-51480e98347b                                    |
| status                  | DOWN                                                                    |
| tags                    |                                                                         |
| trunk_details           | None                                                                    |
| updated_at              | 2025-03-03T17:00:12Z                                                    |
+-------------------------+-------------------------------------------------------------------------+
```


```bash
openstack port create \
  --network "Red K8s" \
  --fixed-ip subnet=$(openstack subnet list --network "Red K8s" -f value -c ID),ip-address=1.0.0.100 \
  "node-worker1-port"
+-------------------------+--------------------------------------------------------------------------+
| Field                   | Value                                                                    |
+-------------------------+--------------------------------------------------------------------------+
| admin_state_up          | UP                                                                       |
| allowed_address_pairs   |                                                                          |
| binding_host_id         | None                                                                     |
| binding_profile         | None                                                                     |
| binding_vif_details     | None                                                                     |
| binding_vif_type        | None                                                                     |
| binding_vnic_type       | normal                                                                   |
| created_at              | 2025-03-03T16:18:00Z                                                     |
| data_plane_status       | None                                                                     |
| description             |                                                                          |
| device_id               |                                                                          |
| device_owner            |                                                                          |
| device_profile          | None                                                                     |
| dns_assignment          | None                                                                     |
| dns_domain              | None                                                                     |
| dns_name                | None                                                                     |
| extra_dhcp_opts         |                                                                          |
| fixed_ips               | ip_address='1.0.0.100', subnet_id='093dec09-9dda-41f8-ba6d-df037e410c50' |
| hardware_offload_type   | None                                                                     |
| hints                   |                                                                          |
| id                      | 2e85664b-f8f2-4699-985d-bf61ce95f2da                                     |
| ip_allocation           | None                                                                     |
| mac_address             | fa:16:3e:f6:68:9a                                                        |
| name                    | node-worker1-port                                                        |
| network_id              | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff                                     |
| numa_affinity_policy    | None                                                                     |
| port_security_enabled   | True                                                                     |
| project_id              | ccb761aa90e244e68a2d7c59ff70e79f                                         |
| propagate_uplink_status | None                                                                     |
| resource_request        | None                                                                     |
| revision_number         | 1                                                                        |
| qos_network_policy_id   | None                                                                     |
| qos_policy_id           | None                                                                     |
| security_group_ids      | 493364d6-238e-4f66-95d7-51480e98347b                                     |
| status                  | DOWN                                                                     |
| tags                    |                                                                          |
| trunk_details           | None                                                                     |
| updated_at              | 2025-03-03T16:18:00Z                                                     |
+-------------------------+--------------------------------------------------------------------------+
```

```bash
openstack port create \
  --network "Red K8s" \
  --fixed-ip subnet=$(openstack subnet list --network "Red K8s" -f value -c ID),ip-address=1.0.0.101 \
  "node-worker2-port"
+-------------------------+--------------------------------------------------------------------------+
| Field                   | Value                                                                    |
+-------------------------+--------------------------------------------------------------------------+
| admin_state_up          | UP                                                                       |
| allowed_address_pairs   |                                                                          |
| binding_host_id         | None                                                                     |
| binding_profile         | None                                                                     |
| binding_vif_details     | None                                                                     |
| binding_vif_type        | None                                                                     |
| binding_vnic_type       | normal                                                                   |
| created_at              | 2025-03-03T16:18:34Z                                                     |
| data_plane_status       | None                                                                     |
| description             |                                                                          |
| device_id               |                                                                          |
| device_owner            |                                                                          |
| device_profile          | None                                                                     |
| dns_assignment          | None                                                                     |
| dns_domain              | None                                                                     |
| dns_name                | None                                                                     |
| extra_dhcp_opts         |                                                                          |
| fixed_ips               | ip_address='1.0.0.101', subnet_id='093dec09-9dda-41f8-ba6d-df037e410c50' |
| hardware_offload_type   | None                                                                     |
| hints                   |                                                                          |
| id                      | a3d41978-9802-415c-9667-0b253c509a3e                                     |
| ip_allocation           | None                                                                     |
| mac_address             | fa:16:3e:c6:a6:48                                                        |
| name                    | node-worker2-port                                                        |
| network_id              | e0f85ecb-4c52-48b5-80d3-0fe85dbd1aff                                     |
| numa_affinity_policy    | None                                                                     |
| port_security_enabled   | True                                                                     |
| project_id              | ccb761aa90e244e68a2d7c59ff70e79f                                         |
| propagate_uplink_status | None                                                                     |
| resource_request        | None                                                                     |
| revision_number         | 1                                                                        |
| qos_network_policy_id   | None                                                                     |
| qos_policy_id           | None                                                                     |
| security_group_ids      | 493364d6-238e-4f66-95d7-51480e98347b                                     |
| status                  | DOWN                                                                     |
| tags                    |                                                                          |
| trunk_details           | None                                                                     |
| updated_at              | 2025-03-03T16:18:34Z                                                     |
+-------------------------+--------------------------------------------------------------------------+
```

Antes de crear las maquinas tambien generaremos los volumenes para estas, primero vemos el id de debian

```bash
openstack image list   
+--------------------------------------+---------------------------------+--------+
| ID                                   | Name                            | Status |
+--------------------------------------+---------------------------------+--------+
| e7caa035-5bb0-47fb-b9e8-ef73dce54499 | Debian 12 Bookworm              | active |
| 1a39c3de-cf79-4d17-a470-a490ba89b366 | Fedora-Cloud-Base-37-1.7.x86_64 | active |
| 52f6e1bd-edc0-49e8-87e2-c99a3f173d41 | Rocky Linux 9                   | active |
| d57c4744-177a-4f53-8db9-6368dc17f979 | Ubuntu 22.04 LTS                | active |
| e2cfdd56-7474-4772-a43c-ad0429f4b78e | Ubuntu 24.04 LTS                | active |
| ffebc834-5c2f-4319-b5b8-a0c360050d4f | cirros-0.6.2-x86_64-disk        | active |
| ebf92cf2-ffec-4cf8-90e8-99c35b3fdd46 | exaiso                          | active |
| 915fa15f-b924-44e0-8130-0a82b50a79c1 | openSUSE MicroOS                | active |
+--------------------------------------+---------------------------------+--------+

```

Y creamos los cinco volumenes

```bash
openstack volume create --size 20 --image e7caa035-5bb0-47fb-b9e8-ef73dce54499 --bootable node-master1-volume                             

+---------------------+------------------------------------------------------------------+
| Field               | Value                                                            |
+---------------------+------------------------------------------------------------------+
| attachments         | []                                                               |
| availability_zone   | nova                                                             |
| bootable            | false                                                            |
| consistencygroup_id | None                                                             |
| created_at          | 2025-03-03T11:28:09.611973                                       |
| description         | None                                                             |
| encrypted           | False                                                            |
| id                  | f623cb89-ac2d-433d-8844-5a96f1f78342                             |
| multiattach         | False                                                            |
| name                | node-master1-volume                                              |
| properties          |                                                                  |
| replication_status  | None                                                             |
| size                | 20                                                               |
| snapshot_id         | None                                                             |
| source_volid        | None                                                             |
| status              | creating                                                         |
| type                | lvmdriver-1                                                      |
| updated_at          | None                                                             |
| user_id             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3 |
+---------------------+------------------------------------------------------------------+
```

```bash
openstack volume create --size 20 --image e7caa035-5bb0-47fb-b9e8-ef73dce54499 --bootable node-master2-volume

+---------------------+------------------------------------------------------------------+
| Field               | Value                                                            |
+---------------------+------------------------------------------------------------------+
| attachments         | []                                                               |
| availability_zone   | nova                                                             |
| bootable            | false                                                            |
| consistencygroup_id | None                                                             |
| created_at          | 2025-03-03T10:01:32.863798                                       |
| description         | None                                                             |
| encrypted           | False                                                            |
| id                  | f1c35e81-0b1c-4ba4-9d8a-5790141a1e67                             |
| multiattach         | False                                                            |
| name                | node-master2-volume                                              |
| properties          |                                                                  |
| replication_status  | None                                                             |
| size                | 20                                                               |
| snapshot_id         | None                                                             |
| source_volid        | None                                                             |
| status              | creating                                                         |
| type                | lvmdriver-1                                                      |
| updated_at          | None                                                             |
| user_id             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3 |
+---------------------+------------------------------------------------------------------+
```

```bash
openstack volume create --size 20 --image e7caa035-5bb0-47fb-b9e8-ef73dce54499 --bootable node-master3-volume

+---------------------+------------------------------------------------------------------+
| Field               | Value                                                            |
+---------------------+------------------------------------------------------------------+
| attachments         | []                                                               |
| availability_zone   | nova                                                             |
| bootable            | false                                                            |
| consistencygroup_id | None                                                             |
| created_at          | 2025-03-03T17:07:12.641483                                       |
| description         | None                                                             |
| encrypted           | False                                                            |
| id                  | eaba0db7-0e3b-4ad7-b65b-a180e7fbc88c                             |
| multiattach         | False                                                            |
| name                | node-master3-volume                                              |
| properties          |                                                                  |
| replication_status  | None                                                             |
| size                | 20                                                               |
| snapshot_id         | None                                                             |
| source_volid        | None                                                             |
| status              | creating                                                         |
| type                | lvmdriver-1                                                      |
| updated_at          | None                                                             |
| user_id             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3 |
+---------------------+------------------------------------------------------------------+
```

```bash
openstack volume create --size 20 --image e7caa035-5bb0-47fb-b9e8-ef73dce54499 --bootable node-worker1-volume

+---------------------+------------------------------------------------------------------+
| Field               | Value                                                            |
+---------------------+------------------------------------------------------------------+
| attachments         | []                                                               |
| availability_zone   | nova                                                             |
| bootable            | false                                                            |
| consistencygroup_id | None                                                             |
| created_at          | 2025-03-03T10:03:44.668277                                       |
| description         | None                                                             |
| encrypted           | False                                                            |
| id                  | fc12ebb1-4b7a-402a-9893-1690fc2a4cd8                             |
| multiattach         | False                                                            |
| name                | node-worker1-volume                                              |
| properties          |                                                                  |
| replication_status  | None                                                             |
| size                | 20                                                               |
| snapshot_id         | None                                                             |
| source_volid        | None                                                             |
| status              | creating                                                         |
| type                | lvmdriver-1                                                      |
| updated_at          | None                                                             |
| user_id             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3 |
+---------------------+------------------------------------------------------------------+
```

```bash
openstack volume create --size 20 --image e7caa035-5bb0-47fb-b9e8-ef73dce54499 --bootable node-worker2-volume

+---------------------+------------------------------------------------------------------+
| Field               | Value                                                            |
+---------------------+------------------------------------------------------------------+
| attachments         | []                                                               |
| availability_zone   | nova                                                             |
| bootable            | false                                                            |
| consistencygroup_id | None                                                             |
| created_at          | 2025-03-03T10:04:06.052880                                       |
| description         | None                                                             |
| encrypted           | False                                                            |
| id                  | 343aaf6e-8db5-4673-94e3-c10f4fd5955a                             |
| multiattach         | False                                                            |
| name                | node-worker2-volume                                              |
| properties          |                                                                  |
| replication_status  | None                                                             |
| size                | 20                                                               |
| snapshot_id         | None                                                             |
| source_volid        | None                                                             |
| status              | creating                                                         |
| type                | lvmdriver-1                                                      |
| updated_at          | None                                                             |
| user_id             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3 |
+---------------------+------------------------------------------------------------------+
```



A continuación vamos a generar las cinco máquinas usando el fichero `cloud-init.yml`

```bash
openstack server create \                                                                                    
  --flavor vol.large \
  --key-name Alex \
  --network "Red Intra de asangom1009b" \
  --volume "node-master1-volume" \
  --user-data r_cloud-init.yml \
  "Master1"
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                               | Value                                                                                                                                                                     |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                                                                                                                                                    |
| OS-EXT-AZ:availability_zone         | nova                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:host                | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:hostname            | master1                                                                                                                                                                   |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:instance_name       | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:kernel_id           | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:launch_index        | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:ramdisk_id          | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:reservation_id      | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:root_device_name    | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:user_data           | I2Nsb3VkLWNvbmZpZwpwYWNrYWdlX3VwZGF0ZTogdHJ1ZQoKIyBDb25maWd1cmFjacOzbiBkZSBkb21pbmlvIHkgaG9zdG5hbWUKaG9zdG5hbWU6IG1hc3RlcjEKCiMgQ3JlYWNpw7NuIGRlIHVzdWFyaW9zCnVzZXJzOgogI |
|                                     | CMgVXN1YXJpbyBhZG1pbmlzdHJhZG9yCiAgLSBuYW1lOiBhZG1pbgogICAgZ2Vjb3M6ICJVc3VhcmlvIEFkbWluaXN0cmFkb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBhZG1pbgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KIC |
|                                     | AgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZ3hCNjlFbzZzbEVoTTQ5ayQ1NXZYTVRtdzgxVnpWSU1 |
|                                     | VLndYZHdqUzE3NEt1N3NZU21qcTFGcllJMDgwT3Yyak4vM0Mud0UyNVIweEx0c0dlai9YQmE2Qy5vamV5Y2Vsdng5cUxNLgogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMy |
|                                     | RUFBQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpU |
|                                     | GFQRm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSH |
|                                     | pyS20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2l |
|                                     | NM1BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1 |
|                                     | bFVFN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gc2luIHByaXZpbGVnaW9zCiAgLSBuYW1lOiBhbGV4CiAgICBnZWNvczogIlVzdWFyaW8gRXN0w6FuZGFyIgogICAgcHJpbWFyeV9ncm91c |
|                                     | DogYWxleAogICAgZ3JvdXBzOgogICAgICAtIHVzZXJzCiAgICBzaGVsbDogL2Jpbi9iYXNoCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZXlrRnh4MHV4ZklCOUp3QyRqdE1UVWl2TTZYZjVzSVpEdn |
|                                     | JGdVhQd2ZjeG1ZNTZOaXJrQ1h3dklEZWhZWEtUa0lVM2pMdlBHYjJvUEhkMnBaUmUwR0NSb1ZBaEJoSjh4cThKRi9vLwogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUF |
|                                     | BQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpUGFQ |
|                                     | Rm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSHpyS |
|                                     | 20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2lNM1 |
|                                     | BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1bFV |
|                                     | FN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gcHJvZmVzb3IKICAtIG5hbWU6IHByb2Zlc29yCiAgICBnZWNvczogIlVzdWFyaW8gUHJvZmVzb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBwcm9m |
|                                     | ZXNvcgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KICAgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkM |
|                                     | DMvTjBBSi9RMjdjc0xoUSR6bllGNTlvRHJqNDd2U24xeEVBUjl2T0RGbUw1OEpSTXFXcC9jTkI4QnU5UmYyN2h5NmFtTTRxS0JJYjM1bTFoQWx4L3RldDdDcDl4ekcyYnlSUUhjMAogICAgc3NoX2F1dGhvcml6ZWRfa2V5cz |
|                                     | oKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJnUURmOWxuQkgybkNUMmV6cERabnFTQmVEdVNzVkdHRkQxS3pxYTRLeUlWa3prckQ3cE5ISGtrcFN1TzRpc0tjQ3NVY29wWU9jQTM4UXRHN3d |
|                                     | CMHYvcW44SnNxNzMxTjhiamFLT2RRTjI1dnFMandWajhEcFl0dkdjK1pBMHVhQ2hlN1RTK1FCemxNQzl5cHdqNHdmMTVRL3ozdi9pcDRGRjJjT1JUMGNRQzA0Y05SUURnVWc0cDFybE9zOCttYTdPUGgzUDNVdnpsUGZMaGky |
|                                     | SDF5bCsvbW80WExPY0FNTnIvamlaQ3dZeG9tNk9FT1lWQk5rOE1aWC9abitxUmk3MUQwUlBpS2cyN0FjWFNEL0ZQV2RRVzloQkgxWnE1eEdpY1VGUzRDOXlYdkhLcnU3Y01tbXhWMkc4MHAvQXJSc2NLV3E5MlVUNWpJSlFwY |
|                                     | 2NtSHhzeGRJaTZvMjVMaGN4SDFkT25aeTZrSGNKMnlQMjRDbkJISzVZM1Nzb3ZDRDBUaDZNTjFWbFR5U2JsOEFyMHlwbVkrR1lPK29WZDRiTTNpb0h6TDBBTXFZblMyOW0wVXRFRHZGRVVVb1NrT29MSzR1U2xjdmVqK09JVn |
|                                     | A3WDVHN29aNTZuWlpmK3FIRWdvZHYrK2E2dlBtaEgyWlNnb09qMXNFMzlESzdJbnVLU3FDRT0gcmFmYUBlY28KICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJBUUNtam9WSW9aQ3g0UUZYdmx |
|                                     | qcW96WEdxeHhsU3ZPN1YyYWl6cXlQZ01mR3FueWwwSjlZWG82enJjV1l3eVdNbk1kUmR3WVpnSHFmaWlGQ1VuMlFEbTZadXpDNExjeDBLM1p3TzJsZ0w0WGFBVHlrVkxuZUhSMWliNlJOcm9GY0NsTjY5Y3hXc2R3UVc2ZHBq |
|                                     | cGlCRFhmOG02L3F4VlAzRUh3VVRzUDhYYU9WN1drY0NBcWZZQU12cFdMSVNxWW1lNmUrNlpHSlVJUGtEVHhhdnU1SlRhZ0RMd1krcHkxV0I1M2VvRFdzRzk5Z212eWl0Mk8xRW8ralJXTittZ1JISXhKVHJGdExTNm80aVdlc |
|                                     | 2hQWjZMdkNaL1B1bTEyT2o0QjRiakdTSHpyS2pIWmdUd2hWSi9MRHEzdjcxL1BQNHphSTNnVkI5WmFsZW1TeHFvbWdiVGxuVCBqb3NlQGRlYmlhbgoKIyBDYW1iaW8gZGUgY29udHJhc2XDsWEgZGVsIHVzdWFyaW8gcm9vdA |
|                                     | pjaHBhc3N3ZDoKICBsaXN0OiB8CiAgICByb290OnJvb3QKICBleHBpcmU6IGZhbHNl                                                                                                        |
| OS-EXT-STS:power_state              | N/A                                                                                                                                                                       |
| OS-EXT-STS:task_state               | scheduling                                                                                                                                                                |
| OS-EXT-STS:vm_state                 | building                                                                                                                                                                  |
| OS-SRV-USG:launched_at              | None                                                                                                                                                                      |
| OS-SRV-USG:terminated_at            | None                                                                                                                                                                      |
| accessIPv4                          | None                                                                                                                                                                      |
| accessIPv6                          | None                                                                                                                                                                      |
| addresses                           | N/A                                                                                                                                                                       |
| adminPass                           | mHdZGRMCZd6i                                                                                                                                                              |
| config_drive                        | None                                                                                                                                                                      |
| created                             | 2025-03-03T12:56:28Z                                                                                                                                                      |
| description                         | None                                                                                                                                                                      |
| flavor                              | description=, disk='0', ephemeral='0', , id='vol.large', is_disabled=, is_public='True', location=, name='vol.large', original_name='vol.large', ram='4096',              |
|                                     | rxtx_factor=, swap='0', vcpus='2'                                                                                                                                         |
| hostId                              | None                                                                                                                                                                      |
| host_status                         | None                                                                                                                                                                      |
| id                                  | efa767da-018d-48f3-b133-1da3e87908eb                                                                                                                                      |
| image                               | N/A (booted from volume)                                                                                                                                                  |
| key_name                            | Alex                                                                                                                                                                      |
| locked                              | None                                                                                                                                                                      |
| locked_reason                       | None                                                                                                                                                                      |
| name                                | Master1                                                                                                                                                                   |
| pinned_availability_zone            | None                                                                                                                                                                      |
| progress                            | None                                                                                                                                                                      |
| project_id                          | ccb761aa90e244e68a2d7c59ff70e79f                                                                                                                                          |
| properties                          | None                                                                                                                                                                      |
| security_groups                     | name='default'                                                                                                                                                            |
| server_groups                       | None                                                                                                                                                                      |
| status                              | BUILD                                                                                                                                                                     |
| tags                                |                                                                                                                                                                           |
| trusted_image_certificates          | None                                                                                                                                                                      |
| updated                             | 2025-03-03T12:56:29Z                                                                                                                                                      |
| user_id                             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3                                                                                                          |
| volumes_attached                    | delete_on_termination='False', id='f623cb89-ac2d-433d-8844-5a96f1f78342'                                                                                                  |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

Debemos instalar el siguiente paquete

```bash
sudo apt update
sudo apt install openvswitch-switch -y
```

Le asignamos la ip de forma estática

```yml
network:
    version: 2
    ethernets:
      ens3:
        dhcp4: false  # Desactiva DHCP
        mtu: 1442
        addresses:
          - 1.0.0.10/24  # IP estática
        routes:
          - to: default
            via: 1.0.0.1
        nameservers:
          addresses:
            - 172.22.0.1  # Servidor DNS
```

Ahora la quitamos de ese puerto que es para que pille el apt update y lo conectamos al port

```bash
openstack server remove network Master1 "Red Intra de asangom1009b"
openstack server add port "Master1" "node-master1-port"
```

```bash
openstack server create \
  --flavor vol.large \
  --key-name Alex \
  --network "Red Intra de asangom1009b" \
  --volume "node-master2-volume" \
  --user-data r_cloud-init.yml \
  "Master2"
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                               | Value                                                                                                                                                                     |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                                                                                                                                                    |
| OS-EXT-AZ:availability_zone         | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:host                | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:hostname            | master2                                                                                                                                                                   |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:instance_name       | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:kernel_id           | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:launch_index        | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:ramdisk_id          | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:reservation_id      | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:root_device_name    | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:user_data           | I2Nsb3VkLWNvbmZpZwpwYWNrYWdlX3VwZGF0ZTogdHJ1ZQoKIyBDb25maWd1cmFjacOzbiBkZSBkb21pbmlvIHkgaG9zdG5hbWUKaG9zdG5hbWU6IG1hc3RlcjEKCiMgQ3JlYWNpw7NuIGRlIHVzdWFyaW9zCnVzZXJzOgogI |
|                                     | CMgVXN1YXJpbyBhZG1pbmlzdHJhZG9yCiAgLSBuYW1lOiBhZG1pbgogICAgZ2Vjb3M6ICJVc3VhcmlvIEFkbWluaXN0cmFkb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBhZG1pbgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KIC |
|                                     | AgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZ3hCNjlFbzZzbEVoTTQ5ayQ1NXZYTVRtdzgxVnpWSU1 |
|                                     | VLndYZHdqUzE3NEt1N3NZU21qcTFGcllJMDgwT3Yyak4vM0Mud0UyNVIweEx0c0dlai9YQmE2Qy5vamV5Y2Vsdng5cUxNLgogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMy |
|                                     | RUFBQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpU |
|                                     | GFQRm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSH |
|                                     | pyS20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2l |
|                                     | NM1BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1 |
|                                     | bFVFN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gc2luIHByaXZpbGVnaW9zCiAgLSBuYW1lOiBhbGV4CiAgICBnZWNvczogIlVzdWFyaW8gRXN0w6FuZGFyIgogICAgcHJpbWFyeV9ncm91c |
|                                     | DogYWxleAogICAgZ3JvdXBzOgogICAgICAtIHVzZXJzCiAgICBzaGVsbDogL2Jpbi9iYXNoCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZXlrRnh4MHV4ZklCOUp3QyRqdE1UVWl2TTZYZjVzSVpEdn |
|                                     | JGdVhQd2ZjeG1ZNTZOaXJrQ1h3dklEZWhZWEtUa0lVM2pMdlBHYjJvUEhkMnBaUmUwR0NSb1ZBaEJoSjh4cThKRi9vLwogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUF |
|                                     | BQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpUGFQ |
|                                     | Rm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSHpyS |
|                                     | 20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2lNM1 |
|                                     | BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1bFV |
|                                     | FN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gcHJvZmVzb3IKICAtIG5hbWU6IHByb2Zlc29yCiAgICBnZWNvczogIlVzdWFyaW8gUHJvZmVzb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBwcm9m |
|                                     | ZXNvcgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KICAgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkM |
|                                     | DMvTjBBSi9RMjdjc0xoUSR6bllGNTlvRHJqNDd2U24xeEVBUjl2T0RGbUw1OEpSTXFXcC9jTkI4QnU5UmYyN2h5NmFtTTRxS0JJYjM1bTFoQWx4L3RldDdDcDl4ekcyYnlSUUhjMAogICAgc3NoX2F1dGhvcml6ZWRfa2V5cz |
|                                     | oKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJnUURmOWxuQkgybkNUMmV6cERabnFTQmVEdVNzVkdHRkQxS3pxYTRLeUlWa3prckQ3cE5ISGtrcFN1TzRpc0tjQ3NVY29wWU9jQTM4UXRHN3d |
|                                     | CMHYvcW44SnNxNzMxTjhiamFLT2RRTjI1dnFMandWajhEcFl0dkdjK1pBMHVhQ2hlN1RTK1FCemxNQzl5cHdqNHdmMTVRL3ozdi9pcDRGRjJjT1JUMGNRQzA0Y05SUURnVWc0cDFybE9zOCttYTdPUGgzUDNVdnpsUGZMaGky |
|                                     | SDF5bCsvbW80WExPY0FNTnIvamlaQ3dZeG9tNk9FT1lWQk5rOE1aWC9abitxUmk3MUQwUlBpS2cyN0FjWFNEL0ZQV2RRVzloQkgxWnE1eEdpY1VGUzRDOXlYdkhLcnU3Y01tbXhWMkc4MHAvQXJSc2NLV3E5MlVUNWpJSlFwY |
|                                     | 2NtSHhzeGRJaTZvMjVMaGN4SDFkT25aeTZrSGNKMnlQMjRDbkJISzVZM1Nzb3ZDRDBUaDZNTjFWbFR5U2JsOEFyMHlwbVkrR1lPK29WZDRiTTNpb0h6TDBBTXFZblMyOW0wVXRFRHZGRVVVb1NrT29MSzR1U2xjdmVqK09JVn |
|                                     | A3WDVHN29aNTZuWlpmK3FIRWdvZHYrK2E2dlBtaEgyWlNnb09qMXNFMzlESzdJbnVLU3FDRT0gcmFmYUBlY28KICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJBUUNtam9WSW9aQ3g0UUZYdmx |
|                                     | qcW96WEdxeHhsU3ZPN1YyYWl6cXlQZ01mR3FueWwwSjlZWG82enJjV1l3eVdNbk1kUmR3WVpnSHFmaWlGQ1VuMlFEbTZadXpDNExjeDBLM1p3TzJsZ0w0WGFBVHlrVkxuZUhSMWliNlJOcm9GY0NsTjY5Y3hXc2R3UVc2ZHBq |
|                                     | cGlCRFhmOG02L3F4VlAzRUh3VVRzUDhYYU9WN1drY0NBcWZZQU12cFdMSVNxWW1lNmUrNlpHSlVJUGtEVHhhdnU1SlRhZ0RMd1krcHkxV0I1M2VvRFdzRzk5Z212eWl0Mk8xRW8ralJXTittZ1JISXhKVHJGdExTNm80aVdlc |
|                                     | 2hQWjZMdkNaL1B1bTEyT2o0QjRiakdTSHpyS2pIWmdUd2hWSi9MRHEzdjcxL1BQNHphSTNnVkI5WmFsZW1TeHFvbWdiVGxuVCBqb3NlQGRlYmlhbgoKIyBDYW1iaW8gZGUgY29udHJhc2XDsWEgZGVsIHVzdWFyaW8gcm9vdA |
|                                     | pjaHBhc3N3ZDoKICBsaXN0OiB8CiAgICByb290OnJvb3QKICBleHBpcmU6IGZhbHNl                                                                                                        |
| OS-EXT-STS:power_state              | N/A                                                                                                                                                                       |
| OS-EXT-STS:task_state               | scheduling                                                                                                                                                                |
| OS-EXT-STS:vm_state                 | building                                                                                                                                                                  |
| OS-SRV-USG:launched_at              | None                                                                                                                                                                      |
| OS-SRV-USG:terminated_at            | None                                                                                                                                                                      |
| accessIPv4                          | None                                                                                                                                                                      |
| accessIPv6                          | None                                                                                                                                                                      |
| addresses                           | N/A                                                                                                                                                                       |
| adminPass                           | qiH3cBu4fTSv                                                                                                                                                              |
| config_drive                        | None                                                                                                                                                                      |
| created                             | 2025-03-03T16:07:50Z                                                                                                                                                      |
| description                         | None                                                                                                                                                                      |
| flavor                              | description=, disk='0', ephemeral='0', , id='vol.large', is_disabled=, is_public='True', location=, name='vol.large', original_name='vol.large', ram='4096',              |
|                                     | rxtx_factor=, swap='0', vcpus='2'                                                                                                                                         |
| hostId                              | None                                                                                                                                                                      |
| host_status                         | None                                                                                                                                                                      |
| id                                  | 53327f8c-cfbd-487b-b36b-07fc82e80ea9                                                                                                                                      |
| image                               | N/A (booted from volume)                                                                                                                                                  |
| key_name                            | Alex                                                                                                                                                                      |
| locked                              | None                                                                                                                                                                      |
| locked_reason                       | None                                                                                                                                                                      |
| name                                | Master2                                                                                                                                                                   |
| pinned_availability_zone            | None                                                                                                                                                                      |
| progress                            | None                                                                                                                                                                      |
| project_id                          | ccb761aa90e244e68a2d7c59ff70e79f                                                                                                                                          |
| properties                          | None                                                                                                                                                                      |
| security_groups                     | name='default'                                                                                                                                                            |
| server_groups                       | None                                                                                                                                                                      |
| status                              | BUILD                                                                                                                                                                     |
| tags                                |                                                                                                                                                                           |
| trusted_image_certificates          | None                                                                                                                                                                      |
| updated                             | 2025-03-03T16:07:50Z                                                                                                                                                      |
| user_id                             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3                                                                                                          |
| volumes_attached                    |                                                                                                                                                                           |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

Hacemos lo mismo que en el 1 y tambien haremos lo mismo con las tres siguientes

```bash
openstack server create \
  --flavor vol.large \
  --key-name Alex \
  --network "Red Intra de asangom1009b" \
  --volume "node-master3-volume" \
  --user-data r_cloud-init.yml \
  "Master3"
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                               | Value                                                                                                                                                                     |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                                                                                                                                                    |
| OS-EXT-AZ:availability_zone         | nova                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:host                | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:hostname            | master3                                                                                                                                                                   |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:instance_name       | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:kernel_id           | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:launch_index        | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:ramdisk_id          | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:reservation_id      | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:root_device_name    | None                                                                                                                                                                      |
| OS-EXT-SRV-ATTR:user_data           | I2Nsb3VkLWNvbmZpZwpwYWNrYWdlX3VwZGF0ZTogdHJ1ZQoKIyBDb25maWd1cmFjacOzbiBkZSBkb21pbmlvIHkgaG9zdG5hbWUKaG9zdG5hbWU6IG1hc3RlcjEKCiMgQ3JlYWNpw7NuIGRlIHVzdWFyaW9zCnVzZXJzOgogI |
|                                     | CMgVXN1YXJpbyBhZG1pbmlzdHJhZG9yCiAgLSBuYW1lOiBhZG1pbgogICAgZ2Vjb3M6ICJVc3VhcmlvIEFkbWluaXN0cmFkb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBhZG1pbgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KIC |
|                                     | AgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZ3hCNjlFbzZzbEVoTTQ5ayQ1NXZYTVRtdzgxVnpWSU1 |
|                                     | VLndYZHdqUzE3NEt1N3NZU21qcTFGcllJMDgwT3Yyak4vM0Mud0UyNVIweEx0c0dlai9YQmE2Qy5vamV5Y2Vsdng5cUxNLgogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMy |
|                                     | RUFBQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpU |
|                                     | GFQRm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSH |
|                                     | pyS20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2l |
|                                     | NM1BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1 |
|                                     | bFVFN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gc2luIHByaXZpbGVnaW9zCiAgLSBuYW1lOiBhbGV4CiAgICBnZWNvczogIlVzdWFyaW8gRXN0w6FuZGFyIgogICAgcHJpbWFyeV9ncm91c |
|                                     | DogYWxleAogICAgZ3JvdXBzOgogICAgICAtIHVzZXJzCiAgICBzaGVsbDogL2Jpbi9iYXNoCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkZXlrRnh4MHV4ZklCOUp3QyRqdE1UVWl2TTZYZjVzSVpEdn |
|                                     | JGdVhQd2ZjeG1ZNTZOaXJrQ1h3dklEZWhZWEtUa0lVM2pMdlBHYjJvUEhkMnBaUmUwR0NSb1ZBaEJoSjh4cThKRi9vLwogICAgc3NoX2F1dGhvcml6ZWRfa2V5czoKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUF |
|                                     | BQUFEQVFBQkFBQUJnUUNmcVRveDlqR2dyVVNhcTByYS9vQVRzTkxWelNoMm50T2FLMmtod3c1Z0toTHpDU3d4RkR0UWdlcmV3MjlpNUNGWnptdloyN3F6ME9OSThEeml4NDUvYzdJYWZJTStqKy9BelB1ODI0UnBRTzFpUGFQ |
|                                     | Rm9KUEVIRlR4ZytLbmxyTUJrcHlDcThuUzMySkdqbnhaakxTQmFuMGhCeTV2NldZa0ZCbS80dUI1YjZqeGtGd205WmlwcWhPMFJUZnhLSktnTDdieFYxNVVuZCtDUUZGWGJyR0txM0grODQwdFJzYVRLMS9wakV1czgvSHpyS |
|                                     | 20ra0ZtdTV6Zlp4dzdzVFNjbkxEY3JvN1RoQXJ3M1pvSHFHeHVTK0VqSnk3YzFnREh2Qk1HSHA5RHdsSXhFTDJKQTBRUHdBSi9NWXRFc2hsVDZOZy9MWUNJb2txWWVXWkpQK0x1NHpwUWUwMVp0UWRCK2ZVNWJOSmFJQ2lNM1 |
|                                     | BNN3ZKb3orSFAxNjRNNTQ0TDlqUXo0Rk1nSEhXWmFJbWRYMzFDZ3JXMjJtZWl5aVJEdDNUUWV3bGROdUE5R1ZOdENmYk1aajdqTFc5bEY0Uk8wMG9iTUwwd1BGOVRIcmJlZkcxWm1ZZGYzSTArSkx2b3BBNGp0clRibUd1bFV |
|                                     | FN2hMTTFsVHNycVlvS0ZTN2VkTT0gYWxleEBSaW9sdQoKICAjIFVzdWFyaW8gcHJvZmVzb3IKICAtIG5hbWU6IHByb2Zlc29yCiAgICBnZWNvczogIlVzdWFyaW8gUHJvZmVzb3IiCiAgICBwcmltYXJ5X2dyb3VwOiBwcm9m |
|                                     | ZXNvcgogICAgZ3JvdXBzOgogICAgICAtIHN1ZG8KICAgIHNoZWxsOiAvYmluL2Jhc2gKICAgIHN1ZG86IFsnQUxMPShBTEwpIE5PUEFTU1dEOkFMTCddCiAgICBsb2NrX3Bhc3N3ZDogZmFsc2UKICAgIHBhc3N3ZDogJDYkM |
|                                     | DMvTjBBSi9RMjdjc0xoUSR6bllGNTlvRHJqNDd2U24xeEVBUjl2T0RGbUw1OEpSTXFXcC9jTkI4QnU5UmYyN2h5NmFtTTRxS0JJYjM1bTFoQWx4L3RldDdDcDl4ekcyYnlSUUhjMAogICAgc3NoX2F1dGhvcml6ZWRfa2V5cz |
|                                     | oKICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJnUURmOWxuQkgybkNUMmV6cERabnFTQmVEdVNzVkdHRkQxS3pxYTRLeUlWa3prckQ3cE5ISGtrcFN1TzRpc0tjQ3NVY29wWU9jQTM4UXRHN3d |
|                                     | CMHYvcW44SnNxNzMxTjhiamFLT2RRTjI1dnFMandWajhEcFl0dkdjK1pBMHVhQ2hlN1RTK1FCemxNQzl5cHdqNHdmMTVRL3ozdi9pcDRGRjJjT1JUMGNRQzA0Y05SUURnVWc0cDFybE9zOCttYTdPUGgzUDNVdnpsUGZMaGky |
|                                     | SDF5bCsvbW80WExPY0FNTnIvamlaQ3dZeG9tNk9FT1lWQk5rOE1aWC9abitxUmk3MUQwUlBpS2cyN0FjWFNEL0ZQV2RRVzloQkgxWnE1eEdpY1VGUzRDOXlYdkhLcnU3Y01tbXhWMkc4MHAvQXJSc2NLV3E5MlVUNWpJSlFwY |
|                                     | 2NtSHhzeGRJaTZvMjVMaGN4SDFkT25aeTZrSGNKMnlQMjRDbkJISzVZM1Nzb3ZDRDBUaDZNTjFWbFR5U2JsOEFyMHlwbVkrR1lPK29WZDRiTTNpb0h6TDBBTXFZblMyOW0wVXRFRHZGRVVVb1NrT29MSzR1U2xjdmVqK09JVn |
|                                     | A3WDVHN29aNTZuWlpmK3FIRWdvZHYrK2E2dlBtaEgyWlNnb09qMXNFMzlESzdJbnVLU3FDRT0gcmFmYUBlY28KICAgICAgLSBzc2gtcnNhIEFBQUFCM056YUMxeWMyRUFBQUFEQVFBQkFBQUJBUUNtam9WSW9aQ3g0UUZYdmx |
|                                     | qcW96WEdxeHhsU3ZPN1YyYWl6cXlQZ01mR3FueWwwSjlZWG82enJjV1l3eVdNbk1kUmR3WVpnSHFmaWlGQ1VuMlFEbTZadXpDNExjeDBLM1p3TzJsZ0w0WGFBVHlrVkxuZUhSMWliNlJOcm9GY0NsTjY5Y3hXc2R3UVc2ZHBq |
|                                     | cGlCRFhmOG02L3F4VlAzRUh3VVRzUDhYYU9WN1drY0NBcWZZQU12cFdMSVNxWW1lNmUrNlpHSlVJUGtEVHhhdnU1SlRhZ0RMd1krcHkxV0I1M2VvRFdzRzk5Z212eWl0Mk8xRW8ralJXTittZ1JISXhKVHJGdExTNm80aVdlc |
|                                     | 2hQWjZMdkNaL1B1bTEyT2o0QjRiakdTSHpyS2pIWmdUd2hWSi9MRHEzdjcxL1BQNHphSTNnVkI5WmFsZW1TeHFvbWdiVGxuVCBqb3NlQGRlYmlhbgoKIyBDYW1iaW8gZGUgY29udHJhc2XDsWEgZGVsIHVzdWFyaW8gcm9vdA |
|                                     | pjaHBhc3N3ZDoKICBsaXN0OiB8CiAgICByb290OnJvb3QKICBleHBpcmU6IGZhbHNl                                                                                                        |
| OS-EXT-STS:power_state              | N/A                                                                                                                                                                       |
| OS-EXT-STS:task_state               | None                                                                                                                                                                      |
| OS-EXT-STS:vm_state                 | building                                                                                                                                                                  |
| OS-SRV-USG:launched_at              | None                                                                                                                                                                      |
| OS-SRV-USG:terminated_at            | None                                                                                                                                                                      |
| accessIPv4                          | None                                                                                                                                                                      |
| accessIPv6                          | None                                                                                                                                                                      |
| addresses                           | N/A                                                                                                                                                                       |
| adminPass                           | KfpiiyFEbP5K                                                                                                                                                              |
| config_drive                        | None                                                                                                                                                                      |
| created                             | 2025-03-03T17:10:41Z                                                                                                                                                      |
| description                         | None                                                                                                                                                                      |
| flavor                              | description=, disk='0', ephemeral='0', , id='vol.large', is_disabled=, is_public='True', location=, name='vol.large', original_name='vol.large', ram='4096',              |
|                                     | rxtx_factor=, swap='0', vcpus='2'                                                                                                                                         |
| hostId                              | None                                                                                                                                                                      |
| host_status                         | None                                                                                                                                                                      |
| id                                  | 081aede7-7174-46db-9302-98cd99bba060                                                                                                                                      |
| image                               | N/A (booted from volume)                                                                                                                                                  |
| key_name                            | Alex                                                                                                                                                                      |
| locked                              | None                                                                                                                                                                      |
| locked_reason                       | None                                                                                                                                                                      |
| name                                | Master3                                                                                                                                                                   |
| pinned_availability_zone            | None                                                                                                                                                                      |
| progress                            | None                                                                                                                                                                      |
| project_id                          | ccb761aa90e244e68a2d7c59ff70e79f                                                                                                                                          |
| properties                          | None                                                                                                                                                                      |
| security_groups                     | name='default'                                                                                                                                                            |
| server_groups                       | None                                                                                                                                                                      |
| status                              | BUILD                                                                                                                                                                     |
| tags                                |                                                                                                                                                                           |
| trusted_image_certificates          | None                                                                                                                                                                      |
| updated                             | 2025-03-03T17:10:42Z                                                                                                                                                      |
| user_id                             | 0ff17b2a02b0f269d55fde473085d46786514ba6296157d210a45cc3e5a17fc3                                                                                                          |
| volumes_attached                    | delete_on_termination='False', id='eaba0db7-0e3b-4ad7-b65b-a180e7fbc88c'                                                                                                  |
+-------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

```bash
openstack server create \
  --flavor vol.large \
  --key-name Alex \
  --network "Red Intra de asangom1009b" \
  --volume "node-worker1-volume" \
  --user-data r_cloud-init.yml \
  "Worker1"
```

```bash
openstack server create \
  --flavor vol.large \
  --key-name Alex \
  --network "Red Intra de asangom1009b" \
  --volume "node-worker2-volume" \
  --user-data r_cloud-init.yml \
  "Worker2"
```

## Configurar los nodos master





### Cargar modulo del kernel

Añadimos un alias

```bash
echo "alias sysctl='/sbin/sysctl'" >> ~/.bashrc
source ~/.bashrc
```

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
```

Y comprobamos con lo siguiente

```bash
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

### Instalación de Docker en todos los nodos
Instalar Docker en cada nodo:

En todos los nodos (master1, master2, master3, worker1, worker2), ejecuta:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt install -y docker-ce
```

Configurar Docker para que se inicie automáticamente:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verificar instalación:

```bash
docker --version
```

### Instalación de Kubernetes
Agregar repositorios de Kubernetes en cada nodo:

En todos los nodos, ejecuta lo siguiente:

```bash
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Instalar kubelet, kubeadm y kubectl en cada nodo:

```bash
sudo apt install -y kubelet kubeadm kubectl
```

Marcar estos paquetes para que no se actualicen automáticamente:

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

### Desactivar el Swap
Kubernetes no permite el uso de swap. Desactívalo y asegúrate de que no se habilite en el arranque:

```bash
sudo swapoff -a
Para hacer el cambio permanente:

```bash
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

### Inicializar el Clúster Kubernetes en el Nodo Master 1

Primero, en el nodo **master1**, inicializamos el clúster. Es importante usar un balanceador de carga para asegurar la alta disponibilidad del clúster. 

Ejecuta el siguiente comando en el nodo **master1**:

```bash
sudo kubeadm init --pod-network-cidr=8.0.0.0/24
```

### Configuración de `kubectl` en el Nodo Master 1

Para interactuar con el clúster desde el nodo **master1** usando `kubectl`, es necesario configurar el archivo de configuración de Kubernetes:

1. Crea el directorio **`~/.kube`** si no existe:

   ```bash
   mkdir -p $HOME/.kube
   ```

2. Copia el archivo de configuración de Kubernetes a **`~/.kube/config`**:

   ```bash
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   ```

3. Cambia el propietario del archivo para que tu usuario tenga acceso:

   ```bash
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

### Añadir los nodos

Para fusionar los nodos master 2 y 3 con el uno ejecutamos no siguiente en el 1

```bash
kubeadm token create --print-join-command
```

### Instalar la Red de Pods

Kubernetes necesita una red de pods para permitir la comunicación entre los pods en el clúster. Vamos a instalar **Flannel** para esta red.

Para instalar **Flannel**, ejecuta:

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

Verifica que los pods estén corriendo:

```bash
kubectl get pods -n kube-system
```

### Unir los Nodos Worker al Clúster

En los nodos **worker1** y **worker2**, ejecuta el comando `kubeadm join` que se muestra al final del proceso de inicialización del nodo **master1** (el token y el hash generados automáticamente).

El comando se verá algo como esto:

```bash
sudo kubeadm join loadbalancer:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

Si por alguna razón no tienes este comando, genera un nuevo token manualmente como se explicó anteriormente.

### Paso 6: Verificar que los Nodos Worker se Han Unido al Clúster

Después de ejecutar el comando `kubeadm join` en los nodos worker, vuelve al nodo **master1** y ejecuta:

```bash
kubectl get nodes
```

Esto debe mostrar una salida similar a esta, donde los nodos master y worker están en estado `Ready`:

```bash
NAME      STATUS     ROLES    AGE   VERSION
master1   Ready      master   10m   v1.23.0
master2   Ready      master   5m    v1.23.0
master3   Ready      master   5m    v1.23.0
worker1   Ready      <none>   3m    v1.23.0
worker2   Ready      <none>   3m    v1.23.0
```

### Paso 7: Configuración de Alta Disponibilidad (HA)

Si tienes múltiples nodos **master**, puedes configurar un balanceador de carga (como HAProxy o Nginx) para dirigir el tráfico a cualquiera de los nodos master. Aquí se utiliza `loadbalancer` como nombre del balanceador de carga.

En el archivo de configuración de kubeadm en **master1**, asegúrate de especificar el punto de entrada como el balanceador de carga:

```bash
sudo kubeadm init --control-plane-endpoint "loadbalancer:6443" --pod-network-cidr=10.244.0.0/16 --upload-certs
```

### Verificación Final del Clúster

Para asegurarte de que todo esté funcionando correctamente, ejecuta los siguientes comandos:

1. **Verificar el estado de los nodos**:

   ```bash
   kubectl get nodes
   ```

2. **Verificar el estado de los pods**:

   ```bash
   kubectl get pods --all-namespaces
   ```

Esto debería mostrar que todos los nodos están en estado `Ready` y que todos los pods (incluyendo Flannel y otros componentes del sistema) están corriendo correctamente.
