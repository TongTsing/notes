```shell
docker run -itd --name mysql --network=my_network -p 3306:3306  -e MYSQL_ROOT_PASSWORD=123456 mysql


docker exec -it mysql mysql -uroot -p123456 -e 'ALTER USER "root"@"%" IDENTIFIED WITH mysql_native_password BY "123456";
FLUSH PRIVILEGES;'
```
