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
    ssl_certificate: '/certs/'  # One or more SSL certificates, for SSL offloading. This can be a single cert, a list of certs or a directory which contains all the certs. Optional.
    ssl_ciphers: 'ECDHE-RSA-AES256-SHA:RC4-SHA' # A string of the SSL ciphers acceptable to HAProxy. Optional.
    vhosts:                     # A 'vhost', to be configured using ACLs. Optional.
    - host: a.cool.com          # A host name for the vhost. Required.
      backend: acoolcom         # The backend this vhost should proxy to. Required.
    default_backend: fourohfour # The default backend to be proxied to, if nothing else matches. Optional.
````
TCP mode example:
````
haproxy_frontends:
  - name: my-cool-frontend      # Name for the frontend
    ip: '172.16.1.1'            # The IP this frontend should bind to. Defaults to '*'
    port: '5432'                # The port this frontend should bind to. Defaults to '80'
    tcp_mode: True              # The TCP mode will be enabled and used instead of the default HTTP
````
````
haproxy_backends:
  - name: acoolcom              # A natural name for each backend. Required.
    balance: first              # The algorithm used to select a server. Defaults to 'roundrobin'
    servers:                    # List of the servers if host_vars is not used
      - name: one.abc.com       # Name for the server in the pool
        port: 80                # Port number of the application
      - name: two.abc.com
        port: 80
````

`haproxy_frontend_port` and `haproxy_backend_port` are will set the default ports across the whole play. `haproxy_ssl_ciphers` will set the default ciphers for all frontends.

Defining Backends
-----------------

Back-end servers are defined using host variables. To add a server to a back-end, add the `haproxy_backend` variable to the server in your inventory and set it to match the backend server defined in the vars file. Optionally, a `haproxy_port` variable will set the port. Of course, this can be set at a group level as well.

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
