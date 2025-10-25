
Zanim uruchomisz cały playbook, warto sprawdzić, czy Ansible w ogóle jest w stanie połączyć się z Twoim serwerem. Użyj do tego specjalnego modułu ping.

# ansible -i ~/dev/ansible-role-factory/inventory/hosts.ini oci_servers -m ping

# cd /home/jacek/dev/ansible-role-factory
# ansible-playbook -i ~/dev/ansible-role-factory/inventory/hosts.ini install_free_tools.yml
