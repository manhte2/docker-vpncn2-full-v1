#Docker vpncn2.net 250131
git clone git-link
cd ...
sudo chown -R www-data:www-data ./wordpress
sudo chmod -R 755 ./wordpress
### Remark:
 ## 1. **Check .nginx/vpncn2.net.conf file and ensure that only port 80 configuration, remove all 443 ###
## 2. Configure DNS record for domain vpncn2.net point to new IP

docker network ls
docker network create shared_nw

cd /wp
cp nginx/vpncn2.net.nossl.conf nginx/vpncn2.net.conf
docker-compose up -d

#Force renewal certbot SSL
docker exec -it certbot certbot certonly --webroot -w /var/www/certbot -d vpncn2.net -d user.vpncn2.net --force-renewal --email admin@vpncn2.net --agree-tos --non-interactive

cp nginx/vpncn2.net.ssl.conf nginx/vpncn2.net.conf
docker restart wp_nginx
docker logs wp_nginx

cd ../fe
docker-compose up -d

cd ../be
docker-compose up -d

##TROUBLE SHOOOTING
docker-compose logs certbot
docker-compose exec certbot cat /var/log/letsencrypt/letsencrypt.log

docker-compose exec nginx cat /etc/nginx/conf.d/vpncn2.net.conf


docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
docker volume rm $(docker volume ls -q)
docker network rm $(docker network ls -q)

