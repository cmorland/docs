debops.samba
############



This role installs and configures a Samba 3 file server with a default file
share. You can configure your own shares using inventory variables.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.samba


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # Location of Samba shares
    samba_path: '/srv/samba'
    samba_homes_path: '{{ samba_path }}/home'
    samba_shares_path: '{{ samba_path }}/shares'
    
    # Allow access to Samba through firewall for specified networks
    # If samba_allow is undefined or False, allow access from all
    samba_allow: []
    
    samba_workgroup: 'WORKGROUP'
    samba_netbios_name: '{{ ansible_hostname }}'
    samba_server_string: '%h file server'
    
    # Name of the /etc/init.d/ service script
    samba_service_name: 'samba'
    
    # Manage iptables modules automatically
    samba_iptables: True
    
    # Which hash variable is used to configure [global] section in smb.conf
    samba_global: '{{ samba_default_global }}'
    
    # You can specify additional options in a separate hash
    samba_global_custom: False
    
    # Which hash variable is used to configure [homes] section in smb.conf
    samba_homes: '{{ samba_default_homes }}'
    
    # Which hash of hashes is used to configure shares in smb.conf
    samba_shares: '{{ samba_default_shares }}'
    
    # Default [global] configuration
    samba_default_global:
    
      # Browsing / Identification
      workgroup: '{{ samba_workgroup | default("WORKGROUP") }}'
      netbios_name: '{{ samba_netbios_name | default(ansible_hostname) }}'
      server_string: '{{ samba_server_string | default("%h file server") }}'
    
      # Logs / Accounting
      log_file: '/var/log/samba/log.%m'
      max_log_size: '1000'
      syslog: '0'
    
      # Authentication
      security: 'user'
      encrypt_passwords: 'yes'
      passdb_backend: 'tdbsam'
      unix_password_sync: 'no'
      obey_pam_restrictions: 'yes'
      invalid_users: 'root bin daemon adm sync shutdown halt mail news uucp proxy www-data backup sshd'
      map_to_guest: 'Bad User'
      guest_account: 'nobody'
    
      # File system / Directories
      unix_charset: 'UTF8'
      locking: 'yes'
      wide_links: 'no'
      browseable: 'yes'
      create_mask: '0660'
      directory_mask: '0770'
      dont_descend: './lost+found'
      use_sendfile: 'yes'
      hide_unreadable: 'yes'
      hide_files: '/.*/lost+found/'
    
      # Networking / Connections
      socket_options: 'TCP_NODELAY SO_RCVBUF=8192 SO_SNDBUF=8192'
      deadtime: '10'
      wins_support: 'no'
      dns_proxy: 'no'
      name_resolve_order: 'lmhosts host wins bcast'
    
      # Disable printing by default
      printing: 'bsd'
      load_printers: 'no'
      printcap_name: '/dev/null'
      show_add_printer_wizard: 'no'
      disable_spoolss: 'yes'
    
    
    # Default [homes] section
    samba_default_homes:
      path: '{{ samba_homes_path }}/%S'
      comment: 'Home Directory'
      browseable: 'no'
      read_only: 'no'
      create_mask: '0600'
      directory_mask: '0700'
      valid_users: '%S'
      guest_ok: 'no'
      root_preexec: '/usr/local/sbin/samba-homedir.sh %S'
    
    
    # Hash of hashes of default shares
    samba_default_shares:
    
      'Public Files':
        path: '{{ samba_shares_path }}/public'
        comment: 'Public Files'
        read_only: 'yes'
        guest_ok: 'yes'




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.samba`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

