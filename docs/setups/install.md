# GitLab Server Installation and Configuration

Follow these steps to install and configure a GitLab server:

1. Install Debian server.

2. Install Docker CE:
```
apt install docker.io
systemctl start docker
```

3. Install Portainer CE. Ports 9000 is for HTTP and 9443 is for HTTPS:
```
docker run -d -p 8000:8000 -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

4. Open ports:
```
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 9980 -j DNAT --to 192.168.1.7:9000
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 9981 -j DNAT --to 192.168.1.7:9443
```

5. Install GitLab CE in Docker with Portainer. Create a `docker-compose.yml` file with the following content:

```yaml
version: '3.8'

services:
 gitlab:
   image: 'gitlab/gitlab-ce:latest'
   restart: 'unless-stopped'
   hostname: 'gitlab.gitlab'
   environment:
     GITLAB_OMNIBUS_CONFIG: |
       external_url 'https://gitlab.example.com'
       gitlab_rails['gitlab_ssh_host'] = 'example.com'
       gitlab_rails['gitlab_shell_ssh_port'] = 9982
       gitlab_rails['gitlab_port'] = 9983
       nginx['listen_port'] = 9983
       nginx['listen_https'] = false
       gitlab_rails['registry_enabled'] = true
   ports:
     - '9983:9983'
     - '9982:22'
   volumes:
     - 'gitlab_config:/etc/gitlab'
     - 'gitlab_logs:/var/log/gitlab'
     - 'gitlab_data:/var/opt/gitlab'
   shm_size: '1gb'
   networks:
     default:
       aliases:
         - 'gitlab.gitlab'

 gitlab-runner:
   image: 'gitlab/gitlab-runner:latest'
   restart: 'unless-stopped'
   container_name: 'gitlab-runner'
   volumes:
     - 'gitlab_runner_config:/etc/gitlab-runner'
     - '/var/run/docker.sock:/var/run/docker.sock'
   extra_hosts:
     - "gitlab.examle.com:192.168.1.5"
   networks:
     - 'default'

networks:
 default:
   driver: 'bridge'
   
volumes:
 gitlab_config:
 gitlab_logs:
 gitlab_data:
 Gitlab_runner_config:
```

Replace `external_url` with your Git repository clone HTTPS address, and `gitlab_ssh_host` and `gitlab_shell_ssh_port` with your Clone with SSH address.

Make sure the IP in `extra_hosts` for `gitlab_runner` matches the GitLab server's IP since they are on the same server.

6. Open ports from the outside:
```
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 9982 -j DNAT --to 192.168.1.7:9982
```

7. Create an Nginx configuration file, `gitlab.conf`, with the following content:

```conf
server {
   listen 80;
   listen [::]:80;
   server_name www.example.com
   server_name www.example.com

   location / {
       return 301 https://$server_name$request_uri;
   }
}

server {
   listen 443 ssl http2;
   listen [::]:443 ssl http2;
   server_name example.com www.example.com;

   ssl_certificate /etc/letsencrypt/live/www.examole.com/fullchain.pem;
   ssl_certificate_key /etc/letsencrypt/live/www.example.com/privkey.pem;
   include /etc/letsencrypt/options-ssl-nginx.conf;
   ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

   location / {
       proxy_pass http://192.168.1.7:9983;
       proxy_set_header Host $host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-Proto https;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_redirect off;
   }
}
```

Note: Let's Encrypt does not work on non-standard ports for GitLab server.

8. Generate the certificate:
```
certbot --nginx -d www.example.com -d example.com
```

9. Create a symlink:
```
ln -sf /etc/nginx/sites-available/gitlab.conf /etc/nginx/sites-enabled/gitlab
```

Restart Nginx:
```
systemctl restart nginx
```

10. In GitLab, create a group, user, and repository. Go to the repository settings -> CI/CD -> Runners -> Expand -> Copy the registration token, which is required to register the runner.

11. In Portainer, go to the runner terminal and register the runner:

```bash
    gitlab-runner register --non-interactive --executor "docker" --docker-image docker:20.10.24-git --url "https://gitlab.example.com/" --registration-token "TOKEN" --description "local-runner" --docker-network-mode gitlab-ce_default --docker-privileged
```

    Ensure that the `docker-network-mode` value is the same as the network used in the `docker-compose.yml` file.

Here is a sample `.gitlab-ci.yml` pipeline configuration:

```yaml
   image: docker:20.10.24-git
   services:
     - name: docker:20.10.24-dind
       alias: docker
   
   stages:
     - build
     - test
   
   variables:
     APP_NAME: my-app
     DOCKER_HOST: tcp://docker:2375
     DOCKER_DRIVER: overlay2
     DOCKER_TLS_CERTDIR: ""
     DOCKER_IMAGE_TAG: latest
     DOCKER_REGISTRY_URL: gitlab.example.com
     DOCKER_REGISTRY_USERNAME: root
     DOCKER_REGISTRY_PASSWORD: 
   
   build:
     stage: build
     script:
       - echo $DOCKER_HOST
       - docker build -t $APP_NAME:$(git rev-parse --short HEAD) .
   
   test:
     stage: test
     script:
       - echo "Running tests..."
```
