paulrentschler.firewall
=======================

Installs and configures the firewall on Ubuntu Linux machines.


Requirements
------------

The following Ansible collections need to be installed for this role to work:

* community.general

Install these collections using:

    ansible-galaxy collection install <collection name>


Role Variables
--------------

The following variables are available with defaults defined in `defaults/main.yml`:

Specify whether or not the firewall should be enabled (it protects nothing unless it is enabled).

    firewall_enabled: yes


### SSH specific settings

Specify which port the SSH daemon is listening on. Defaults to the current SSH port that Ansible is using.

    firewall_ssh_port: "{{ ansible_ssh_port }}"

To restrict the IP addresses and/or networks that can connect via SSH.

    firewall_ssh_from:
      - 10.0.0.10
      - 10.0.0.0/8
      - 172.16.0.0/12
      - 192.168.0.0/16


### HTTP specific settings for unsecured web traffic

To allow traffic from anywhere to port 80 (http) change the `no` to a `yes`.

    firewall_allow_http: no

To restrict the IP addresses and/or networks that can connect via http (in addition to setting `firewall_allow_http: yes`).

    firewall_http_from:
      - 10.0.0.10
      - 172.16.0.0/12
      - 192.168.0.0/16


### HTTPS specific settings for SSL-secured web traffic

To allow traffic from anywhere to port 443 (https) change the `no` to a `yes`.

    firewall_allow_https: no

To restrict the IP addresses and/or networks that can connect via https (in addition to setting `firewall_allow_https: yes`).

    firewall_https_from:
      - 10.0.0.10
      - 172.16.0.0/12
      - 192.168.0.0/16


### Additional "custom" firewall rules

Additional firewall rules can be specified with the `firewall_rules` variable that takes a list of rules with at most 4 parameters:

* rule (required) - the action to take: allow, deny, limit, reject
* to_port (required) - the destination port
* from_ip (optional) - the source IP address
* proto (optional) - the TCP/IP protocol: any, tcp, udp, ipv6, esp, ah, gre, igmp

    firewall_rules:
      # allow traffic to port 8080 from anywhere
      - rule: allow
        to_port: 8080
      # block UDP traffic to port 514 (syslog) from 1.2.3.4
      - rule: deny
        to_port: 514
        from_ip: 1.2.3.4
        proto: udp


Dependencies
------------

None.


Example Playbook
----------------

Simple example that will enable the firewall and only permit SSH traffic from anywhere on the port currently being used by Ansible for SSH:

    - hosts: all
      roles:
         - paulrentschler.firewall

Complex example that enables the firewall, limits SSH traffic from one IP to a non-standard port, and allows HTTP/HTTPS traffic from a small group of IP ranges:

    - hosts: all
      vars:
        allowed_networks:
          - 192.168.10.0/24
          - 192.169.20.0/24
          - 10.0.0.0/14
      roles:
        - { role: paulrentschler.firewall,
            firewall_ssh_port: 8022,
            firewall_ssh_from: 192.168.10.10,
            firewall_allow_http: yes,
            firewall_allow_https: yes,
            firewall_http_from: {{ allowed_networks }},
            firewall_https_from: {{ allowed_networks }},
            }


License
-------

MIT


Author Information
------------------

Created by Paul Rentschler in 2021.
