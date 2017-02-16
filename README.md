# Firewall
Sets up the system firewall, either iptables or firewalld.

**NOTE**: The firewall setup and configuration has been greatly simplified from previous iterations of ISU roles

## Variables

### Global Variables

No global variables are used in this role.

### Role Variables

The following variables are configurable:

1. **configure_iptables**: (boolean)
1. **configure_firewalld**: (boolean)

#### Iptables

1. **iptables_save_rules**: (boolean) Determine whether or not iptables rules should be saved
1. **iptables_default_rules**: (list of dictionaries)
1. **iptables_rules**: (list of dictionaries)
1. **iptables_rules_extra**: (list of dictionaries)

#### FirewallD
1. **firewalld_default_zone**: Default zone for firewalld. This is, by default, set to public.
1. **firewalld_default_rules**: (list of dictionaries)
1. **firewalld_rules**: (list of dictionaries)
1. **firewalld_rules_extra**: (list of dictionaries)


### User Variables
Users are encouraged to modify the role variables inside their group_vars folder.

#### Iptables

By default, the iptables rule listing for the firewall role is:

    iptables_save_rules: true

    iptables_default_rules:

      - chain: INPUT
        ctstate:
          - ESTABLISHED
          - RELATED
        jump: ACCEPT
        state: present

      - chain: INPUT
        protocol: icmp
        jump: DROP

      - chain: INPUT
        in_interface: 'lo'
        jump: ACCEPT

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
        source: '129.186.0.0/16'
        table: filter
        destination_port: '22'
        jump: ACCEPT

      - chain: INPUT
        jump: DROP

      - chain: FORWARD
        jump: DROP

    iptables_rules: []
    iptables_rules_extra: []

Three variables are provided by this role:

- iptables_default_rules
- iptables_rules
- iptables_rules_extra

These variables are flattened (concatenated, but not overwritten), so you can create different groups that may modify different iptables rules.

#### Firewalld

By default, the firewalld rules configuration is:

    firewalld_default_zone: public

    firewalld_default_rules:

      - zone: public
        service: ssh
        state: disabled
        permanent: yes

      - zone: work
        source: '129.186.0.0/16'
        state: enabled
        permanent: yes

      - zone: work
        source: '10.0.0.0/8'
        state: enabled
        permanent: yes

      - zone: work
        service: ssh
        permanent: yes
        state: enabled

    firewalld_rules: []

    firewalld_rules_extra: []

The default zone is listed as "public". This is changed within the file /etc/firewalld/firewalld.conf

This rule disables ssh for public service and enables ssh in the "work" zone, which is defined as anything originating from the 129.186. and 10. subnets, or almost the entirety of ISU's campus network.

You can overwrite this, if necessary, in your group_vars or host_vars folders, as you see fit.

The variables for configuration provided are:

- firewalld_default_rules
- firewalld_rules
- firewalld_rules_extra

These variables are flattened (concatenated, but not overwritten), so you can create different groups that may modify different firewalld rules.

## Tasks

### Description
This role does the following:

#### IPTables
1. Starts iptables
2. Creates iptables rules based on your variable configuration.
3. Optionally saves your iptables rules
4. Optionally restarts your iptables

#### Firewalld
1. Sets the default firewalld zone.
2. Creates firewalld rules based on your variable configuration.
3. Restarts firewalld.

### Changed Files
- /etc/sysconfig/iptables, or
- /etc/firewalld*

### Installed Programs
No programs are installed as part of this role

## Example

### FirewallD

#### group_vars/all

    configure_firewalld: true
    firewalld_default_zone: "public"


#### playbooks/firewall.yml

    - name: Firewall Playbook
      hosts: all
      user: root
      connection: smart

      roles:
        - firewall

### IPTables
#### group_vars/all

    configure_iptables: true
    iptables_save_rules: true


#### playbooks/firewall.yml

    - name: Firewall Playbook
      hosts: all
      user: root
      connection: smart

      roles:
        - firewall
