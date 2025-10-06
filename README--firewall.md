to na pocztkuu - utworzenie roli:  ansible-galaxy init roles/firewall
----------------------------------------------------

Vault hasło:  test


# ansible-playbook molecule/default/destroy.yml --ask-vault-pass

# ansible-playbook configure_server.yml --ask-vault-pass

# ansible-playbook molecule/default/verify.yml --ask-vault-pass


ubuntu@instance-20250920-1026:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW IN    Anywhere                   # Webserver: A                                                                                                             llow HTTP
22/tcp                     ALLOW IN    Anywhere                   # Webserver: A                                                                                                             llow SSH
443/tcp                    ALLOW IN    Anywhere                   # Allow HTTPS                                                                                                              for Nginx
80/tcp (v6)                ALLOW IN    Anywhere (v6)              # Webserver: A                                                                                                             llow HTTP
22/tcp (v6)                ALLOW IN    Anywhere (v6)              # Webserver: A                                                                                                             llow SSH
443/tcp (v6)               ALLOW IN    Anywhere (v6)              # Allow HTTPS



sudo iptables -nvL


ubuntu@instance-20250920-1026:~$ sudo iptables -S | grep -- '--dport 80'
-A InstanceServices -d 169.254.0.2/32 -p tcp -m tcp --dport 80 -m comment --comment "See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule" -j ACCEPT
-A InstanceServices -d 169.254.0.3/32 -p tcp -m owner --uid-owner 0 -m tcp --dport 80 -m comment --comment "See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule" -j ACCEPT
-A InstanceServices -d 169.254.0.4/32 -p tcp -m tcp --dport 80 -m comment --comment "See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule" -j ACCEPT
-A InstanceServices -d 169.254.169.254/32 -p tcp -m tcp --dport 80 -m comment --comment "See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule" -j ACCEPT
-A ufw-user-input -p tcp -m tcp --dport 80 -j ACCEPT
ubuntu@instance-20250920-1026:~$


Jeśli wszystko jest skonfigurowane poprawnie (a wiemy, że jest), powinieneś zobaczyć jedną, prostą linię, która wygląda mniej więcej tak:
 
-A ufw-user-input -p tcp -m tcp --dport 80 -j ACCEPT


/////////////


Dla kompletności, oto wersja dla IPv6:
code
Bash
sudo ip6tables -S | grep -- '--dport 80'

ubuntu@instance-20250920-1026:~$ sudo ip6tables -S | grep -- '--dport 80'
-A ufw6-user-input -p tcp -m tcp --dport 80 -j ACCEPT


