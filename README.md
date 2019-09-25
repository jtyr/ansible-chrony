chrony
======

Role which helps to install and configure Chrony.

The configuration of the role is done in such way that it should not be necessary
to change the role for any kind of configuration. All can be done either by
changing role parameters or by declaring completely new configuration as a
variable. That makes this role absolutely universal. See the examples below for
more details.

Please report any issues or send PR.


Example
-------

```yaml
---

- name: Default installation
  hosts: myhost1
  roles:
    - chrony

- name: Example of how to set custom NTP server list
  hosts: myhost1
  vars:
    # Override the default variable
    chrony_config_server:
      - 0.uk.pool.ntp.org
      - 1.uk.pool.ntp.org
      - 2.uk.pool.ntp.org
      - 3.uk.pool.ntp.org
  roles:
    - chrony

- name: Example of how to extend the default configuration
  hosts: myhost2
  vars:
    # Change the default configuration - disable RTC sync
    chrony_config_rtcsync: None
    # Extending the default configuration
    chrony_config_custom:
      # Allow to set time manually from chronyc
      # (the value is adding comment line under the option without argument)
      manual: "\n#"
  roles:
    - chrony
```


Role variables
--------------

List of variables used by the role:

```yaml
# Package to be installed (you can force a specific version here)
chrony_pkg: chrony

# Default service name
chrony_service: chronyd

# Default location of the chrony.conf file
chrony_conf_file: /etc/chrony.conf


# Common options for the server list bellow
chrony_config_server_options: iburst

# Default list of NTP servers
chrony_config_server:
  - 0.centos.pool.ntp.org
  - 1.centos.pool.ntp.org
  - 2.centos.pool.ntp.org
  - 3.centos.pool.ntp.org

# Default drift file location
chrony_config_driftfile: /var/lib/chrony/drift

# Default make step
chrony_config_makestep: 1.0 3

# Default rtcsync
chrony_config_rtcsync: "\n#"

# Default location of the log directory
chrony_config_logdir: /var/log/chrony

# Default configuration
chrony_config_default:
  server: "{{ chrony_config_server | map('regex_replace', '^(.*)$', '\\1 ' ~ chrony_config_server_options) | list }}"
  driftfile: "{{ chrony_config_driftfile }}"
  makestep: "{{ chrony_config_makestep }}"
  rtcsync: "{{ chrony_config_rtcsync }}"
  logdir: "{{ chrony_config_logdir }}"

# Custom configuration
chrony_config_custom: {}

# Final configuration
chrony_config: "{{
  chrony_config_default | combine(
  chrony_config_custom) }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
