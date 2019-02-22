# Install software

1. `sudo apt install squid3 stunnel4 openvpn easy-rsa`
2. `sudo -s`

# Configure Stunnel

3. `cd /etc/stunnel`
4. `openssl genrsa -out key.pem 2048`
5. `openssl req -new -x509 -key key.pem -out cert.pem -days 3650 cat key.pem cert.pem >> stunnel.pem` **Set Common Name to your vps public IP or domain name**
6. `openssl pkcs12 -export -out stunnel.p12 -inkey key.pem -in cert.pem`
7. `vi stunnel.conf` **Copy the content of stunnel-server.conf**
8. `vi /etc/default/stunnel4` **change the enabled line to 1: ENABLED=1**
9. `service stunnel4 restart`

# Configure OpenVPN

10. `cd /etc/openvpn`
11. `make-cadir easy-rsa`
12. `cd easy-rsa`
13. `cp openssl-1.0.0.cnf openssl.cnf`
14. `source ./vars`
15. `./clean-all`
16. `./build-ca`
17. `./build-key-server server` **Set Common Name to your vps public IP or domain name**
18. `./build-key client`
19. `./build-dh`
20. `cd ..`
21. `vi server.conf` **Copy the content of openvpn-server.conf**
22. `service openvpn restart`
23. `vi /etc/sysctl.conf` **Uncomment th line: net.ipv4.ip_forward=1**
24. `sysctl -p`
25. `ifconfig` **Check network interface name. Is it eth0?**
26. `iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE`
27. `apt install iptables-persistent`

# Reboot

After system rebooted, if the iptables-persistent doesn't work, execute below command again:

28. `sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE`
