zadziałać dokładnie tak, jak się tego spodziewaliśmy.
#  ansible-playbook configure_server.yml --tags "web" --ask-vault-pass
#  ansible-playbook configure_server.yml --skip-tags "tools" --ask-vault-pass
#  ansible-playbook configure_server.yml --tags "security,web" --ask-vault-pass