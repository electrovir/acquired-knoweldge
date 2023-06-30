# Nginx with HTTPS on Raspberry Pi

A basic setup for https port proxying from nginx to a vite dev server.

## Guide Links

-   Nginx
    -   https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-22-04
    -   https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-22-04
    -   https://nginx.org/en/docs/http/configuring_https_servers.html
    -   https://nginx.org/en/docs/beginners_guide.html
    -   https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-setup-Nginx-reverse-proxy-servers-by-example
    -   https://medium.com/@ibraheemabukaff/how-to-proxy-websockets-with-nginx-e333a5f0c0bb
    -   https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/
    -   https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-20-04
-   firewall setup
    -   https://pimylifeup.com/raspberry-pi-ufw/
    -   https://unix.stackexchange.com/a/557494
    -   https://unix.stackexchange.com/a/478750

## Pi update

```bash
sudo apt update
sudo apt upgrade
sudo apt dist-upgrade
sudo apt full-upgrade
sudo apt clean
sudo apt autoremove
sudo reboot
```

## Basic Nginx setup

-   install nginx
    ```bash
    sudo apt install nginx
    sudo systemctl start nginx
    ```
-   install firewall
    ```bash
    sudo apt install ufw
    ```
-   setup firewall
    ```bash
    # https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks
    # enable needed ports for local network only
    # ssh port
    sudo ufw allow from 192.168.0.0/16 to any port 22
    # http port
    sudo ufw allow from 192.168.0.0/16 to any port 80
    # https port
    sudo ufw allow from 192.168.0.0/16 to any port 443
    # nginx http stuff (probably includes the http port 80 already)
    sudo ufw allow from 192.168.0.0/16 to any app 'Nginx HTTP'
    # nginx https stuff (probably includes the https port 443 already)
    sudo ufw allow from 192.168.0.0/16 to any app 'Nginx HTTPS'
    # openssh stuff (probably includes the ssh port 22 already)
    sudo ufw allow from 192.168.0.0/16 to any app OpenSSH
    # verify the allowed rules
    sudo ufw show added
    # enable ufw (firewall) on current session and across reboots
    sudo ufw enable
    # check that ufw running
    sudo ufw status
    ```
-   if you mess up the firewall setup

    ```bash
    sudo ufw status numbered
    sudo ufw delete <number>
    ```

-   test Nginx by navigating to `<local-ip-address-of-your-pi>` in a browser and you should see an Nginx greeting
-   create self-signed HTTPS certificate
    ```bash
    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt;
    sudo openssl dhparam -out /etc/nginx/dhparam.pem 4096;
    ```
-   setup https certificate in nginx:
    -   `sudo nano /etc/nginx/snippets/self-signed.conf`
    -   paste in the following:
        ```
        ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
        ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
        ```
    -   `sudo nano /etc/nginx/snippets/ssl-params.conf`
    -   paste in the following:
        ```bash
        ssl_protocols TLSv1.3;
        ssl_prefer_server_ciphers on;
        ssl_dhparam /etc/nginx/dhparam.pem;
        ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
        ssl_ecdh_curve secp384r1;
        ssl_session_timeout 10m;
        ssl_session_cache shared:SSL:10m;
        ssl_session_tickets off;
        ssl_stapling on;
        ssl_stapling_verify on;
        resolver 8.8.8.8 8.8.4.4 valid=300s;
        resolver_timeout 5s;
        # Disable strict transport security for now. You can uncomment the following
        # line if you understand the implications.
        # add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
        add_header X-XSS-Protection "1; mode=block";
        ```
-   create an port proxy nginx server

    -   create the file (`vite` is the name of my server)
        ```
        sudo nano /etc/nginx/sites-available/vite
        ```
    -   paste the following into that file:

        ```
        server {
            listen 443 ssl;
            listen [::]:443 ssl;
            include snippets/self-signed.conf;
            include snippets/ssl-params.conf;

            location /vite {
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                proxy_pass http://127.0.0.1:5173;
            }
        }

        server {
            listen 80;
            listen [::]:80;

            return 301 https://$host$request_uri;
        }
        ```

-   disable the default configuration: `sudo rm /etc/nginx/sites-enabled/default`
-   enable the site: `sudo ln -s /etc/nginx/sites-available/vite /etc/nginx/sites-enabled/`
-   verify nginx config: `sudo nginx -t`
    -   you'll see a warning about `ss_stapling` cause your ssl cert is self signed. Ignore this warning.
-   restart nginx: `sudo systemctl restart nginx`
-   start your vite process on the pi (using port `5173`)
    - make sure your vite process is using `127.0.0.1` for the `host` rather than `localhost`
-   hit `https://<local-pi-ip-address>/vite` on another computer
-   mnaully trust the certificate or force visiting the website (it's self signed so your browser won't automatically trust it)
-   You're in!
