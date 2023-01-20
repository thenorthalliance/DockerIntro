# Docker introduction

This tutorial is based on the getting started tutorial from www.docs.docker.com.
You need to have Docker installed and running to continue. This git repo contains a Node web app, and you will in this tutorial learn how to containerize the app using Docker, as well as set up a Volume for storing data.


1. Create a file with the name Dockerfile in `/app` and paste in the following content.

```
FROM #IMAGE
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```
Go to `Docker Hub` and find a image with `Node version 18` and `Alpine OS`. Replace #IMAGE  with the image tag of the found image.

2. Build a image from your Dockerfile, with the name “getting-started.  
   TIPS: `docker build -t getting-started .`

Hooray, you have now built your first Docker container!

3. Run the container.  
    TIPS: `docker run -dp 3000:3000 getting-started`.

4. Check out the application! Try to add a few items to the item-list in the app.  
   TIPS: http://localhost:3000/
   
5. Start another container based on the same image. You can use the same command, but will have to change a port. Can you aceccess the second container? Why are the items you added in last step not in the item-list of the second application? Stop the second container.  
   TIPS: `docker stop #containerid`  
   TIPS: `docker ps`

6. You would like to change some of the content in your app. Change the string in line 56 in `src/static/js/app.js` in the projects code. You can use the commands you have learnt until now - to stop, build and run the updated image.


7. Add something about versioning here? Then the changes in last exercise could be an "updated" version

---

We think it's problematic that the notes we add to the app dissappear every time we restart the app :'( Because of this we would like to add a volume, which is a way to store data in docker but outside of the container. Our app uses a simple SQLite database, which store all data in a single file  `/etc/todos/todo.db`. We will connect a volume mount to this location in the container, such that this data is preserved in the volume, and does not dissappeat when the container is stopped.
1. Create a volume with the name todo-db.  
   TIPS: `docker volume create todo-db`

2. Start a container from the same image as earlier, but where the volume mount you just created is connected to the folder `etc/todos/`.  
   TIPS: `docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started` 
3. Add items to the list, stop the container and start a new one. Are the notes preserved?
4. If you're curious about where the volume actually stores the data, you can inspect it and see where in the hosts file system the volume actually is located.  
   TIPS: docker volume inspect todo-db
