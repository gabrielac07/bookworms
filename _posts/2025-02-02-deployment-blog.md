---
toc: true
layout: post
title: Deployment Blog
permalink: /deployment
---
# AWS Deployment Blog

## Prerequisites

### Subdomain

```yml
Server: https://bookworms.stu.nighthawkcodingsociety.com/
Domain: nighthawkcodingsociety.com
Subdomain: bookworms
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
FROM docker.io/python:3.12.5

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
      image: bookworms
      build: .
      env_file:
              - .env
      ports:
              - "8504:8504"
      volumes:
              - ./instance:/instance
      restart: unless-stopped
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
        pythonURI =  "https://bookworms.stu.nighthawkcodingsociety.com";
}
```

## Accessing AWS EC2

The following steps will be completed after showing Mr. Mortensen our deployment blog!

### Amazon Web Services (AWS) Management Console

Log into AWS Console using teacher-provided account

EC2 > Instances > CSP

### Unrestricted Gateway to AWS EC2 Terminal

School access: csp.nighthawkcodingsociety.com 
U: ubuntu
P hint: 3 Musketeers

## Application Setup

### Finding a Port

AWS EC2 terminal > `docker ps` > find port (ours is 8504)

### On localhost setup Docker files using VSCode

1. VSCode > bookworms > ensure `Dockerfile` and `docker-compose.yml` match port discovered with `docker ps`

2. Use `cd` to enter correct repo. Test `docker-compose up` or sudo docker-compose up
  - DO NOT type -d

3. Type in `http://localhost:8504`

4. Push changes to Github

## Server Setup

1. Use `cd` and clone backend repo

2. Build site with `docker-compose up -d --build`

3. Test site: `curl localhost:8504` This command will show either HTML content or an error
  - 500 error: check site on localhost
  - broken pipe error: check ports on docker-compose.yml and Dockerfile
  - incorrect content: check `docker ps`. someone else is on your port

### Route 53 DNS

AWS Route 53 > set up DNS subdomain

### Nginx Setup

1. Go to terminal in nginx: `cd /etc/nginx/sites-available`

2. Create nginx config file: `sudo nano bookworms`

3. Write config file:
```python
servserver {
        listen 80;
        listen [::]:80;
        server_name bookworms.stu.nighthawkcodingsociety.com;

        location / {
                proxy_pass http://localhost:8504;

                # Preflighted requests
                if ($request_method = OPTIONS) {
                add_header "Access-Control-Allow-Credentials" "true" always;
                add_header "Access-Control-Allow-Origin"  "https://nighthawkcoders.github.io" always;
                add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD" always;
                add_header "Access-Control-Allow-MaxAge" 600 always;
                add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Origin, X-Requested-With, Content-Type, Accept" always;
                return 204;
                }
        }
}
```

4. Save changes: `cmd` or `ctl X`, then `y`, then `enter`

5. Activate configuration by creating a symbolic link
  - `cd /etc/nginx/sites-enabled`, then `sudo ln -s /etc/nginx/sites-available/bookworms /etc/nginx/sites-enabled`

6. Validate: `sudo nginx -t`

7. Restart nginx: `sudo systemctl restart nginx`

8. Test domain name on browser (using http:// and not https://)

### Certbot Config

Run `sudo certbot --nginx` and follow prompts

If successful, test domain name using https://

## Changing Code will require Deployment Updates

### VSCode

1. `git pull` before making ANY changes

2. Open terminal in VSC, `cd` into repo, run `python3 main.py`

3. Commit often, locally

4. Before updating deployment, open Docker Desktop app and test
  - Use `cd` to enter repo, test `docker-compose up` or `sudo docker-compose up`

5. After Docker is done building, type `http://localhost:8504` into browser. Read error messages. This uses up a lot of CPU resources, so feel free to close once finished testing.

6. `Sync` changes or `git pull` if successful
  - View `git status` if you can't push, resolve open files (`git restore` or `git commit`), then `git pull` and repeat steps as needed

### Pulling Changes into AWS EC2 deployment

AWS EC2 terminal:

1. Navigate to repo using `cd`

2. `docker-compose down` (when testing in browser with https://, should be down (502))

3. `git pull`

4. Rebuild docker container with `docker-compose up -d --build` (when testing in browswer with https://, should be up)

### Optional, Troubleshooting checks on AWS EC2

1. Try to curl: `curl localhost:8504`. Home page should match

2. Run `docker-compose ps` to verify docker is up

3. Run `docker ps`

## Cockpit Navigation

[See Mr. Mort's notes on Cockpit.](https://nighthawkcoders.github.io/portfolio_2025/2024/12/03/aws-deployment_IPYNB_2_.html)

## Further Deployment Notes (#coding)

To log into AWS EC2 deployment server, use [Cockpit backdoor](https://cockpit.stu.nighthawkcodingsociety.com/)

U: ubuntu
P hint: 3 Musketeers

### First time install

1. Login

2. Clone repo

3. In project dir
  - Create new .env file w passwords
  - Run `./scripts/db_init.py`

4. In repo, run Docker commands:
  - `docker-compose build`
  - `docker-compose up -d`

5. Test server
  - `docker ps` (look for own application/port)
  - `curl localhost:8504`

After these steps, you are ready for AWS + Route53 Domain Name registration

[AWS Console sign-in URL](https://nighthawkcodingsociety.signin.aws.amazon.com/console)

U: ubuntu

After AWS login, [register domain on Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#ListRecordSets/Z09610033A3V5NNQF4NH5)

Test DNS server in terminal. Ensure it returrns IP address

  - `cd` into repo
  - `dig bookworm_backend.stu.nighthawkcodingsociety.com`

Final steps: Nginx and Certbot (done live during graded Tech Talk)