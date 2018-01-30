Firewall
========

Firewall role

Data Data data data data

Usage
-----

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

These rules accept SSH connections from 129.186/16 and 10.0/8

iptables_rules:

- chain: INPUT
ctstate:
- NEW
protocol: tcp

Handlers
========

Handlers

