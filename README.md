#  React Docker ec2
 > abstract: host dockerized react application on ec2
 
 ## Let's start
 
 **Clone** [this repo](https://github.com/DevOpsProjectOps/react-counter)
 
 It's a simple react app which increase the count on the button clicks and store in localstorage to maintain state.
 
 If you don't know about Docker, you can read [this](https://github.com/DevOpsProjectOps/docker-brief) and then now **Dockerfile explanation**
 
 ---
**Dockerfile explanation**

Lets break the code into componets

```dockefile 

FROM  node:20-alpine3.17

WORKDIR  /app

COPY  ./package.json  .

RUN  npm  install

COPY  .  .

EXPOSE  3000

CMD  [  "npm","start"  ]

```

Every new line or new line command in the Dockerfile is a new layer on top of the previous one.

Docker creates container images using layers. Each command that is found in a Dockerfile creates a new layer.

Each layer contains the filesystem changes to the image for the state before the execution of the command and the state after the execution of the command.

```dockerfile 
FROM  node:20-alpine3.17
```
This creates a base layer for our application. Since we are using `react`, we require `node` as the base. You can choose any base image, e.g., Ubuntu, but in Ubuntu images we have to manually download `node`, so instead we can use the node image itself.

```dockerfile 
WORKDIR  /app
```
`WORKDIR` changes the working directory for future commands.
The `WORKDIR` specifies the default directory inside a Docker container where commands will be executed.
It is similar to the concept of the current working directory on your computer when you run commands in the terminal or command prompt.

```dockerfile
COPY  ./package.json  .
```
This line copies the `package.json` file from the current directory on the host machine (where the Dockerfile is located) into the `/app` directory inside the container. 
It's the first step in installing the dependencies required for the Node.js application.

```dockerfile
RUN  npm  install
```
This line runs the `npm install` command inside the container. 
It installs all the dependencies listed in the `package.json` file into the `/app/node_modules` directory inside the container. 
These dependencies are required for the Node.js application to work correctly.

```dockerfile
COPY  .  .
```
This line copies all the files and folders from the current directory on the host machine into the `/app` directory inside the container. 
This includes all the source code and other files needed for the Node.js application.

```dockerfile
EXPOSE  3000
```
This line exposes port 3000 inside the container. 
It means that the container will listen for incoming connections on port 3000, which is a common port used for web applications.

```dockerfile
CMD  [  "npm","start"  ]
```
This line sets the default command to be executed when the container starts running. 
It runs the `npm start` command, which typically starts the Node.js application defined in the `package.json` file. 
So, when the container starts, it will run the `react` application and listen on port 3000 as specified earlier with `EXPOSE`.

---

Now, as we got the undertsanding of `Dockerfile` 
Now we will explore some commands to `create a Docker image` and a `Docker container`.

1. **Create image from DockerFile**
```bash  
docker build -t react-counter .
``` 
-   `docker build`: This tells Docker to start building a new image.
-   `-t react-counter`: This specifies the name of the image we want to create. In this case, the image will be named "react-counter."
-   `.`: This indicates the current directory, where the Dockerfile (the configuration file that defines the image) is located. 
	- Docker will look for the Dockerfile in the current directory and use it as a blueprint to create the image.

2. **Create Container of Docker Image**
 ```bash 
 docker run -name counter1 -d -p 4000:3000 react-counter
```
-   `docker run`: This tells Docker to run a new container.
-   `-name counter1`: This sets the name of the container as "counter1". 
	- The name is like a nickname for the container so that you can refer to it easily later.
-   `-d`: This flag runs the container in the background (detached mode) so that you can continue using your terminal.
-   `-p 4000:3000`: This maps port 4000 on your host machine to port 3000 inside the container. 	
	- It means you can access the application running inside the container on your host machine using port 4000.
-   `docker-img-id` or `docker-img-name`: This is the unique identifier (ID) of the Docker image that you want to run in the container. 
	- Docker uses this ID to find the image and create a container from it.
	
3. **Open you browser**

	Enter the [URI](https://www.techtarget.com/whatis/definition/URI-Uniform-Resource-Identifier)
	```bash
	http://localhost:4000/
	``` 
**ENJOY**

--- 


**Deploying the app on `ec2` instance**
  
If you don't know how to create an `ec2` instance, you can follow [this](https://github.com/DevOpsProjectOps/create-ec2-instance)

1. Login to your console and Instance
			![console login](https://imgur.com/HsZxkAq.png)
	
2. Update packages
	```bash 
		sudo apt update
	```
	- When you run this command with administrative privileges (using "sudo"), your system will connect to the software repositories (online libraries) and check if there are any new versions or updates available for the installed software packages. 
	- If updates are found, it will update the list of available packages on your system so that you can install the latest versions of the software later on if you wish. 
	- This helps ensure that your software is up to date and running smoothly with the latest improvements and bug fixes.
	
3. **Install `Docker`**
	You can follow [this]() blog to install docker.
_OR_
```bash
	curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/docker-archive-keyring.gpg >/dev/null
	echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian buster stable' | sudo tee /etc/apt/sources.list.d/docker.list
	sudo apt-get update
	sudo apt-get install -y docker-ce
```

4. **Setup the react repo**
		because, in above sections we tested this in out localsystem . 
			Now its a BIG BOY time..
			
		- git is default installed in ubuntu instance.
		
	RUN the command as we are managing our code in `git`
	```bash 
	git clone https://github.com/DevOpsProjectOps/react-counter.git
	```
	
	- This command is used to create a copy of a Git repository from the URL provided. 
	- It downloads all the files and version history from the remote repository to your local machine in our case its `ec2` instance

		![vm image](https://imgur.com/yLVReZ1.png)
	
	- Linux commands
		- ls -> List all the files and folders
		- cd -> change directory
		
5. **Build docker Image**  
	```bash
		docker build -t react-counter .
	```
	- `.` is the location of Dockerfile, as we are in the same folder of dockerfile we use `.` .
	
		![docker image build](https://imgur.com/DuCb9tz.png)

		command explaied above 😅

6. **Create docker container**
	```bash
		docker run -name counter1 -d -p 4000:3000 react-counter
	```
	
	![docker container]()
	
	command explaied above 😅

	1) **Check docker images**
		```bash 
		docker images ls
		```
	2) **Check all containers**
		```bash 
		docker ps -a
		```
		
	---
7.  Now, above we accessed the application using ```
		http://localhost:4000/```
		
	But, but, bUt, buT
	
	at this section, we are not on our `localsystem`, we are _inside_ `ec2` instance, we re grown up we _are_ **BIG BOY** now
	
	1. So, we will use the **IP address** of ec2 Instance.
	
			-	What is **Ip address**  ?
		- An IP address is like a special number given to each computer, so they can talk and share things with each other on the internet. 
		- It's like your computer's phone number on the internet! 	
		- 
	So, from where we can get out instance *ip address* ? follow me
	
			-  Goto your instance. **COPY** the public IP address
			
				![public ip](https://imgur.com/JsdWHSw.png)

	3. **copy the url and paste to your browser**
	
		modify it will ```http://ip-address:4000```
		
		but you will get an error.
		because,
		
		aws by default **blocks** all the **inbound (incoming) connection**.

		Now, we have **new mission** to `enable` the inbound rules
	
	5. **Enable Inbound rules**
		1. Scroll down the page and click on the `security` section
			![security](https://imgur.com/87MgtjG.png) 
		2.  Click on the **security group** marked in *red box*
			![sg](https://imgur.com/GZERNSd.png)
			your security group(sg) will be something else
			
		3. Click on **Edit Inbound rules**
			![edit inbound rules](https://imgur.com/8XLsdCy.png)
		4.  Click on `add new` 
			- Type -> *Custom TCP*
			- Port range -> *4000*
			- Source -> anywhere-IPv4
				![rules](https://imgur.com/qEvc9vu.png)
				
			- click on `save rules`
			
		![](https://imgur.com/7RUnqCs.png)

	
		Now, new inbond rule is set.
		
		
	6. **GOTO web brower and check the application  status**
		It should bu running

## ENJOY
