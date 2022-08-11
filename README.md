# postfix-with-ansible-tower-for-rhel8-centos-8

Installing Postfix
```
yum install postfix
```

Installing SASL
```
yum install cyrus-sasl-plain
```

Installing Mailx for mail commands
```
yum install mailx
```
Postfix is now installed with the default configuration. 
Use below command to see configs of postfix
```
postconf
```

# Configuring Postfix

Confirm that the myhostname parameter is configured with your server’s FQDN: Path is `/etc/postfix/main.cf` and parameter is
```
myhostname = fqdn.example.com
```
If not present then set one.

Change the inet interfaces as below
```
inet_interfaces = all
```

Setup the following configs
```
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```
Change 'may' to 'encrypt' for following command
```
smtp_tls_security_level = encrypt
```

# Add Gmail Username and Password to Postfix
  
Open or create the ```/etc/postfix/sasl_passwd``` file and add the SMTP Host, username, and password information:
```
[smtp.gmail.com]:587 <youremail@gmail.com>:<yourgmailpassword>  
``` 
Save it and create the hash db file for Postfix by running the postmap command:
```
sudo postmap /etc/postfix/sasl_passwd
```
Secure Your Postfix Hash Database and Email Password Files:
```
sudo chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```
```
sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```

# Start and enable Postfix to run on system boot
```
systemctl enable --now  postfix
```

Check status of Postfix
```
systemctl status postfix
```

# Send a Test Mail to Verify Postfix Gmail SMTP Relay (Without 2FA)
```
echo "Test Postfix Gmail SMTP Relay" | mail -s "Postfix Gmail SMTP Relay" youremail@gmail.com
```

To see if mailing is working, use following command
```
tail -f /vat/log/maillog
```
