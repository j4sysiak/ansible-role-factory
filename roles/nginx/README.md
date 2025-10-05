W terminalu WSL (lub Intelij) aktywuj środowisko:

# source ~/ansible-venv/bin/activate

destroy
# ansible-playbook molecule/default/destroy.yml

uruchomienie
# ansible-playbook molecule/default/converge.yml

sprawdzenie
# ansible-playbook molecule/default/verify.yml


przetestuj w przeglądarce: http://141.144.231.36