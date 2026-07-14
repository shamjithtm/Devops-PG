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
