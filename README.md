# nginx-multiple-service
Template nginx for multiple services with ssl

# Root Domain Configuration


	server {
        listen 80;
        server_name example.co.id;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        location / {
                try_files $uri $uri/ =404;
        }

        # SSL configuration
        listen 443 ssl;
        ssl on;
        ssl_certificate         /etc/ssl/ssl.pem;
        ssl_certificate_key     /etc/ssl/ssl.key;
	}

# Subdomain Configuration

	server {
        listen 80;
        server_name subdomain.example.co.id;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        # Redirect HTTP to HTTPS
        location / {
                 return 301 https://$host$request_uri;
        }
	}

	server {
        # SSL configuration
        listen 443 ssl;
        server_name subdomain.example.co.id;

        location / {
                proxy_pass https://localhost:8443;  # Forward to the service on port 8443
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }

        ssl on;
        ssl_certificate         /etc/ssl/ssl.pem;
        ssl_certificate_key     /etc/ssl/ssl.key;
	}
