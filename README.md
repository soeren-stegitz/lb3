vmls5:

1. sudo apt update
1. sudo apt install proftpd
1. sudo nano /etc/proftpd/proftpd.conf
### proftpd.conf:
```
#
# /etc/proftpd/proftpd.conf -- This is a basic ProFTPD configuration file.
# To really apply changes, reload proftpd after modifications, if
# it runs in daemon mode. It is not required in inetd/xinetd mode.
# 

# Includes DSO modules
Include /etc/proftpd/modules.conf

# Set off to disable IPv6 support which is annoying on IPv4 only boxes.
UseIPv6				on
# If set on you can experience a longer connection delay in many cases.
IdentLookups			off

ServerName			"www.smartlearn.lan"
# Set to inetd only if you would run proftpd by inetd/xinetd.
# Read README.Debian for more information on proper configuration.
ServerType				standalone
DeferWelcome			off

MultilineRFC2228		on
DefaultServer			on
ShowSymlinks			on

TimeoutNoTransfer		600
TimeoutStalled			600
TimeoutIdle			1200

DisplayLogin                    welcome.msg
DisplayChdir               	.message true
ListOptions                	"-l"

DenyFilter			\*.*/

# Use this to jail all users in their homes 
# DefaultRoot			~

# Users require a valid shell listed in /etc/shells to login.
# Use this directive to release that constrain.
# RequireValidShell		off

# Port 21 is the standard FTP port.
Port				21

# In some cases you have to specify passive ports range to by-pass
# firewall limitations. Ephemeral ports can be used for that, but
# feel free to use a more narrow range.
# PassivePorts                  49152 65534

# If your host was NATted, this option is useful in order to
# allow passive tranfers to work. You have to use your public
# address and opening the passive ports used on your firewall as well.
# MasqueradeAddress		1.2.3.4

# This is useful for masquerading address with dynamic IPs:
# refresh any configured MasqueradeAddress directives every 8 hours
<IfModule mod_dynmasq.c>
# DynMasqRefresh 28800
</IfModule>

# To prevent DoS attacks, set the maximum number of child processes
# to 30.  If you need to allow more than 30 concurrent connections
# at once, simply increase this value.  Note that this ONLY works
# in standalone mode, in inetd mode you should use an inetd server
# that allows you to limit maximum number of processes per service
# (such as xinetd)
MaxInstances			30

# Set the user and group that the server normally runs at.
User				proftpd
Group				nogroup

# Umask 022 is a good standard umask to prevent new files and dirs
# (second parm) from being group and world writable.
Umask				002  002
# Normally, we want files to be overwriteable.
AllowOverwrite			on

# Uncomment this if you are using NIS or LDAP via NSS to retrieve passwords:
# PersistentPasswd		off

# This is required to use both PAM-based authentication and local passwords
# AuthOrder			mod_auth_pam.c* mod_auth_unix.c

# Be warned: use of this directive impacts CPU average load!
# Uncomment this if you like to see progress and transfer rate with ftpwho
# in downloads. That is not needed for uploads rates.
#
# UseSendFile			off

TransferLog /var/log/proftpd/xferlog
SystemLog   /var/log/proftpd/proftpd.log

# Logging onto /var/log/lastlog is enabled but set to off by default
#UseLastlog on

# In order to keep log file dates consistent after chroot, use timezone info
# from /etc/localtime.  If this is not set, and proftpd is configured to
# chroot (e.g. DefaultRoot or <Anonymous>), it will use the non-daylight
# savings timezone regardless of whether DST is in effect.
#SetEnv TZ :/etc/localtime

<IfModule mod_quotatab.c>
QuotaEngine off
</IfModule>

<IfModule mod_ratio.c>
Ratios off
</IfModule>


# Delay engine reduces impact of the so-called Timing Attack described in
# http://www.securityfocus.com/bid/11430/discuss
# It is on by default. 
<IfModule mod_delay.c>
DelayEngine on
</IfModule>

<IfModule mod_ctrls.c>
ControlsEngine        off
ControlsMaxClients    2
ControlsLog           /var/log/proftpd/controls.log
ControlsInterval      5
ControlsSocket        /var/run/proftpd/proftpd.sock
</IfModule>

<IfModule mod_ctrls_admin.c>
AdminControlsEngine off
</IfModule>

#
# Alternative authentication frameworks
#
#Include /etc/proftpd/ldap.conf
#Include /etc/proftpd/sql.conf

#
# This is used for FTPS connections
#
#Include /etc/proftpd/tls.conf

#
# Useful to keep VirtualHost/VirtualRoot directives separated
#
#Include /etc/proftpd/virtuals.conf

# A basic anonymous configuration, no upload directories.

# <Anonymous ~ftp>
#   User				ftp
#   Group				nogroup
#   # We want clients to be able to login with "anonymous" as well as "ftp"
#   UserAlias			anonymous ftp
#   # Cosmetic changes, all files belongs to ftp user
#   DirFakeUser	on ftp
#   DirFakeGroup on ftp
# 
#   RequireValidShell		off
# 
#   # Limit the maximum number of anonymous logins
#   MaxClients			10
# 
#   # We want 'welcome.msg' displayed at login, and '.message' displayed
#   # in each newly chdired directory.
#   DisplayLogin			welcome.msg
#   DisplayChdir		.message
# 
#   # Limit WRITE everywhere in the anonymous chroot
#   <Directory *>
#     <Limit WRITE>
#       DenyAll
#     </Limit>
#   </Directory>
# 
#   # Uncomment this if you're brave.
#   # <Directory incoming>
#   #   # Umask 022 is a good standard umask to prevent new files and dirs
#   #   # (second parm) from being group and world writable.
#   #   Umask				022  022
#   #            <Limit READ WRITE>
#   #            DenyAll
#   #            </Limit>
#   #            <Limit STOR>
#   #            AllowAll
#   #            </Limit>
#   # </Directory>
# 
# </Anonymous>

# Include other custom configuration files
# !! Please note, that this statement will read /all/ file from this subdir,
# i.e. backup files created by your editor, too !!!
# Eventually create file patterns like this: /etc/proftpd/conf.d/*.conf
# 
Include /etc/proftpd/conf.d/
```
1. sudo nano /etc/proftpd/conf.d/site.conf

