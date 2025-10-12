
first step - uruchom dockera:
# README--docker.md
 
*******************************************************
Następnie uruchamiamy playbook:

# ansible-playbook deploy_icinga_master.yml --ask-vault-pass

# ansible-playbook verify_icinga_master.yml

http://localhost:8080/icingaweb2/dashboard
http://localhost:8080/icingaweb2/setup

*******************************************************

Ewentualna diagnostyka:
Musimy dostać się do powłoki bash wewnątrz naszego kontenera icinga-master.
 
# docker exec -it icinga-master bash
# service icinga2 status

Przeczytaj Główny Plik Logu Icinga 2
To jest najważniejszy krok. Domyślnie Icinga 2 zapisuje swoje logi w pliku 
# tail -n 50 /var/log/icinga2/icinga2.log

Użyjemy komendy tail, aby zobaczyć ostatnie kilkadziesiąt linii tego pliku – to tam powinny być najnowsze błędy.


Icinga 2 ma wbudowane narzędzie do sprawdzania, 
   czy wszystkie pliki konfiguracyjne są poprawne składniowo. To jest niezwykle przydatne.

# icinga2 daemon -C

---

## Najczęstsze problemy i szybka diagnostyka

### 1. Problemy z certyfikatami i API (port 5665)
Jeśli w logach pojawia się błąd o braku pliku `ca.crt` lub problem z uprawnieniami do certyfikatów:

```bash
# Upewnij się, że katalog na certyfikaty istnieje
mkdir -p /var/lib/icinga2/certs

# Skopiuj plik CA do katalogu certyfikatów
cp /var/lib/icinga2/ca/ca.crt /var/lib/icinga2/certs/ca.crt
chown nagios:nagios /var/lib/icinga2/certs/ca.crt
chmod 644 /var/lib/icinga2/certs/ca.crt

# Wygeneruj certyfikat jako użytkownik nagios
su -s /bin/bash nagios -c "icinga2 pki new-cert --cn icinga-master --key /var/lib/icinga2/certs/icinga-master.key --cert /var/lib/icinga2/certs/icinga-master.crt"

# Zrestartuj usługę
service icinga2 restart
```

### 2. Problemy z bazą danych MySQL (brak tabel)
Jeśli w logach pojawia się błąd o braku tabeli `icinga_dbversion`:

```bash
# Połącz się z bazą MySQL i zainicjuj schemat:
mysql -u root -p

# W konsoli MySQL:
USE icinga2;
SOURCE /usr/share/icinga2-ido-mysql/schema/mysql.sql;
```

Po tych krokach Icinga2 powinna działać poprawnie, a port 5665 będzie otwarty.

## Sprawdzanie dostępności Icinga Web 2

- Jeśli sprawdzasz z hosta (np. przeglądarka, Ansible):
  http://localhost:8080/icingaweb2/setup
  (port 8080 jest mapowany na port 80 w kontenerze)

- Jeśli sprawdzasz z wnętrza kontenera:
  http://localhost/icingaweb2/setup
  (port 80 jest otwarty w kontenerze)

Przykład:

Z hosta:
    curl http://localhost:8080/icingaweb2/setup

Z kontenera:
    docker exec -it icinga-master bash
    curl http://localhost/icingaweb2/setup



***********************************************************
uruchaminie aplikacji icingaweb2
***********************************************************
# docker exec -it icinga-master bash

root@icinga-master:/# cat /etc/icingaweb2/setup.token
QnC48nsFWYKSX3Xm


Przykładowe dane do wpisania w kreatorze bd:
Resource Name: icingaweb2
Database Type: MySQL
Host: localhost
Port: 3306
Database Name: icingaweb2
Username: icingaweb2
Password: icingaweb2


# mysql -u root -p     ----> (hasło: test)

# CREATE DATABASE IF NOT EXISTS icingaweb2;
# CREATE USER IF NOT EXISTS 'icingaweb2'@'%' IDENTIFIED BY 'icingaweb2';
# GRANT ALL PRIVILEGES ON icingaweb2.* TO 'icingaweb2'@'%';
# FLUSH PRIVILEGES;


Administration
Now it's time to configure your first administrative account or group for Icinga Web 2.

admin / admin

You've configured Icinga Web 2 successfully. 
You can review the changes supposed to be made before setting it up. 
Make sure that everything is correct (Feel free to navigate back to make any corrections!) 
so that you can start using Icinga Web 2 right after it has successfully been set up.

Database Setup
The database user "icingaweb2" will be used to setup the missing schema required 
by Icinga Web 2 in database "icingaweb2".

Authentication
Users will authenticate using a database.

Authentication Backend
Backend Name	icingaweb2
Administration
Administrative rights will initially be granted to a new account called "admin".

Resource
Database
Resource Name	icingaweb_db
Database Type	mysql
Host	localhost
Port	3306
Database Name	icingaweb2
Username	icingaweb2
Password	icingaweb2



---------------
W tym kroku kreatora Icinga Web 2 musisz podać dane dostępowe do bazy danych IDO (Icinga Data Output), 
czyli tej, do której Icinga 2 zapisuje wyniki monitoringu.

