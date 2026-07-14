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