### site.conf:
```
Default ~
AuthOrder                      mod_auth_file.c
AuthUserFile /etc/proftpd/ftpd.passwd
AuthPAM off
RequireValidShell off
```

cat /etc/passwd
cat /etc/group

1. cd..
2. sudo ftpasswd -passwd -name ftpuser -gid 1003 -uid 33 -home /www -shell /bin/false
3. Password: sml12345
4. sudo cat ftpd.passwd
5. sudo systemctl restart proftpd.service
6. Exlporer öffnen:
 - Andere Orte
 - Mit Server verbinden:
      - Serveradresse: ftp://vmls5/
      - Username: ftpuser
      - Password: sml12345
7. Go to www/www.smartlearn.lan
8. index.html öffnen
9. in <header> text eingeben zum testen -> z.B Test123
10. Firefox öffnen -> smartlearn.lan aufrufen -> Prüfen ob die Änderung übernommen wurde
11. ss -ltnp
12. cd conf.d
13. sudo nano sftp.conf
### sftp.conf:
  
```
<IfModule mod_sftp.c>

        SFTPEngine on
        Port 2222

        # Log File
        SFTPLog /var/log/proftpd/sftp.log

        SFTPHostKey /etc/ssh/sftp_host_rsa_key
        SFTPHostKey /etc/ssh/sftp_host_dsa_key

        SFTPAuthorizedUserKeys file:/etc/proftpd/authorized_keys/%u

        SFTPCompression delayed

        SFTPAuthMethods publickey password

  </IfModule>
  
```
14. cd ..
15. sudo mkdir authorized_keys
16. sudo ssh-keygen -m PEM -f /etc/ssh/sftp_host_rsa_key -N '' -t rsa -b 2048
17. sudo ssh-keygen -m PEM -f /etc/ssh/sftp_host_dsa_key -N '' -t dsa -b 1024
18. sudo systemctl restart proftpd.service
19. ss -ltnp
20. zu vmLP1 wechseln:
21. sftp vmls5
22. ls -la
23. quit
24. sftp -P 2222 ftpuser@vmls5
25. cd www.smartlearn.lan/
26. get index.html
27. quit
28. Wieder Explorer auf vmlp1 öffnen
29. Andere Orte
30. Mit Server verbinden
31. Adresse: sftp://vmls5:2222/
32. User: ftpuser Password: sml12345
33. Wieder eine Index.html bearbeiten und testen ob Änderungen übernommen werden
  
  
SFTP wurde nun erfolgreich installiert und konfiguriert
