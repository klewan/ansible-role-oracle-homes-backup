Ansible Role: oracle-homes-backup
=================================

This role performs backup of a particular ORACLE_HOME using tar utility

Supported OS:
-------------
* RedHat
* CentOS
* OracleLinux


Requirements
------------

This role uses `oracle` role.

It requires the following variables to be defined:

* `oracle_homes_backup_ora_home` -> ORACLE_HOME to backup
* `oracle_homes_backup_dir` -> backup and log directory location

Additionally, this role executes `sudo tar` commands without ansible privilege escalation feature.
Therefore, the user, who runs the role, must have `(root) NOPASSWD: /bin/tar` sudoers privilege configured.


Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    # ORACLE_HOME to backup:
    oracle_homes_backup_ora_home:

    # ORACLE_HOME backup directory
    oracle_homes_backup_dir: /path/to/backup/dir

    # display message about location of a backup directory
    oracle_homes_backup_display_backup_message: true


Example Playbook
----------------

    - name: Backup ORACLE_HOMEs
      hosts: ora-servers
      gather_facts: true
      become: true
      become_user: '{{ oracle_user }}'

      vars:
        _oracle_homes_backup_rac_support: false

      tasks:

      - import_role:
          name: oracle-gatherinfo-gi
        tags:
          - oracle-gatherinfo-gi

      - import_role:
          name: oracle-gatherinfo-databases
        tags:
          - oracle-gatherinfo-databases

      - include_role:
          name: oracle-homes-backup
        vars:
          oracle_homes_backup_ora_home: '{{ _oracle_homes_backup_outer_item.oracle_home }}'
        with_items:
          - '{{ oracle_gi_info }}'
          - '{{ oracle_databases }}'
        loop_control:
          label: "[ORACLE_HOME: {{ _oracle_homes_backup_outer_item.oracle_home }}]"
          loop_var: _oracle_homes_backup_outer_item
        when: not _oracle_homes_backup_rac_support|bool
        tags:
          - oracle-homes-backup


Inside `vars/main.yml` or `group_vars/..` or `host_vars/..`:


    #-----------------------------------------------
    # overrides role 'oracle-homes-backup' variables
    #-----------------------------------------------

    # ORACLE_HOME backup directory
    oracle_homes_backup_dir: /DB_Export/orahome_backups

    
License
-------

GPLv3 - GNU General Public License v3.0

Author Information
------------------

This role was created in 2018 by [Krzysztof Lewandowski](mailto:Krzysztof.Lewandowski@fastmail.fm).

