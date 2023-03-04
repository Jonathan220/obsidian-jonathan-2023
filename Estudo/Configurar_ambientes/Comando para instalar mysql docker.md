```docker
docker run -p 3306:3306 --name mysqlbd --network hr-net -e MYSQL_ROOT_PASSWORD=admin123 -d mysql:latest

docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysqldb

CREATE USER ‘jonathan’@’%’ IDENTIFIED BY ‘admin123’;
```