Jeśli nie masz jeszcze użytkownika i bazy, utwórz je w MySQL:
# mysql -u root -p     ----> (hasło: test)

Host — jeśli łączysz się z contenera 
# CREATE DATABASE IF NOT EXISTS icinga2;
# CREATE USER IF NOT EXISTS 'icinga2'@'%' IDENTIFIED BY 'icinga2';
# GRANT ALL PRIVILEGES ON icinga2.* TO 'icinga2'@'%';
# FLUSH PRIVILEGES;

Host — jeśli łączysz się z localhost, a użytkownik jest utworzony tylko dla %, utwórz go także dla localhost:
# CREATE USER IF NOT EXISTS 'icinga2'@'localhost' IDENTIFIED BY 'icinga2';
# GRANT ALL PRIVILEGES ON icinga2.* TO 'icinga2'@'localhost';
# FLUSH PRIVILEGES;


MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| icinga2            |
| icingaweb2         |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.016 sec)

MariaDB [(none)]> SELECT User, Host FROM mysql.user;
+-------------+-----------+
| User        | Host      |
+-------------+-----------+
| icinga2     | %         |
| icingaweb2  | %         |
| icinga2     | localhost |
| mariadb.sys | localhost |
| mysql       | localhost |
| root        | localhost |
+-------------+-----------+

baza icinga2 jest pusta lub nie ma w niej schematu IDO (brakuje tabel, np. icinga_dbversion). 
Musisz zaimportować schemat do bazy.

# USE icinga2;

To polecenie SQL importuje schemat bazy danych IDO do wybranej bazy (icinga2). Upewnij się, że jesteś w konsoli MySQL i masz wybraną bazę icinga2:
# SOURCE /usr/share/icinga2-ido-mysql/schema/mysql.sql;


 
library "db_ido_mysql"

object IdoMysqlConnection "ido-mysql" {
user = "icinga2",
password = "icinga2",
host = "127.0.0.1",
database = "icinga2"
}



Przykładowe dane (zgodnie z Twoim README):
Resource Name: icinga_ido
Database Type: MySQL
Host: 127.0.0.1
Port: 3306
Database Name: icinga2
Username: icinga2
Password: icinga2

library "db_ido_mysql"

vi /etc/icinga2/features-enabled/ido-mysql.conf
" 
object IdoMysqlConnection "ido-mysql" {
user = "icinga2",
password = "icinga2",
host = "127.0.0.1",
database = "icinga2"
}"



Sprawdź, czy moduł IDO MySQL jest włączony w Icinga 2
# icinga2 feature list
# service mysql restart



Sprawdź plik api-users.conf (zwykle w /etc/icinga2/conf.d/api-users.conf lub /etc/icinga2/conf.d/):
Upewnij się, że masz użytkownika z odpowiednimi uprawnieniami, np.:

object ApiUser "icingaweb2" {
password = "icingaweb2pass"
permissions = [ "status/query", "actions/*", "objects/modify/*", "objects/query/*" ]
}



 ----------  podsumowanie  ----------
1. Upewnij się, że Icinga 2 działa poprawnie:
2. Sprawdź logi Icinga 2 pod kątem błędów:
   tail -n 50 /var/log/icinga2/icinga2.log
3. Sprawdź, czy moduł IDO MySQL jest włączony:
   icinga2 feature list
4. Upewnij się, że baza danych i użytkownik są poprawnie skonfigurowani.
5. Sprawdź plik api-users.conf, aby upewnić się, że użytkownik ma odpowiednie uprawnienia.
6. Upewnij się, że port 5665 jest otwarty i nasłuchuje:
   netstat -tuln | grep 5665
7. Sprawdź, czy certyfikaty są poprawnie skonfigurowane i mają odpowiednie uprawnienia.
8. Użyj narzędzia do sprawdzania konfiguracji Icinga 2:
   icinga2 daemon -C
9. Po każdej zmianie konfiguracji zrestartuj Icinga 2:
   service icinga2 restart
10. Po wykonaniu powyższych kroków spróbuj ponownie uzyskać dostęp do Icinga Web 2.

Monitoring Backend
Icinga Web 2 will retrieve information from your monitoring environment using a backend called "icinga" and the specified resource below:

Database Resource
Resource Name	icinga_ido
Database Type	mysql
Host	127.0.0.1
Port	3306
Database Name	icinga2
Username	icinga2
Password	icinga2



Command Transport
Icinga Web 2 will use the Icinga 2 API to send commands to your monitoring instance by using the connection details listed below:

Host	localhost
Port	5665
Username	icingaweb2
Password	icingaweb2pass



Monitoring Security
Icinga Web 2 will protect your monitoring environment against prying eyes using the configuration specified below:

Protected Custom Variables






******************************************************************

Możesz teraz zalogować się do panelu Icinga Web 2 i rozpocząć monitorowanie systemów.
http://localhost:8080/icingaweb2/dashboard
http://localhost:8080/icingaweb2/setup
******************************************************************
    



