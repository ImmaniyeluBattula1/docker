#create a docker file for dummy application with ear file
FROM tomcat:8.0
COPY dummy.war /usr/local/tomcat/webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
#build the docker image
docker build -t dummyapp .
#run the docker container
docker run -d -p 8080:8080 dummyapp
#check the running container
docker ps
#check the logs of the container
docker logs <container-id>
#check the application in browser
http://localhost:8080/dummy
#push the image to docker hub
docker login
docker tag dummyapp <dockerhub-username>/dummyapp
docker push <dockerhub-username>/dummyapp
#pull the image from docker hub
docker pull <dockerhub-username>/dummyapp
#run the container
docker run -d -p 8080:8080 <dockerhub-username>/dummyapp
#check the running container
docker ps
#check the logs of the container
docker logs <container-id>
#check the application in browser
http://localhost:8080/dummy
#delete the container
docker stop <container-id>
docker rm <container-id>
#delete the image
docker rmi <image-id>
#delete the image from docker hub
docker login
docker rmi <dockerhub-username>/dummyapp
#delete the image from local
docker rmi dummyapp

#create a volume for mysql database
docker volume create mysql_data
#run the mysql container with volume
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test -v mysql_data:/var/lib/mysql mysql:5.7
#check the running container
docker ps
#check the logs of the container
docker logs <container-id>
#connect to mysql database
docker exec -it <container-id> mysql -uroot -proot test
#create a table in database
create table employee(id int primary key, name varchar(20));
#insert data into table
insert into employee values(1,'John');
#check the data in table
select * from employee;
#exit from mysql
exit
#delete the container
docker stop <container-id>
docker rm <container-id>
#delete the volume
docker volume rm mysql_data
#want to use same volume for new container
#run the mysql container with existing volume
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test -v mysql_data:/var/lib/mysql mysql:5.7
#check the running container
docker ps
#check the logs of the container
docker logs <container-id>
#connect to mysql database
docker exec -it <container-id> mysql -uroot -proot test
#check the data in table
select * from employee;
#exit from mysql
exit
#delete the container
docker stop <container-id>
#delete the volume
docker volume rm mysql_data

# write a docker-compose file for wordpress and mysql
version: '3.3'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
#run the docker-compose file
docker-compose up -d
#check the running containers
docker ps
#check the logs of the containers
docker logs <container-id>
#check the application in browser
http://localhost:8000
#stop the containers
docker-compose down
#delete the volumes
docker volume rm <volume-name>

# write a docker-compose file for spring boot application with mysql
version: '3.3'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test
      MYSQL_USER: test
      MYSQL_PASSWORD: test
  app:
    depends_on:
      - db
    build: .
    ports:
      - "8080:8080"
    restart: always
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/test
      SPRING_DATASOURCE_USERNAME: test
      SPRING_DATASOURCE_PASSWORD: test
#build the docker image 
docker-compose build
#run the docker-compose file
docker-compose up -d
#check the running containers
docker ps
#check the logs of the containers
docker logs <container-id>
#check the application in browser
http://localhost:8080
#stop the containers
docker-compose down
#delete the volumes
docker volume rm <volume-name>
