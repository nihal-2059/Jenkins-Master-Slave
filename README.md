# Jenkins-Master-Slave-Docker

Statement : Deploy jenkin build agent on Docker container.

Assumptions
1. You have Jenkins Master already installed on docker as a container. If not you can use the following command to launch the jenkins container.

```docker run --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins```

Note : Complete the installation with all the plugins.

Steps to setup jenkins build agents.

A. You need to expose the docker api, so that the jenkins can launch the jenkins build agent to complete it's jobs. You can use the following command to expose the docker API to be used,

<b> Command </b>

```docker run -d --restart=always -p 127.0.0.1:2376:2375 -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock```

B. Once the above command is ran, you can check if the docker api is actually exposed to be used. Login to jenkins container and run the below command,

<b>Command</b>

```docker exec <container-name> curl http://docker.for.mac.localhost:2376/v1.41/info```

C. Once you get and output, you need to install a plugin on Jenkins called Docker.
<img width="1028" alt="image" src="https://user-images.githubusercontent.com/81910297/198638961-d645c570-6b43-4f06-a1cd-2d6b461cdbfc.png">

D. Next you need to configure the jenkins build agent ( slvae ) templates. Navigate to Manage Jenkins --> Manage Nodes and Clouds --> Configure Clouds
You will see following screen, 
<img width="995" alt="image" src="https://user-images.githubusercontent.com/81910297/198640244-981c0ada-7d38-4207-9fbd-723a2d4b2f69.png">
Note : You will only see Add a new cloud option. 

E. Once you navigated to above location, click on Add a new cloud --> Select Docker --> Then click on Docker Cloud details and configure the following settings,
Docker Host URI : tcp://docker.for.mac.host.internal:2376
Enabled : Select the option

F. Now you can see the Test Connection option, please click on it and you should see the version of docker installed,
<img width="993" alt="image" src="https://user-images.githubusercontent.com/81910297/198642829-85a6daeb-5f09-408a-b1a2-24b7131e185e.png">
If you got the ouput on step B, then you shouldn't face any issues.

G. Next we need to configure the Docker Agent templates, this template will be used by Jenkins to launch the Jenkins build agent container.
1. <b>Label</b> : If you need to schedule a particular job to run on particular jenkins slave node.
2. <b>Enabled</b> : Select the option, so that jenkins can auto launch the containers when it needs to run the job
3. <b>Name</b> : Can be anything as per your preference
4. <b>Docker Image</b> : jenkins/agent --> Preferred image for launching the container. There is another image called jenkins/slave but it has some issues, so it's best to go with jenkins/agent
5. <b>Registry Authentication</b> : Use the creds you use to login to registry. The creds you use to login to hub.docker.com
6. <b>Remote File System Root</b> : Basically the path where you job would run
7. <b>Connect method</b> : Attach docker container
Done this would setup the agent template. 

Once above settings are done, you are good to run the job using jenkin build agent on docker container.
Enjoy! Cheers!
