# Mattermost Ansible Role

This role will install a Mattermost chat server on Ubuntu 18.04 with a nginx proxy.  
Supports http or https via a lets encrypt cert.  
Is configured to connect to a postgresql database which is not included in this role.


## Other Roles being used

### **nginxinc.nginx**
Source: https://galaxy.ansible.com/nginxinc/nginx  
Used as the proxy server

### **clutterbox.dehydrated**
Source: https://galaxy.ansible.com/clutterbox/dehydrated  
Used to generate the certs if the var `mattermost_http_type` is set to `https`


## Example playbooks

Install mattermost with no ssl using an external db
```yaml
---
- name: Create a mattermost server
  hosts: mattermost
  become: true
  vars:
    mattermost_domain: "mattermost.example.com"
    mattermost_http_type: "http"
    mattermost_db_host: "SOME_HOST/IP_HERE"
    mattermost_db_name: "mattermost"
    mattermost_db_user: "mmuser"
    mattermost_db_pass: "secret"

  roles:
    - xtream1101.mattermost
```

Install mattermost alongside a postgres db and use letsencrypt certs
```yaml

- name: Create a mattermost server
  hosts: mattermost
  become: true
  vars:
    mattermost_domain: "mattermost.example.com"
    mattermost_http_type: "https"
    mattermost_db_name: "mattermost"
    mattermost_db_user: "mmuser"
    mattermost_db_pass: "secret"

    postgresql_databases:
      - name: "{{ mattermost_db_name }}"
    postgresql_users:
      - name: "{{ mattermost_db_user }}"
        password: "{{ mattermost_db_pass }}"

    dehydrated_accept_letsencrypt_terms: yes
    dehydrated_contactemail: your@email.com
    dehydrated_challengetype: dns-01
    dehydrated_lexicon_dns:
      LEXICON_CLOUDFLARE_USERNAME: your@email.com
      LEXICON_CLOUDFLARE_TOKEN: AHG87923...
    dehydrated_domains: |
      {{ mattermost_domain }}

  roles:
    - geerlingguy.postgresql
    - xtream1101.mattermost
```
