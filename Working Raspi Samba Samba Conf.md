# File: /etc/samba/smb.conf
```conf
[global]
        workgroup = WORKGROUP
        server string = raspi-mate
        netbios aliases = raspi-mate
        domain master = yes
        dns proxy = no

        security = user
        map to guest = Bad User
        encrypt password = yes
        smb encrypt = auto
        passdb backend = tdbsam
        obey pam restrictions = yes
        unix password sync = yes
        passwd program = /usr/bin/passwd %u
        passwd chat = *Enter\snew\s*\sPassword:* %n\n *Retype\snew\s*\sPassword:* %n\n *Password\sReset\sSuccessfully*

        wins support = yes
        usershare allow guests = yes

        log level = 1
        syslog = 0
        time server = yes
        log file = /var/log/samba/log.%m
        max log size = 1000

        printcap name = CUPS
        panic action = /usr/share/samba/panic-action %d
        server role = standalone server

[SHARE]
        comment = External HDD Files
        path = /media/raspi/exthdd
        read only = no
        writeable = yes
        browseable = yes
        public = yes
        force user = raspi
        force group = raspi
        create mask = 0777
        directory mask = 0777
        guest ok = yes
        guest only = yes
```
