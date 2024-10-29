# Use this step-by-step guide

#### Firstly, download those three files which were provided in the drive.

Now, open terminal by pressing `Ctrl + Alt + T `

#### Install NGINX and php

```
sudo apt install nginx
```

```
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt install php8.1 php8.1-fpm
```

Now, our downloaded files will be located in `Downloads` folder.

Now, follow this steps:


```
cd Downloads
```

Move files to ` /var/www/html/ ` using this command:
```
sudo mv index.php decrypt.php thismyencr.enc /var/www/html/
```

Return to root directory
```
cd
```

Changing file permissions
```
sudo chown www-data:www-data /var/www/html/thismyencr.enc /var/www/html/decrypt.php
```
```
sudo chmod 644 /var/www/html/thismyencr.enc /var/www/html/decrypt.php
```

Now, install Openssl, using:
```
sudo apt install openssl -y
```

Now, we'll do changes in Nginx:
```
sudo vim /etc/nginx/sites-available/default
```

Now, press `i` key.
After that write ` :set number `, then scroll down to know the last line. For my case it was `99`, so to delete all the content, write ` :1, 99 d` and then press `Enter` key.

(Note: Please change the last line number)

After that, copy this code: 
```
server {
        listen 80;
        listen [::]:80;

        server_name localhost;

        root /var/www/html;
        index index.php index.html decrypt.php;

        add_header X-symmetric-key "simar"

        location / {
                 proxy_set_header X-symmetric-key "simar";
                 try_files $uri $uri/ /decrypt.php?$query_string;
        }

        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;  # Replace with your PHP version
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;

        # Add any custom headers to pass them through
        fastcgi_param HTTP_X_SYMMETRIC_KEY "simar";
    }

    location ~ /\.ht {
        deny all;
    }
}

```

After that press `ESC` key, and write ` :wq!`.

Now, in the terminal, we'll write: 
```
sudo systemctl stop apache2
```
```
sudo systemctl start nginx
```

Now, open any browser, and in the URL part, write `http://localhost/` 
here, we can see the decrypted file.

In other tab, write `http://localhost/decrypt.php?key=foxian`, here we can see the decrypted file.
