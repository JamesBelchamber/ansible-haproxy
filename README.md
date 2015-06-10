HAProxy
=========

A role that installs and configures HAProxy. This role is designed to gracefully and easily handle common HAProxy configurations, instead of completely supporting every single feature. As such, you may want to go over the features and check this is the right playbook for you - of course, improvements are welcome!

Requirements
------------

At the moment this has only been tested with EL6 servers.

Role Variables
--------------

This role accepts two variables - haproxy_frontends and haproxy_backends - that are dictionaries. The layout is as follows:

````
haproxy_frontends:
  - name: my-cool-frontend      # A natural name for each frontend. Required.
    ip: '192.168.1.1'           # The IP this frontend should bind to. Defaults to '*'
    port: '443'                 # The port this frontend should bind to. Defaults to '80'
    vhosts:                     # A 'vhost', to be configured using ACLs. Optional.
    - host: a.cool.com          # A host name for the vhost. Required.
      backend: acoolcom         # The backend this vhost should proxy to. Required.
    default_backend: fourohfour # The default backend to be proxied to, if nothing else matches. Optional.
````
````
haproxy_backends:
  - name: acoolcom              # A natural name for each backend. Required.
    balance: first              # The algorithm used to select a server. Defaults to 'roundrobin'
    servers:                    # A list of servers in the backend pool. Required.
    - name: coolback1           # A natural name for each server. Required.
      ip: 192.168.2.1           # Server IP. Required.
      port: 8080                # Server port. Defaults to '80'
````

Example Playbook
----------------

I would recommend writing all these frontends and backends into variable files, which you can call in on a per-play basis - like so:

    - hosts: proxy_servers
      vars_files:
      - vars/proxy_vars.yml
      roles:
         - { role: jamesbelchamber.haproxy }

License
-------

GPLv3

Author Information
------------------

This role was created in 2015 by [James Belchamber](http://james.belchamber.com/)
