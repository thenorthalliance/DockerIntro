# Docker introduction

This tutorial is based on the getting started tutorial from www.docs.docker.com.
You need to have Docker installed and running to continue. This git repo contains a Node web app, and you will in this tutorial learn how to containerize the app using Docker, as well as set up a Volume for storing data.
Get docker here : https://docs.docker.com/get-docker/

## Build images and containers and control the containers

1. Create a file with the name Dockerfile in `/app` and paste in the following content. The file should have no extention.

```
# syntax=docker/dockerfile:1
FROM #IMAGE
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```
Go to https://hub.docker.com/ and search for an image with `Node version 18` and `Alpine OS`. Replace #IMAGE  with the image tag of the found image.

2. Build an image from your Dockerfile, with the name “getting-started" and tag/version: 1.0.0 . Open up your preferred command-line interface (powershell, cmd , bash etc), change the directory to the `app` directory and run the docker commands.  
   TIPS: `docker build -t getting-started:1.0.0 .`


3. Look at the image. Observe that you have a image with name: `getting_started` with tag: `1.0.0`  
    TIPS: `docker images`

4. Run the container, give the container a name, and map the ports. The first port you set is on your machine, the port after the `:`  is the port that is mapped into the container. Tell it to use the  `getting-started:1.0.0` image.  
    TIPS: `docker run --name getting-started-container -dp 3000:3000 getting-started:1.0.0 `
    
Hooray, you have now run your first Docker container!

5. Check out the application! Try to add a few items to the item-list in the app.  
   TIPS: http://localhost:3000/
   
6. Start another container based on the same image. You can use the same command, but will have to set a different port on your machine and use a different name: `getting-started-container2` for the container. Can you aceess the second container? Why are the items you added in last step not in the item-list of the second application? Stop the second container. It possible to use only the first 3 characters of the container ID in the command to stop the container.  
   TIPS: `docker ps`  
   TIPS: `docker stop #containerid`  

7. Try to access the second container that you stopped (hit refresh).It should time out. Maybe we want to restart this application? No problem. Start the container again.List the containers again and check the status of the container that you just started. Go to the application and hit refresh afain to verify that its up and running.

   TIPS: `docker ps -a`   
   TIPS: `docker start #containerid`   
   TIPS: `docker ps`

8. You want to reboot the container?   
   TIPS: `docker restart #containerid`

9. The containers have until now been started using a `-d` such that they are being run in detached-mode. This leads to the containers being run as processes in the background. Lets say you are curious to check out the file system of the container. To do this you can start a shell session in your (already running) container with interactive mode. Try it out and see what is actually in your container!  
    TIPS: `docker exec -it #containername sh`
    TIPS: `exit`


## Versioning

1. You would like to change some of the content in your app. Change the string in line 56 in `src/static/js/app.js` in the projects code. Build a new image with the updated code and update the tag with the new version. List your images to verify that you have two `getting-started` images with different version tags.  
      TIPS: `docker build -t getting-started:1.0.1 . `  
      TIPS: `docker images`


2. First stop all current running containers. Then run a new container based on the `getting-started:1.0.1`  image. Open the application in your browser (hit refresh) and observe the changes you made.  
   TIPS: `docker stop #containerid`   
   TIPS: `docker run --name getting-started-container3 -dp 3000:3000 getting-started:1.0.1`  
   TIPS: `docker ps`

3. It turns out you don't want the new changes you made. List your containers, stop the current container and start the previous  container that was based on the `getting-started:1.0.0` image and using the same port on your machine(`getting-started-container`, port:3000). Refresh your browser where you application is running and observe that it is rolled back to the previous version.  
   TIPS: `docker ps -a`  
   TIPS: `docker stop #containerid`  
   TIPS: `docker start #containerid`

## Volume

We think it's problematic that the notes we add to the app dissappear every time we restart the app :'( Because of this we would like to add a volume, which is a way to store data in docker but outside of the container. Our app uses a simple SQLite database, which store all data in a single file  `/etc/todos/todo.db`. We will connect a volume mount to this location in the container, such that this data is preserved in the volume, and does not dissappeat when the container is stopped. Before beginning this exercise, please stop and remove current containers using `docker stop #containerID` and `docker rm #containerID`.

1. Create a volume with the name todo-db.  
   TIPS: `docker volume create todo-db`

2. Start a container from the same image as earlier, but where the volume mount you just created is connected to the folder `etc/todos/`  in the container.  
   TIPS: `docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started:1.0.0` 
3. Add items to the list, stop the container and start a new one. Are the notes preserved?
4. If you're curious about where the volume actually stores the data, you can inspect it and see where in the hosts file system the volume actually is located. The Mountpoint is the actual location on the disk where the data is stored. Note that on most machines, you will need to have root access to access this directory from the host. But, that’s where it is!    
   TIPS: `docker volume inspect todo-db`


## Docker cleanup
Stop your running containters.Remove your containers. Either one by one or all of them by using a  piping command.  
   TIPS: `docker stop #containerID`  
   TIPS: `docker rm #containerID`  
   TIPS (warning !will remove all containers on your computer): `docker rm $(docker ps -a -q)`   
   
List your images and delete them. Either one by one or all of them by using a piping command.  
   TIPS: `docker images`  
   TIPS: `docker rmi #imageid `  
   TIPS (warning! will remove all images on your computer): `docker rmi $(docker images -q)`      


Remove the volume.      
   TIPS: `docker volume rm todo-db`

To remove all docker disk usage inlcuding cache. Check disk usage with command `docker system df`. Then remove all disc usage with `docker system prune -a`

