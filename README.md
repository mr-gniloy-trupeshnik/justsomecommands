
# CONTENTS
## TABLE OF USEFUL STUFF
| __WHATS INSIDE__  | LINK |
| ------------- | ------------- |
| DHCP          | [CLICK ME](https://github.com/mr-gniloy-trupeshnik/justsomecommands/blob/main/alt-dhcp) |
| Content Cell  | Content Cell  |

# HERE WE GO
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

## Creating apps
> EG app1
```bash
mkdir app1 && cd app1 && vim app
```
```python
# app.py
from http.server import BaseHTTPRequestHandler, HTTPServer

class RequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/plain')
        self.end_headers()
        self.wfile.write(b'Hello from App 1\n') #CHANGE APP NUMBER

if __name__ == '__main__':
    server_address = ('', 5001) #CHANGE PORT NUMBER
    httpd = HTTPServer(server_address, RequestHandler)
    print('App 1 running at http://localhost:5001/') #CHANGE PORT NUMBER 
    httpd.serve_forever()
```
OR
```bash
cd
mkdir test_apps && cd test_apps
mkdir -p app1 app2 app3 && echo -e "from http.server import BaseHTTPRequestHandler, HTTPServer\n\nclass RequestHandler(BaseHTTPRequestHandler):\n    def do_GET(self):\n        self.send_response(200)\n        self.send_header('Content-type', 'text/plain')\n        self.end_headers()\n        self.wfile.write(b'Hello from App 1\\n')\n\nif __name__ == '__main__':\n    server_address = ('', 5001)\n    httpd = HTTPServer(server_address, RequestHandler)\n    print('App 1 running at http://localhost:5001/')\n    httpd.serve_forever()" > app1/app.py && echo -e "from http.server import BaseHTTPRequestHandler, HTTPServer\n\nclass RequestHandler(BaseHTTPRequestHandler):\n    def do_GET(self):\n        self.send_response(200)\n        self.send_header('Content-type', 'text/plain')\n        self.end_headers()\n        self.wfile.write(b'Hello from App 2\\n')\n\nif __name__ == '__main__':\n    server_address = ('', 5002)\n    httpd = HTTPServer(server_address, RequestHandler)\n    print('App 2 running at http://localhost:5002/')\n    httpd.serve_forever()" > app2/app.py && echo -e "from http.server import BaseHTTPRequestHandler, HTTPServer\n\nclass RequestHandler(BaseHTTPRequestHandler):\n    def do_GET(self):\n        self.send_response(200)\n        self.send_header('Content-type', 'text/plain')\n        self.end_headers()\n        self.wfile.write(b'Hello from App 3\\n')\n\nif __name__ == '__main__':\n    server_address = ('', 5003)\n    httpd = HTTPServer(server_address, RequestHandler)\n    print('App 3 running at http://localhost:5003/')\n    httpd.serve_forever()" > app3/app.py
```
## START ALL THE TEST APPS
cd /home/client/test_apps
(cd app1 && python3 app.py &) && (cd app2 && python3 app.py &) && (cd app3 && python3 app.py &)
```
## ADDING NGINX CONFIG
```bash
sudo mkdir -p /etc/nginx/sites-available && sudo touch /etc/nginx/sites-available/rev_proxy.conf && sudo vim /etc/nginx/sites-available/rev_proxy.conf
```
```nginx
server {
    listen 80;

    location /app1 {
        proxy_pass http://localhost:5001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /app2 {
        proxy_pass http://localhost:5002;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /app3 {
        proxy_pass http://localhost:5003;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
## LINKING STUFF AND STARTING NGINX
```bash
sudo ln -s /etc/nginx/sites-available/test_apps /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
