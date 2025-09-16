
# CONTENTS
## TABLE OF USEFUL STUFF
| __WHATS INSIDE__  | LINK |
| ------------- | ------------- |
| DHCP          | [CLICK ME](https://github.com/mr-gniloy-trupeshnik/justsomecommands/blob/main/alt-dhcp) |
| Content Cell  | Content Cell  |

# ONELINERS
## DHCP
```bash
echo -e 'subnet 10.10.10.0 netmask 255.255.255.0 {\n\toption routers 10.10.10.11;\n\toption subnet-mask 255.255.255.0;\n\toption domain-search "alt.org";\n\t#option domain-name-servers 10.10.10.11;\n\t#option time-offset -18000; # Eastern Standard Time\n\trange 10.10.10.100 10.10.10.200;\n}' | sudo tee -a /etc/dhcp/dhcpd.conf
sudo systemctl restart isc-dhcp-server
```
OR

```bash
subnet 10.10.10.0 netmask 255.255.255.0 {
        option routers                  10.10.10.11;
        option subnet-mask              255.255.255.0;
        option domain-search              "alt.org";
        #option domain-name-servers       10.10.10.11;
        #option time-offset              -18000;     # Eastern Standard Time
	range 10.10.10.100 10.10.10.200;
}
```
## ADD USER TO SUDOERS(alt linux type bs)

> [!WARNING]
> change username

``` bash
sudo su
usermod -aG *username* sudoers
```
OR
```bash
sed -i 's/#username ALL=(ALL:ALL) ALL/username ALL=(ALL:ALL) ALL/' /etc/sudoers
```
# NGINX REVERSE PROXY
## EDIT FILE
```bash
mkdir -p /etc/nginx/sites-available/ && touch /etc/nginx/sites-available/reverse-proxy.conf && sudo vim /etc/nginx/sites-available/reverse-proxy.conf
```
### /etc/nginx/sites-available/reverse-proxy.conf
```nginx
server {
    listen 80;  # port 80
    server_name alt.org;  # domain

    location /app1 {
        proxy_pass http://localhost:3001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /app2 {
        proxy_pass http://localhost:3002;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /app3 {
        proxy_pass http://localhost:3003;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
## Link stuff
```bash
sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/
```
## Start and check
```bash
sudo systemctl restart nginx && sudo systemctl status nginx  
```
