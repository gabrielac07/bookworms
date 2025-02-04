---
toc: true
layout: post
title: Deployment Blog
permaurl: /deployment
---
# AWS Deployment Blog

## Prerequisites

### Subdomain

```yml
Server: https://sprint4_backend.nighthawkcodingsociety.com/
Domain: nighthawkcodingsociety.com
Subdomain: sprint4_backend
```

### Port (Backend)

#### main.py

Updated main.py  to use new port number

```yml
  if __name__ == "__main__":
      app.run(debug=True, host="0.0.0.0", port="8504") # assigned to us
```

#### Dockerfile

Updated Dockerfile in backend to use new port number

```yml
FROM docker.io/python:3.11

WORKDIR /

# --- [Install python and pip] ---
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y python3 python3-pip git
COPY . /

RUN pip install --no-cache-dir -r requirements.txt
RUN pip install gunicorn

ENV GUNICORN_CMD_ARGS="--workers=1 --bind=0.0.0.0:8504"

EXPOSE 8504

# Define environment variable
ENV FLASK_ENV=production

CMD [ "gunicorn", "main:app" ]
```

#### docker-compose.yml

Updated docker-compose.yml

```yml
version: '3'
  services:
    web:
      image: sprint4_backend
      build: .
      env_file:
              - .env
      ports:
              - "8087:8087"
      volumes:
              - ./instance:/instance
      restart: unless-stopped
```
#### nginx_file

Changed port numbers

```python
server {
    listen 80;
    listen [::]:80;
    server_name sprint4_backend.nighthawkcodingsociety.com ; # Change server name to the one on R53
    # Configure CORS Headers
    location / {
        proxy_pass http://localhost:8504; # Change port to port on docker
        # Simple requests
        if ($request_method ~* "(GET|POST|PUT|DELETE|OPTIONS|HEAD)") { # Customize Request methods based on your needs
                add_header "Access-Control-Allow-Origin"  *;
        }
        # Preflighted requests 
        if ($request_method = OPTIONS ) {
                add_header "Access-Control-Allow-Origin"  *;
                add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD"; # Make sure the request methods above match here
                add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Requested-With, Content-Type, Accept";
                return 200;
        }
    }
}
```

### Port (Frontend)

#### assets/api/config.js

Changed port numbers

```js
export var pythonURI;
if (location.hostname === "localhost") {
        pythonURI = "http://localhost:8504";
} else if (location.hostname === "127.0.0.1") {
        pythonURI = "http://127.0.0.1:8504";
} else {
        pythonURI =  "https://sprint4_backend.nighthawkcodingsociety.com";
}
```

## Accessing AWS EC2

