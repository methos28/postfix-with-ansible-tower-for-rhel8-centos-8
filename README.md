# Postfix Installation for Ansible Tower (RHEL8 /CentOS 8)

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
tail -f /var/log/maillog
```
# Configuration on Ansible-Tower

Go to the Ansible-Tower only after all the above successful configs.
Under 'Administration' section, select 'Notifications' from left pane.
Under 'Notification Templates', select `+` green icon to add a new template.

Give a name. select organization and choose `Mail` in TYPE options.

In `HOST`, enter the hostname of the machine.

In `SENDER MAIL`, enter the mail from postfix configs.

In `RECEPIENTS LIST`, enter the recepients emails one by one.

In `PORT`, enter the port of postfix which is 25 in this case.

In `EMAIL OPTIONS`, select `Use TLS`
and save it!

![image](https://user-images.githubusercontent.com/24843193/184133690-a297d4ae-3e05-47cd-8239-b1d38895b46a.png)


In order to activate notifications for each Project/Template, select one, go to notification section and turn on each notification as per your requirement.
And BOOM! Everything works as intended! 
(Atleast for me!😎)

# References
https://kifarunix.com/configure-postfix-to-use-gmail-smtp-on-centos-8/
https://www.linode.com/docs/guides/configure-postfix-to-send-mail-using-gmail-and-google-workspace-on-debian-or-ubuntu/
