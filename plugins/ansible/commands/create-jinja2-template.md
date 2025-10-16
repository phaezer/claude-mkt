---
description: Create Jinja2 templates for Ansible
argument-hint: Optional template requirements
---

# Jinja2 Template Creation

You are creating Jinja2 templates for Ansible using the jinja2-developer agent, which coordinates with domain specialists for accurate technical content.

## Workflow

### 1. Identify Template Requirements

Determine what template is needed:
- **Configuration files**: Application configs, server configs
- **Scripts**: Shell scripts, service files
- **Web content**: HTML, nginx configs, Apache configs
- **Network configs**: FRR, netplan, interfaces
- **Database configs**: PostgreSQL, MySQL, MongoDB
- **System files**: /etc files, systemd units

### 2. Gather Template Information

If not specified, ask for:
- **Template purpose**:
  - What file is being generated
  - Target application or service
  - Configuration goals
- **Domain/technology**:
  - Web server (Nginx, Apache, Caddy)
  - Database (PostgreSQL, MySQL, Redis)
  - Network (FRR, BGP, OSPF, interfaces)
  - Application (custom app config)
  - System (systemd, cron, logrotate)
- **Variables needed**:
  - Required variables (no defaults)
  - Optional variables (with defaults)
  - Variable types and validation
- **Target platform**:
  - Operating system
  - Software versions
  - Environment (dev/staging/production)
- **Domain specialist needed**:
  - Which specialist agent to consult
  - What expertise is required

### 3. Identify Domain Expert

**CRITICAL**: Before developing templates with technical content, identify specialist:

**Network configurations**:
- FRR routing configs → `frr-config-generator`
- Netplan configs → `netplan-config-generator`
- Network interfaces → `interfaces-config-generator`

**Web servers**:
- Nginx configs → Web server specialist
- Apache configs → Web server specialist
- HAProxy configs → Load balancer specialist

**Databases**:
- PostgreSQL configs → Database specialist
- MySQL configs → Database specialist
- MongoDB configs → Database specialist

**Applications**:
- Custom app configs → Application specialist
- Kubernetes manifests → K8s specialist

**System**:
- Systemd units → System specialist
- Security configs → Security specialist

### 4. Consult Domain Specialist

**Ask user which specialist to consult**, then:

Launch appropriate specialist agent:
```
"Generate [technology] configuration for [purpose].
Requirements:
- [List specific requirements]
- Target: [platform/version]
- Environment: [dev/staging/prod]
- Best practices for [specific concerns]"
```

### 5. Create Template

Launch **jinja2-developer** with specialist guidance:
```
"Create Jinja2 template for [file/purpose].
Technology: [web server/database/network/etc.]
Specialist consulted: [agent-name]
Specialist recommendations: [guidance from specialist]

Variables:
- Required: [list]
- Optional: [list with defaults]

Template should:
- Include ansible_managed header
- Incorporate specialist's configuration recommendations
- Use proper Jinja2 syntax
- Validate inputs where possible
- Support different environments
- Include helpful comments
- Document all variables"
```

### 6. Review Template

Check generated template for:
- **Syntax**: Valid Jinja2 syntax
- **Variables**: All variables defined and used correctly
- **Defaults**: Sensible default values with `| default()`
- **Validation**: Input checking where possible
- **Comments**: Clear documentation
- **ansible_managed**: Included in header
- **Specialist guidance**: Properly incorporated
- **Platform-specific**: OS/version conditionals if needed

### 7. Create Example Task

Generate example Ansible task to use the template:

```yaml
- name: Template [file description]
  ansible.builtin.template:
    src: [template-name].j2
    dest: /path/to/destination
    owner: [user]
    group: [group]
    mode: '0644'
    validate: '[validation-command %s]'  # If available
    backup: yes
  notify: [Handler name]
  tags: config
```

### 8. Test Template

Validate template works:

