Esta documento cubre la configuración de una VPN site-to-site basada en WireGuard y pfsense.

[https://www.wireguard.com/](https://www.wireguard.com/) es un sistema VPN basado en criptografía.

Una VPN site-to-site es una VPN en la que dos redes que se encuentran separadas por una WAN y conectadas a ella mediante un router/firewall que impide el acceso a ellas desde esa WAN pueden comunicarse entre ellas de forma transparente para cada uno de los equipos que componen esa red.

[https://www.pfsense.org/](https://www.pfsense.org/) es un software que permite convertir un PC genérico con varios interfaces de red en un firewall/router.


# Conceptos previos

En WireGuard no existe el concepto de servidor<->cliente. Existe el concepto de tunnel, peer que serían equivalentes

Un tunnel es un servicio VPN contra el cual se conecta un peer el cual puede acceder a la red interna que ha expuesto el tunnel.

## Configuración de red

Vamos a suponer un escenario con dos redes separadas por una WAN. En ambas redes tendremos la siguiente configuración de red:


```
                                  WAN
                   ┌────────────────────────────────┐
     192.168.56.2  │                                │  192.168.56.3
           ┌───────►                                ◄─────┐
           │       │                                │     │
           │       │                                │     │
           │       │                                │     │
           │       └────────────────────────────────┘     │
           │                                              │
       ┌───┴───────┐                               ┌──────┴─────┐
       │           │10.20.2.0            10.20.2.1 │            │
       │ firewall  ◄───────────────────────────────►   firewall │
       │           │        WIREGUARD NET          │            │
       └────▲──────┘                               └──────▲─────┘
            │                                             │
            │                                             │
            │                                             │
            │                                             │
            ▼                                             ▼
      ┌─────────────┐                               ┌─────────────┐
      │             │                               │             │
      │Local network│                               │Local network│
      │             │                               │             │
      └─────────────┘                               └─────────────┘
      192.168.57.0/24                               192.168.58.0/24

```



### Red1
* IP WAN 
    * 192.168.56.2
* IP WireGuard network
    * 10.20.2.0
* Red local
    * 192.168.57.0/24

### Red2
* IP WAN 
    * 192.168.56.3
* IP WireGuard network
    * 10.20.2.1
* Red local
    * 192.168.58.0/24



## Configuración site-to-site

La configuración de una VPN site-to-site con WireGuard consiste en:

* Crear un tunel WireGuard en cada uno de los routers/firewall que hacen de puerta de acceso de cada red
* Crear un peer en cada uno de los túneles WireGuard que apunte al tunel WireGuard contrario a través de su dirección ip WAN intercambiando sus claves públicas
* Mapear cada uno de los túneles en un interfaz virtual ethernet en cada uno de los routers/firewall
* Mapear una dirección IP del tunel WireGuard al interfaz virtual
* Crear un gateway que apunte a la dirección IP WireGuard del interfaz WireGuard opuesto
* Enrutar el tráfico hacia la red local opuesta a través de ese gateway

## Configuración pfsense


* Instalar WireGuard en pfsense
    * System -> Package Manager -> Available Packages
* Habilitar un tunel WireGuard en cada uno pfsense
    * VPN -> WireGuard -> Tunnels -> Add Tunnel
        * Listen Port 51820
        * Generate Key (copy public key)
        * Interface Address: WireGuard IP (10.20.2.0/31 o 10.20.2.1)
* Abrir el puerto WireGuard en el interfaz WAN
    * Firewall -> Rules -> WAN -> Add
        * Family: IPv4
        * Protocol: UDP
        * Destination: WAN address
        * Port Range: 51820
* Habilitar un peer en cada tunel
    * VPN -> Add Peer to the Tunnel (person figure in actions tab)
        * Static
        * Endpoint
            * WAN ip y puerto (51820) WAN del router/firewall contrario
        * Public key del tunel WireGuard contrario.
        * Allowed ips:
            * Ip address red WireGuard contrario (10.20.2.1/31 o 10.20.2.0)
            * Red local contrario (192.168.58.0/24 o 192.168.10.0/24)
* Asignar un interfaz al tunel 
    * Interfaces -> Assignments -> Add (tun_wg0)
* Poner la ip WireGuard a ese interfaz de red red
    * Interfaces -> ¿OPT2?
        * Enable interface
        * Ipv4 Configuration Static
            * IPv4 Address: WireGard IP (10.20.2.0/31 o 10.20.2.1/31)
* Crear un nuevo gateway con la ip del WireGuard del contrario
    * System -> Routing -> Add
        * Interface: OPT2
        * IP: 10.20.2.1 o 10.20.2.0
* Enrutar el tráfico hacia la red local contraria
    * System -> Routing -> Static Routes -> Add
    * Gateway: Gateway anterior
    * Destination network: Red local contrario (192.168.58.0/24 o 192.168.57.0/24)
* Abrir todos los puertos y todo el tipo de tráfico de ese interfaf de red (¿Verificar el origen del tráfico?)
    * Firewall -> Rules -> OPT2 -> Add
        * Protocol: Any
* Abrir puertos y tráfico en la LAN hacia la subred local del pfsense contrario
    * Firewall -> Rules -> LAN
        * Family: Ipv4
        * Protocol: Any
        * Destination: Network: (192.168.58.0/24 o 192.168.57.0/24)

Referencia: [https://docs.netgate.com/pfsense/en/latest/recipes/wireguard-s2s.html](https://docs.netgate.com/pfsense/en/latest/recipes/wireguard-s2s.html)