composer audit


trivy fs /var/www/laravel


semgrep scan .


vendor/bin/phpstan analyse


Fix this in php.ini file 


Important PHP settings to review:

display_errors=Off
allow_url_include=Off
allow_url_fopen=Off
expose_php=Off
cgi.fix_pathinfo=0

Disable dangerous functions where possible:

exec
shell_exec
system
passthru
proc_open
popen

trivy fs /

composer audit

semgrep scan .

lynis audit system

clamscan -r /var/www

aide --check



<img width="1122" height="704" alt="image" src="https://github.com/user-attachments/assets/026390c5-6d19-4fc5-a3c5-73136c627150" />



lynis


cd /usr/local/src

sudo git clone https://github.com/CISOfy/lynis.git

cd /usr/local/src/lynis
sudo git pull


sudo ln -sf /usr/local/src/lynis/lynis /usr/local/bin/lynis

 /usr/local/bin/lynis audit system



 add in wazuh agent 


Step 3: Configure the Wazuh Agent

Edit the agent configuration:

sudo nano /var/ossec/etc/ossec.conf

Add:

<localfile>
  <location>/var/log/lynis-report.dat</location>
  <log_format>syslog</log_format>
</localfile>

Save the file.

Step 4: Restart the Wazuh Agent
sudo systemctl restart wazuh-agent

Step 5: Verify the agent
sudo tail -f /var/ossec/logs/ossec.log



crete cronjob to check it 

vim lynis-audit.sh


#!/bin/bash

/usr/local/bin/lynis audit system --quiet

logger "Lynis audit completed"



chmod +x lynis-audit.sh



crontab -e 

#### security Audit Cron Jobs

0 2 * * 0 /scripts/security/lynis-audit.sh


SSH Hardening (/etc/ssh/sshd_config)
Your SSH configuration is accepting a bit too much default behavior. Open /etc/ssh/sshd_config (or a dedicated drop-in file under /etc/ssh/sshd_config.d/) and update or add the following directives:

Plaintext
AllowTcpForwarding no
AllowAgentForwarding no
X11Forwarding no
MaxAuthTries 3
MaxSessions 2
LogLevel VERBOSE
TCPKeepAlive no
ClientAliveCountMax 2
Note: After updating, verify the configuration syntax with sudo sshd -t before restarting the service using sudo systemctl restart sshd.






Create a hardening configuration file, for example /etc/sysctl.d/99-lynis-hardening.conf, and add the following values:

Plaintext
# File system protections
fs.protected_fifos = 2
fs.protected_regular = 2

# Restrict kernel pointer exposure and module manipulation
kernel.kptr_restrict = 2
kernel.sysrq = 0
kernel.yama.ptrace_scope = 1

# Disable network redirects (prevents MITM routing tricks)
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0


sudo sysctl --system

# Spoof protection (Reverse Path Filtering) and logging
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1







systemd-analyze security nginx.service




Bash
sudo systemctl edit nginx
This opens a blank file. Paste the following hardening directives inside the [Service] block to knock down the majority of those security risks:

Ini, TOML
[Service]
# Prevents the service and its children from gaining new privileges (Fixes NoNewPrivileges)
NoNewPrivileges=yes

# Denies access to physical hardware devices like raw disks/USB (Fixes PrivateDevices)
PrivateDevices=yes

# Makes /usr, /boot, and /etc read-only for this service
ProtectSystem=full

# Hides /home, /root, and /run/user entirely from the service
ProtectHome=yes

# Makes kernel variables in /proc/sys read-only (Fixes ProtectKernelTunables)
ProtectKernelTunables=yes

# Prevents the service from loading or unloading kernel modules (Fixes ProtectKernelModules)
ProtectKernelModules=yes

# Prevents modifications to the control group architecture (Fixes ProtectControlGroups)
ProtectControlGroups=yes

# Prevents the creation of writable and executable memory mappings (Fixes MemoryDenyWriteExecute)
MemoryDenyWriteExecute=yes

# Restricts the service from creating new namespaces (Fixes RestrictNamespaces)
RestrictNamespaces=yes

# Locks down system host/domain name modifications (Fixes ProtectHostname)
ProtectHostname=yes
Save and exit the editor. Systemd will automatically reload the configuration. Then, restart your service to apply the changes:

Bash
sudo systemctl restart nginx