```bash
# Test template rendering
ansible-playbook test-template.yml -i localhost, --connection=local

# Check rendered output
cat /tmp/rendered-template

# Validate syntax (if validator available)
nginx -t -c /tmp/rendered-template  # For nginx
postgresql --check /tmp/rendered-template  # For PostgreSQL
```

## Template Development Patterns

### Basic Configuration Template

**Example: Application Config**

```jinja2
{#
Template: app_config.ini.j2
Purpose: Application configuration file
Specialist: None (simple key-value config)

Required Variables:
  app_name: Application name
  app_port: Port to listen on

Optional Variables:
  app_debug: Enable debug mode (default: false)
  app_log_level: Logging level (default: info)
  app_workers: Number of workers (default: 4)
#}

# {{ ansible_managed }}
# Application Configuration

[general]
name = {{ app_name }}
port = {{ app_port }}
environment = {{ app_environment | default('production') }}

{% if app_debug | default(false) %}
debug = true
log_level = debug
{% else %}
debug = false
log_level = {{ app_log_level | default('info') }}
{% endif %}

[performance]
workers = {{ app_workers | default(4) }}
timeout = {{ app_timeout | default(30) }}
keepalive = {{ app_keepalive | default(5) }}

[database]
host = {{ db_host }}
port = {{ db_port | default(5432) }}
name = {{ db_name }}
user = {{ db_user }}
{% if db_ssl_enabled | default(true) %}
ssl_mode = require
{% endif %}
```

### Network Configuration Template

**Example: FRR BGP (with specialist guidance)**

```jinja2
{#
Template: frr_bgp.conf.j2
Purpose: FRR BGP routing configuration
Specialist: frr-config-generator (consulted for BGP best practices)

Required Variables:
  bgp_local_asn: Local AS number
  bgp_router_id: BGP router ID
  bgp_peer_ip: Peer IP address
  bgp_peer_asn: Peer AS number

Optional Variables:
  bgp_peer_description: Peer description (default: 'BGP Peer')
  bgp_peer_password: Peer authentication password
  bgp_network: Network to advertise
  bgp_max_prefix: Maximum prefixes (default: 1000)
#}

# {{ ansible_managed }}
# FRR BGP Configuration
# Specialist guidance: frr-config-generator

router bgp {{ bgp_local_asn }}
 bgp router-id {{ bgp_router_id }}
 bgp log-neighbor-changes
 no bgp default ipv4-unicast

 {# Peer configuration from specialist recommendations #}
 neighbor {{ bgp_peer_ip }} remote-as {{ bgp_peer_asn }}
 neighbor {{ bgp_peer_ip }} description {{ bgp_peer_description | default('BGP Peer') }}

 {% if bgp_peer_password is defined %}
 neighbor {{ bgp_peer_ip }} password {{ bgp_peer_password }}
 {% endif %}

 {# Address family configuration #}
 address-family ipv4 unicast
  {% if bgp_network is defined %}
  network {{ bgp_network }}
  {% endif %}

  neighbor {{ bgp_peer_ip }} activate
  neighbor {{ bgp_peer_ip }} prefix-list {{ bgp_prefix_list_in | default('PL-IN') }} in
  neighbor {{ bgp_peer_ip }} prefix-list {{ bgp_prefix_list_out | default('PL-OUT') }} out
  neighbor {{ bgp_peer_ip }} maximum-prefix {{ bgp_max_prefix | default(1000) }} 80

  {% if bgp_default_originate | default(false) %}
  neighbor {{ bgp_peer_ip }} default-originate
  {% endif %}
 exit-address-family
!

{# Prefix lists for route filtering #}
{% if bgp_allowed_prefixes is defined %}
ip prefix-list {{ bgp_prefix_list_in | default('PL-IN') }} seq 5 deny 0.0.0.0/0 le 32
{% for prefix in bgp_allowed_prefixes %}
ip prefix-list {{ bgp_prefix_list_in | default('PL-IN') }} seq {{ loop.index * 10 + 10 }} permit {{ prefix }}
{% endfor %}
!
{% endif %}

{% if bgp_advertised_prefixes is defined %}
{% for prefix in bgp_advertised_prefixes %}
ip prefix-list {{ bgp_prefix_list_out | default('PL-OUT') }} seq {{ loop.index * 10 }} permit {{ prefix }}
{% endfor %}
ip prefix-list {{ bgp_prefix_list_out | default('PL-OUT') }} seq 1000 deny 0.0.0.0/0 le 32
!
{% endif %}
```

