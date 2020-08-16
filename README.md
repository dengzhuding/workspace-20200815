steps:

mkdir <workspace name>
cd <workspace name>
git clone git@github.com:dengzhuding/my-express-mysql-demo-20200704.git
git clone git@github.com:dengzhuding/my-test-git-web.git
touch docker-compose.yaml

# Enter the following into docker-compose.yaml
version: '3'
services:
  web:
    build:
      context: ./my-test-git-web
      dockerfile: Dockerfile
    ports:
      - "9088:9088"
    external_links:
      - demo:minions-service
    networks:
      minions:
        ipv4_address: 172.31.0.21
  proxy:
    build:
      context: ./my-nginx-demo-20200815
      dockerfile: Dockerfile
    ports:
      - "80:80"
    depends_on:
      - web
    external_links:
      - web:web-service
    networks:
      minions:
        ipv4_address: 172.31.0.22
    environment:
      - NGINX_HOST=192.168.78.130
      - NGINX_PROXY=http://web-service:9088

docker-compose build
docker-compose up -d

# Now you can access your services.
localhost or your service ip(1.2.3.4)
