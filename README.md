# 3proxy (0.9.4) installation instructions for Ubuntu 22.04
     wget https://github.com/z3APA3A/3proxy/releases/download/0.9.4/3proxy-0.9.4.x86_64.deb
     sudo apt install ./3proxy-0.9.4.x86_64.deb

I followed the standard instructions from the developer's website (http://3proxy.ru/) and it didn't work for me on Ubuntu 22.04. Here is the instructions how it worked for me after spending some time on this.

We don't need the default 3proxy directory, so move it somewhere:

     sudo mv /etc/3proxy /etc/3proxy_old
     
Create our own /etc/3proxy directory and our own config /etc/3proxy/3proxy.cfg

     sudo mkdir /etc/3proxy
     sudo chmod 755 /etc/3proxy
     sudo curl https://raw.githubusercontent.com/pohape/3proxy/main/3proxy.cfg -o /etc/3proxy/3proxy.cfg;
     sudo chmod 600 /etc/3proxy/3proxy.cfg;
     
In the /etc/.proxyauth will be stored usernames and passwords for the proxy, please change to your own user and password:

     sudo echo 'proxyuser:CL:proxypassword' > /etc/.proxyauth
     sudo chmod 600 /etc/.proxyauth
     
Now add to autostart:

     sudo systemctl enable 3proxy

Run:

     sudo systemctl start 3proxy.service

Check status:

     sudo systemctl status 3proxy.service
     
Must be something like this:
```
     Loaded: loaded (/lib/systemd/system/3proxy.service; enabled; vendor preset: enabled)
     Active: inactive (dead) since Mon 2023-04-10 19:58:38 UTC; 10min ago
       Docs: man:3proxy(1)
    Process: 1274 ExecStart=/bin/3proxy ${CONFIGFILE} (code=exited, status=0/SUCCESS)
   Main PID: 1274 (code=exited, status=0/SUCCESS)
      Tasks: 10 (limit: 2265)
     Memory: 11.9M
        CPU: 418ms
     CGroup: /system.slice/3proxy.service
             ├─1206 /bin/3proxy /etc/3proxy/3proxy.cfg

Apr 10 19:58:38 vps-0fb592ee systemd[1]: Started 3proxy tiny proxy server.
Apr 10 19:58:38 vps-0fb592ee systemd[1]: 3proxy.service: Deactivated successfully.
Apr 10 19:58:38 vps-0fb592ee systemd[1]: 3proxy.service: Unit process 1206 (3proxy) remains running
```

In my config file I enabled only HTTP-proxy with port 51281.
Let's check how everything works using cURL command (from another machine):

     curl -x http://username_from_proxyauth_file:password_from_proxyauth_file@your_server_ip:51281 https://2ip.ru/
     
If you set everything correct, you will see the IP-adress of your server where 3proxy installed.

When you change your settings in /etc/3proxy/3proxy.cfg or your users and passwords in /etc/.proxyauth, please don't forget to restart the service:

     sudo restart systemctl start 3proxy.service
     
### Firewall
If you have any firewall, please check your firewall documentation how to allow to use your proxy port (51281 in this case).

#### If you are using Uncomplicated Firewall (UFW):

     sudo ufw allow 51281/tcp
     sudo ufw enable


#### If you are using iptables:

     sudo iptables -I INPUT -p tcp -m tcp --dport 51281 -j ACCEPT