### Web Server Template

**Example: Nginx Virtual Host**

```jinja2
{#
Template: nginx_vhost.conf.j2
Purpose: Nginx virtual host configuration
Specialist: Web server specialist (for SSL and security hardening)

Required Variables:
  vhost_domain: Domain name
  vhost_root: Document root path

Optional Variables:
  vhost_port: HTTP port (default: 80)
  vhost_ssl: Enable SSL (default: false)
  vhost_ssl_cert: SSL certificate path
  vhost_ssl_key: SSL key path
  vhost_locations: Custom location blocks
#}

# {{ ansible_managed }}
# Nginx Virtual Host: {{ vhost_domain }}

server {
    listen {{ vhost_port | default(80) }};
    server_name {{ vhost_domain }} {% if vhost_aliases is defined %}{{ vhost_aliases | join(' ') }}{% endif %};

    {% if vhost_ssl | default(false) %}
    # SSL Configuration (from specialist recommendations)
    listen 443 ssl http2;
    ssl_certificate {{ vhost_ssl_cert }};
    ssl_certificate_key {{ vhost_ssl_key }};
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    {% endif %}

    root {{ vhost_root }};
    index index.html index.htm {% if vhost_php | default(false) %}index.php{% endif %};

    # Logging
    access_log /var/log/nginx/{{ vhost_domain }}_access.log;
    error_log /var/log/nginx/{{ vhost_domain }}_error.log;

    # Default location
    location / {
        try_files $uri $uri/ {% if vhost_php | default(false) %}=404{% else %}/index.html{% endif %};
    }

    {% if vhost_php | default(false) %}
    # PHP-FPM configuration
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    {% endif %}

    {% if vhost_locations is defined %}
    # Custom locations
    {% for location in vhost_locations %}
    location {{ location.path }} {
        {% if location.proxy_pass is defined %}
        proxy_pass {{ location.proxy_pass }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        {% elif location.alias is defined %}
        alias {{ location.alias }};
        {% elif location.return is defined %}
        return {{ location.return }};
        {% endif %}

        {% if location.auth_basic is defined %}
        auth_basic "{{ location.auth_basic }}";
        auth_basic_user_file {{ location.auth_basic_file }};
        {% endif %}
    }

    {% endfor %}
    {% endif %}

    # Deny access to hidden files
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
}

{% if vhost_ssl | default(false) and vhost_redirect_http | default(true) %}
# Redirect HTTP to HTTPS
server {
    listen {{ vhost_port | default(80) }};
    server_name {{ vhost_domain }};
    return 301 https://$server_name$request_uri;
}
{% endif %}
```

### Systemd Service Template

**Example: Systemd Unit File**

