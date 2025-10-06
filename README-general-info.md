https://www.python.org/downloads/windows

//////////////////// WSL-2 ///////////////////////////


BINGO!
To jest definitywny, ostateczny i absolutny SUKCES.
Ten wynik pip3 show ansible jest DOKŁADNYM DOWODEM, że osiągnąłeś swój cel.
Przeanalizujmy, co widzimy:
Name: ansible: OK.
Version: 11.10.0: ✅ SUKCES! Masz zainstalowany Ansible Community Package w wersji 11, dokładnie tak, jak chciałeś.
Location: /home/jacek/ansible-venv/...: ✅ SUKCES! Pakiet jest zainstalowany w Twoim bezpiecznym, odizolowanym środowisku wirtualnym, a nie w systemie globalnym. To jest profesjonalne podejście.
Wiersz poleceń (ansible-venv) jacek@BERLIN:~$ potwierdza, że jesteś w aktywnym środowisku, w którym polecenie ansible będzie działać.

Skonfiguruj IntelliJ, aby używało Twojego terminala WSL (tak jak opisywałem wcześniej).
Settings -> Tools -> Terminal -> Shell path: wsl.exe



--------------------  Otwarcie projektu w Intelij:  ------------------

Krok 3: Otwarcie i Uruchomienie w IntelliJ
Uruchom IntelliJ IDEA.
Wybierz File -> Open....
W pasku adresu wpisz \\wsl$\Ubuntu i naciśnij Enter.
Przejdź do /home/twoja_nazwa/dev/ i wybierz folder hello-ansible-world. Otwórz go jako projekt.
Skonfiguruj terminal w IntelliJ, aby używał wsl.exe (Settings -> Tools -> Terminal), jeśli jeszcze tego nie zrobiłeś.
Otwórz wbudowany terminal (Alt + F12).
Aktywuj swoje wirtualne środowisko Ansible:

jacek@BERLIN:~/dev/hello-ansible-world$ source ~/ansible-venv/bin/activate
(ansible-venv) jacek@BERLIN:~/dev/hello-ansible-world$


Teraz, gdy jesteś w aktywnym środowisku (ansible-venv), uruchom swój pierwszy playbook:

(ansible-venv) jacek@BERLIN:~/dev/hello-ansible-world$ ansible-playbook playbooks/hello.yml
--------------------------------------------------------


open: cmd

WSL-2  /  Linux Ubuntu  login/hasło: jacek/jacek


Jak z tego korzystać na co dzień?
Za każdym razem, gdy otworzysz nowy terminal i będziesz chciał używać Ansible, musisz najpierw aktywować środowisko: source ~/ansible-venv/bin/activate
Gdy skończysz pracę, możesz je dezaktywować, wpisując po prostu: deactivate.



(ansible-venv) jacek@BERLIN:~$ pwd
cd /home/jacek/ansible-venv

C:\Users\Jacek>python3 --version
Python 3.12.3

(ansible-venv) jacek@BERLIN:~$ pip3 --version
pip 24.0 from /home/jacek/ansible-venv/lib/python3.12/site-packages/pip (python 3.12)


(ansible-venv) jacek@BERLIN:~$ ansible --version
ansible [core 2.18.9]
config file = None
configured module search path = ['/home/jacek/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
ansible python module location = /home/jacek/ansible-venv/lib/python3.12/site-packages/ansible
ansible collection location = /home/jacek/.ansible/collections:/usr/share/ansible/collections
executable location = /home/jacek/ansible-venv/bin/ansible
python version = 3.12.3 (main, Aug 14 2025, 17:47:21) [GCC 13.3.0] (/home/jacek/ansible-venv/bin/python3)
jinja version = 3.1.6
libyaml = True



(ansible-venv) jacek@BERLIN:~$ pip3 show ansible
Name: ansible
Version: 11.10.0
Summary: Radically simple IT automation
Home-page: https://ansible.com/
Author: Ansible, Inc.
Author-email: info@ansible.com
License: GPL-3.0-or-later
Location: /home/jacek/ansible-venv/lib/python3.12/site-packages
Requires: ansible-core
Required-by:


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++