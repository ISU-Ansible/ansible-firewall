Firewall
========

Firewall role


Default Variables
-----------------

### Configuration

You can use the *configure_firewalld* or *configure_iptables* variables to
determine which service will be configured. Only one should be chosen, not
both.


### IPTables Usage

This rule forwards port 80 to port 8080

    iptables_rules:

      - table: nat
        chain: PREROUTING
        in_interface: {{ ansible_default_ipv4.alias }}
        protocol: tcp
        match: tcp
        destination_port: 80
        jump: REDIRECT
        to_ports: 8080
        comment: "Redirect port 80 to 8080"

This rule accepts all established and related connections and is extremely
useful for decreasing the amount of time network connections are filtered.
Note that the ctstate variable expects a list of states.

      iptables_rules:

        - chain: INPUT
          ctstate:
            - ESTABLISHED
            - RELATED
          jump: ACCEPT

These rules accept SSH connections from 192.168/16 and 10.0/8

      iptables_rules:

        - chain: INPUT
          ctstate:
            - NEW
          protocol: tcp
          source: '10.0.0.0/8'
          table: filter
          destination_port: '22'
          jump: ACCEPT

        - chain: INPUT
          ctstate:
            - NEW
          protocol: tcp
          source: '192.168.0.0/16'
          table: filter
          destination_port: '22'
          jump: ACCEPT



### Firewalld Usage

These variables will set up the firewalld service to accept ssh connections
from 192.168/16 and 10.0/8

    firewalld_default_zone: public
    firewalld_zone_interface: []
    firewalld_zone_source:
      - zone: work
        source: 10.0.0.0/8
      - zone: work
        source: 192.168.0.0/16
    firewalld_service_rules:
      - zone: work
        service: ssh
    firewalld_port_rules: []
    firewalld_rich_rules: []

Alternatively, you can enable/disable any zones, interfaces, services, ports,
or rich rules.


Handlers
========

Handlers
