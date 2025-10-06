
# ansible-playbook configure_server.yml --ask-vault-pass

ssh ubuntu@141.144.231.36
sudo ufw status verbose


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



////////////////////////


Teraz pójdziemy o krok dalej w tworzeniu profesjonalnych, reużywalnych ról. Do tej pory nasza rola firewall była sterowana "na sztywno" przez zmienną firewall_rules. Co jeśli chcielibyśmy mieć różne zestawy reguł dla różnych typów serwerów (np. "podstawowe" i "dla serwera web") i łatwo się między nimi przełączać?
Nowy Lekki Przykład: Domyślne Zmienne i Przesłanianie (Variable Precedence)
Nauczymy się, jak tworzyć role, które mają wbudowane, "bezpieczne" ustawienia domyślne, ale pozwalają na łatwe ich nadpisanie z zewnątrz. To jest klucz do tworzenia uniwersalnych komponentów.
Nasz cel: Ulepszymy rolę firewall, aby miała wbudowaną listę reguł domyślnych, ale pozwoli nam w prosty sposób "podmienić" tę listę na inną, bardziej specyficzną dla serwera WWW.
Krok 1: Przeniesienie Domyślnych Reguł do Wnętrza Roli
Rola powinna być samowystarczalna. Przenieśmy podstawowy zestaw reguł do jej wnętrza.
Otwórz plik roles/firewall/defaults/main.yml. Pliki w tym katalogu mają najniższy priorytet i są idealnym miejscem na definiowanie wartości domyślnych.
Wklej do niego podstawowy zestaw reguł:
code
Yaml
---
# defaults file for firewall
firewall_rules:
- port: '22'
  proto: 'tcp'
  rule: 'allow'
  comment: 'Default: Allow SSH'
  Teraz nasza rola domyślnie otwiera tylko port 22, co jest bezpiecznym punktem wyjścia dla każdego serwera.
  Krok 2: Zdefiniowanie Specjalistycznych Reguł w group_vars
  Teraz w pliku group_vars zdefiniujemy naszą specyficzną listę reguł dla serwera WWW. Ansible ma precyzyjną kolejność wczytywania zmiennych – te z group_vars zawsze nadpiszą te z defaults.
  Otwórz plik group_vars/all/main.yml.
  Upewnij się, że zawiera on naszą listę reguł dla serwera WWW. Zmieńmy lekko komentarze, aby widzieć różnicę.
  code
  Yaml
---
# ... inne zmienne ...

firewall_rules:
- port: '22'
  proto: 'tcp'
  rule: 'allow'
  comment: 'Webserver: Allow SSH'
- port: '80'
  proto: 'tcp'
  rule: 'allow'
  comment: 'Webserver: Allow HTTP'
  Krok 3: Uruchomienie i Weryfikacja Przesłaniania
  Teraz самое ciekawe. Uruchomimy nasz główny playbook. Ansible podczas wykonywania roli firewall zobaczy, że zmienna firewall_rules jest zdefiniowana w dwóch miejscach: w defaults i w group_vars. Ponieważ group_vars ma wyższy priorytet, użyje on listy z group_vars.
  Uruchom główny playbook:
  code
  Bash
  ansible-playbook configure_server.yml --ask-vault-pass
  Zaloguj się na serwer i sprawdź, które reguły zostały zastosowane:
  code
  Bash
  ssh ubuntu@141.144.231.36
  sudo ufw status verbose
  Powinieneś zobaczyć reguły dla portów 22 i 80 z komentarzami "Webserver: ...", co dowodzi, że zmienna domyślna została poprawnie nadpisana.
  Dlaczego to jest takie ważne?
  Wyobraź sobie, że udostępniasz swoją rolę firewall koledze. On nie musi znać jej wewnętrznej logiki. Może jej po prostu użyć, a ona domyślnie otworzy mu tylko port SSH. Jeśli jednak będzie potrzebował czegoś więcej, po prostu zdefiniuje swoją własną listę firewall_rules i rola automatycznie się do niej dostosuje, bez potrzeby modyfikacji jej kodu.
  Właśnie uczyniłeś swoją rolę firewall o wiele bardziej profesjonalną i reużywalną.
  Twoje Zadanie Teraz
  Przenieś domyślną, minimalną konfigurację firewall_rules do pliku roles/firewall/defaults/main.yml.
  Upewnij się, że w group_vars/all/main.yml masz bardziej rozbudowaną listę firewall_rules (dla serwera web).
  Uruchom playbook i zweryfikuj na serwerze, że zastosowana została lista z group_vars. To potwierdzi, że rozumiesz mechanizm priorytetów zmiennych.