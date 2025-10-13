icinga2_agent  -->  to jets po prostu usługa agenta Icinga2, 
       którą instalujemy na zdalnym serwerze, aby umożliwić monitorowanie tego serwera 
przez serwer Icinga2 (master) - zadanie roles/icinga-master.
W tym przykładzie zainstalujemy agenta na serwerze Ubuntu 22.04 
              o adresie IP 141.144.231.36  tag w inventory: [oracle_vms]

------------

Uruchamiamy playbook:

# ansible-playbook deploy_icinga2_agent.yml --ask-vault-pass

# ansible-playbook verify_icinga2_agent.yml


Po pomyślnym wykonaniu zaloguj się na serwer i sprawdź, czy usługa działa:
 
ssh ubuntu@141.144.231.36
# systemctl status icinga2_agent


# sudo icinga2_agent feature enable api
Enabling feature api. Make sure to restart Icinga 2 for these changes to take effect.
# systemctl restart icinga2_agent


