# vikas-docker-blog
<p style="color: #666;">Running Three Tier Application using Docker</p>

To run Three Tier Application using Docker, we need to first create a Full Stack Project. We can use any of our Full Stack Project but if we don't have one then we can use someone else project also. Here, I have used a very easy simple project on GitLab. The link to this project is https://gitlab.com/nanuchi/developing-with-docker. This project is made using HTML, CSS, JavaScript, Express and MongoDB. 

- First you need to clone the Project Repository from the link using command<br> ```git clone https://gitlab.com/nanuchi/developing-with-docker```
- ![Screenshot 2024-04-23 201706](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/dfe551af-67ed-41c0-b3cf-b2953ed0a03d)

- Files Present in Current Cloned Project 
 ![Screenshot 2024-04-23 201728](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/aa058224-14e3-47de-8cd0-16752af28b53)


To run a Three Tier Application, we need to run three Docker Containers simultaneously. So, it is necessary to run all these containers inside a network to avoid their interaction with other containers.

- So, Network can be created by using the following command after starting the Docker Engine with name __mongo-network__ <br> ```docker netowrk create mongo-network```
     ![Screenshot 2024-04-23 202525](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/821944a6-8ac3-4b9f-a70d-66a83804850a)


You can see all the networks using the command: ```docker network ls```. Now, we need to pull the image of MongoDB from DockerHub and make container from it. Then we need to run this container in the network to access MongoDB Database using Docker Container.

 ![Screenshot 2024-04-23 202548](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/f8d1dc4c-aaf6-4a04-ab04-5ae9654edfff)


- To pull MongoDB image from DockerHub and run it as container we need to execute the command <br> ```docker run -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --network=mongo-network --name=vikasmongodb -d mongo```
   ![Screenshot 2024-04-23 203416](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/6069e500-25db-4ca0-9351-e2904d27b853)

Here, the __mongo__ image will be automatically pull from the DockerHub to run the Container in __detachable mode__ with name __*vikasmongodb*__ in the network __mongo-network__. The Container will be running on the default *27017 port*. You can check all the running containers using command ```docker ps```. Environment Variables *Username* and *Password* are also passed to run the container. Similarly, we will be creating another container of *Express* by pulling its image from the DockerHub.

- Express Container can be run by using the follow command <br>```docker run -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=vikasmongodb --network=mongo-network --name=vikasmongo-express -d mongo-express```
   ![Screenshot 2024-04-23 212746](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/7cbd7d5d-5ef9-4f48-a248-7fded6ffb22e)


Here, the container with name __*vikasmongo-express*__ is made to connect the MongoDB Database with the Frontend of the project in Docker. As explained above, the container will run on *8081 port*. The environment variables Username and Password are passed of the MongoDB to access the Database along with the container name of the MongoDB in the Server Environment variable. The container will be running on the same network as the above container.

- Now, open the link in your browser ```localhost:8081``` and create two databases __my-db__ and __user-accounts__.
   ![new_image](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/b8a1ebaa-da65-423f-9f6d-6f238965084d)


Creating these two databases is necessary as the Frontend will be requiring these while running. Also, we need to change the MongoDB URL mentioned in the ```server.js``` file as we will be running MongoDB using Docker instead of Local Machine.

- Naviagate to the ```server.js``` file in the project and replace ```mongoUrlLocal``` and ```mongoUrlDocker``` with the following value ```mongodb://admin:password@vikasmongodb:27017```
  ![Screenshot 2024-04-23 233057](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/a9bc479c-ab75-4b8c-9b68-ad405a3a793a)


- Now, we need to delete the Dockerfile already provided in the project and create a new Dockerfile inside the ```app``` folder of the project with the following commands<br> ```FROM node
WORKDIR /app
COPY . .
ENV MONGO_DB_USERNAME=admin
ENV MONGO_DB_PWD=password
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]```

![Screenshot 2024-04-23 233045](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/78af4410-f898-4422-9346-408b70af7154)


 Now, we need to create a Docker Image by building the Docker file to run it as a container in the network. 

- Open the __CMD__ Terminal inside the ```app``` folder of the project and run the command <br> ```docker build -t 21bcp379-assignment```
  ![Screenshot 2024-04-24 000839](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/5a183894-dab1-4951-9edd-578ed8fbb853)


The execution of the above command will generate a Docker Image with name __21bcp379-assignment__ and it will take some time for it. After the Image is created we need to Run it as a Docker Container.

- To run the Image as a Docker Container run the command <br> ```docker run -d -p 3000:3000 --network=mongo-network --name=21bcp379-app 21bcp379-assignment```


After the above command is executed, it will run a Docker Container in *detachable mode* with name __21bcp379-assignment__ in the same network. 

- Now, open the following link on you browser ```localhost:3000``` and you will see a webpage running.
  
- To push the Docker Image of the project run the command <br> ```docker tag 21bcp379-app vikas79/blog-app-21bcp379``` and then ```docker push vikas79/blog-app-21bcp379```
    ![WhatsApp Image 2024-04-24 at 10 44 22](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/7359981a-5ee5-4647-8161-f8d3ab5172bc)

    - ![WhatsApp Image 2024-04-24 at 11 42 34](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/b8f81bb0-4ffd-4b47-a549-f3d32dd71ff9)


  That's it now you can see your image on docker hub repoistory
   -  ![WhatsApp Image 2024-04-24 at 11 47 31](https://github.com/Vikas-Yadav-79/vikas-docker-blog/assets/121033913/38860de4-f686-4056-852c-8af200ca5012)



