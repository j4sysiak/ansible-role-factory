W terminalu WSL (lub Intelij) aktywuj środowisko: 

# source ~/ansible-venv/bin/activate

i odpal to:  
# ansible-galaxy collection install community.postgresql

------------------------------------------------------------------------------------
Teraz Twój cykl pracy (np. podczas testowania zmian) wygląda tak:
"Burzenie" starej konfiguracji (aby mieć czysty start):

(w terminalu z aktywnym venv)
# (ansible-venv) jacek@BERLIN:~/dev/ansible-master-config$ ansible-playbook playbooks/teardown.yml


"Budowanie" nowej, świeżej konfiguracji:
# (ansible-venv) jacek@BERLIN:~/dev/ansible-master-config$ ansible-playbook playbooks/setup.yml



Weryfikacja
Wejdź na http://141.144.231.36 – powinieneś zobaczyć stronę Nginx.
Zaloguj się na serwer przez SSH i sprawdź:
sudo iptables -L INPUT – powinieneś zobaczyć wszystkie 3 reguły.
systemctl status postgresql – usługa powinna być active (running).


*****************************************************************************************************************
uruchamianie al'la molecule:   testowanie roli

 
 
(ansible-venv) jacek@BERLIN:  ansible-playbook playbooks/test_role.yml --extra-vars "test_role=influxdb"

--extra-vars "test_role=influxdb": To jest polecenie, które przekazuje zmienną z linii poleceń do naszego playbooka. 
Ansible podstawi influxdb w miejsce {{ test_role }}.
To polecenie uruchomi tylko zadania z roli influxdb na Twoim serwerze. To jest dokładnie to, co robi molecule converge.
=======
************************************************************************
          al'a  Molecule

czyszczenie:
# ansible-playbook playbooks/teardown.yml
 
 
Etap 2: converge (Testowanie roli)
Teraz, zamiast uruchamiać cały setup.yml, użyjemy naszego nowego, sprytnego playbooka test_role.yml i "wstrzykniemy" do niego rolę influxdb.
# ansible-playbook playbooks/test_role.yml --extra-vars "test_role=influxdb"
 

--extra-vars "test_role=influxdb": To jest polecenie, które przekazuje zmienną z linii poleceń do naszego playbooka. Ansible podstawi influxdb w miejsce {{ test_role }}.
To polecenie uruchomi tylko zadania z roli influxdb na Twoim serwerze. To jest dokładnie to, co robi molecule converge.


Zaloguj się na serwer przez SSH.
Sprawdź, czy usługa influxdb działa:
# systemctl status influxdb

Sprawdź, czy InfluxDB nasłuchuje na swoim domyślnym porcie (8086):
# ss -tlpn | grep 8086


Dodałem nową regułę do firewalla w sekcji vars – to jest najlepsze miejsce, aby trzymać całą konfigurację iptables w jednym miejscu.

  # --- PRZENIESIONA I POPRAWIONA SEKCJA ZMIENNYCH ---
  vars:
    firewall_rules:
      - { proto: 'tcp', port: '22', comment: 'Allow SSH' }
      - { proto: 'tcp', port: '80', comment: 'Allow HTTP for Nginx' }
      - { proto: 'tcp', port: '5432', comment: 'Allow PostgreSQL' }
      - { proto: 'tcp', port: '8080', comment: 'Allow Tomcat' }
      - { proto: 'tcp', port: '8086', comment: 'Allow InfluxDB' }
      - { proto: 'tcp', port: '3000', comment: 'Allow Grafana' }
      - { proto: 'tcp', port: '9090', comment: 'Allow Prometheus' }



Dodałem zupełnie nowy "play" na końcu pliku. Celuje on w grupę monitoring_servers i uruchamia na niej rolę influxdb. Przypisałem mu również tag influxdb, co pozwoli nam w przyszłości uruchomić tylko tę część konfiguracji, jeśli będziemy chcieli 

# --- NOWA SEKCJA (NOWY "PLAY") ---
- name: Configure monitoring servers
  hosts: monitoring_servers
  become: true
  roles:
    - role: influxdb
      tags: ['influxdb']
# --- KONIEC NOWEJ SEKCJI ---




# (ansible-playbook ... --tags "influxdb").
