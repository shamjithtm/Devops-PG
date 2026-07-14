docker inspect sonarqube-server --format '{{json .NetworkSettings.Networks}}'
{"sonarqube_sonar-net":{"IPAMConfig":null,"Links":null,"Aliases":["sonarqube-server","sonarqube"],"MacAddress":"62:e9:82:a0:05:f1","DriverOpts":null,"GwPriority":0,"NetworkID":"535cfd7293c382571a75f1ebbbeb855079da899f05489a12f656812ca88e53c5","EndpointID":"2ece13d7de6ad7bdf664fded528bbf7472befdae35da6087c47432e8caa47da7","Gateway":"172.23.0.1","IPAddress":"172.23.0.3","IPPrefixLen":16,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"DNSNames":["sonarqube-server","sonarqube","07949d08c844"]}}
root@ip-172-31-4-207:/home/ubuntu#


connect to jenkin

docker network connect sonarqube_sonar-net <jenkins-container-name>
