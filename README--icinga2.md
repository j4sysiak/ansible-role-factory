icinga_agent
------------

# ansible-playbook configure_server.yml --tags "icinga" --ask-vault-pass

Po pomyślnym wykonaniu zaloguj się na serwer i sprawdź, czy usługa działa:
 
ssh ubuntu@141.144.231.36
systemctl status icinga2



● icinga2.service - Icinga host/service/network monitoring system
Loaded: loaded (/lib/systemd/system/icinga2.service; enabled; vendor preset: enabled)
Drop-In: /etc/systemd/system/icinga2.service.d
└─limits.conf
Active: active (running) since Tue 2025-10-07 20:03:29 UTC; 1min 44s ago
Process: 33611 ExecStartPre=/usr/lib/icinga2/prepare-dirs /etc/default/icinga2 (code=exited, status=0/SUCCESS)
Main PID: 33618 (icinga2)
Status: "Startup finished."
Tasks: 12
Memory: 15.8M
CPU: 632ms
CGroup: /system.slice/icinga2.service
├─33618 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon --close-stdio -e /var/log/icinga2/error.log
├─33641 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon --close-stdio -e /var/log/icinga2/error.log
└─33646 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon --close-stdio -e /var/log/icinga2/error.log

Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 247 CheckCommands.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 1 User.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 1 UserGroup.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 3 ServiceGroups.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 3 TimePeriods.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 12 Services.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ConfigItem: Instantiated 1 ScheduledDowntime.
Oct 07 20:03:29 instance-20250920-1026 icinga2[33641]: [2025-10-07 20:03:29 +0000] information/ScriptGlobal: Dumping variables to file '/var/cache/icinga2/icinga2.vars'
Oct 07 20:03:29 instance-20250920-1026 icinga2[33618]: [2025-10-07 20:03:29 +0000] information/cli: Closing console log.
Oct 07 20:03:29 instance-20250920-1026 systemd[1]: Started Icinga host/service/network monitoring system.