```jinja2
{#
Template: app_service.j2
Purpose: Systemd service unit file
Specialist: System specialist (for hardening and best practices)

Required Variables:
  service_name: Service name
  service_exec_start: Start command
  service_user: User to run as

Optional Variables:
  service_description: Service description
  service_working_directory: Working directory
  service_environment: Environment variables
  service_restart: Restart policy (default: on-failure)
  service_restart_sec: Restart delay (default: 10)
#}

# {{ ansible_managed }}
# Systemd Service: {{ service_name }}

[Unit]
Description={{ service_description | default(service_name + ' Service') }}
After=network.target {% if service_after is defined %}{{ service_after | join(' ') }}{% endif %}

{% if service_requires is defined %}
Requires={{ service_requires | join(' ') }}
{% endif %}

[Service]
Type={{ service_type | default('simple') }}
User={{ service_user }}
{% if service_group is defined %}
Group={{ service_group }}
{% endif %}

{% if service_working_directory is defined %}
WorkingDirectory={{ service_working_directory }}
{% endif %}

{% if service_environment is defined %}
{% for key, value in service_environment.items() %}
Environment="{{ key }}={{ value }}"
{% endfor %}
{% endif %}

ExecStart={{ service_exec_start }}
{% if service_exec_reload is defined %}
ExecReload={{ service_exec_reload }}
{% endif %}

{% if service_exec_stop is defined %}
ExecStop={{ service_exec_stop }}
{% else %}
KillMode={{ service_kill_mode | default('mixed') }}
KillSignal={{ service_kill_signal | default('SIGTERM') }}
{% endif %}

Restart={{ service_restart | default('on-failure') }}
RestartSec={{ service_restart_sec | default(10) }}

# Security hardening (from specialist recommendations)
{% if service_harden | default(true) %}
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem={{ service_protect_system | default('strict') }}
ProtectHome={{ service_protect_home | default('true') }}
ReadWritePaths={{ service_writable_paths | default(['/var/log/' + service_name, '/var/lib/' + service_name]) | join(' ') }}
{% endif %}

# Resource limits
{% if service_limit_nofile is defined %}
LimitNOFILE={{ service_limit_nofile }}
{% endif %}
{% if service_limit_nproc is defined %}
LimitNPROC={{ service_limit_nproc }}
{% endif %}

# Logging
StandardOutput={{ service_stdout | default('journal') }}
StandardError={{ service_stderr | default('journal') }}
SyslogIdentifier={{ service_name }}

[Install]
WantedBy={{ service_wanted_by | default('multi-user.target') }}
```

## Output Format

### Template Delivery

**Template**: [template-name].j2
**Purpose**: [description]
**Specialist Consulted**: [agent-name or "None"]
**Technology**: [web server/database/network/etc.]

**Variables**:

Required:
- `variable_name`: Description, type, example
- `another_var`: Description, type, example

Optional (with defaults):
- `optional_var`: Description (default: value)
- `another_optional`: Description (default: value)

**Example Ansible Task**:
```yaml
- name: Template [file description]
  ansible.builtin.template:
    src: [template-name].j2
    dest: /path/to/file
    owner: root
    group: root
    mode: '0644'
    validate: '[command %s]'  # If applicable
    backup: yes
  notify: [Handler name]
  tags: config
```

**Example Variables**:
```yaml
# In defaults/main.yml or group_vars
variable_name: "value"
another_var: 8080
optional_var: "custom_value"
```

**Testing**:
```bash
# Render template locally
ansible-playbook test.yml -i localhost, --connection=local

# Validate rendered config
[validation-command] /tmp/rendered-file

# Deploy to test environment
ansible-playbook playbook.yml -i inventory/dev --check
ansible-playbook playbook.yml -i inventory/dev
```

## Best Practices

**Template Design**:
- Always include `{{ ansible_managed }}` header
- Use `| default()` for all optional variables
- Add comments explaining non-obvious logic
- Group related configuration sections
- Use whitespace control (`{%-` and `-%}`) for clean output

**Variable Management**:
- Namespace variables with role/template name
- Document all variables in template header
- Provide sensible defaults
- Validate input where possible with `assert`

**Security**:
- Never hardcode secrets in templates
- Use Ansible Vault for sensitive variables
- Set appropriate file permissions in task
- Validate rendered configs before deploying

**Testing**:
- Test rendering with example variables
- Validate syntax of rendered output
- Test in development before production
- Use `validate` parameter when available

**Documentation**:
- Document purpose in template header
- List all required and optional variables
- Include example values
- Note specialist consultation if applicable
- Explain complex logic with comments
