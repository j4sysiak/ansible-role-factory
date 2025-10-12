
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
