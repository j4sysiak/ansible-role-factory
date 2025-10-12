icinga_agent
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


