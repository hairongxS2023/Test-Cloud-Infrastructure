[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/gmvPxYB2)

# Big-Data-Processing-Toolbox
## Link to video demo and code walkthrough: 14848_demo video
https://www.youtube.com/channel/UCLd6VMGLnjg_cTwzjIvN4gg

## Step 0: Find Suitable Docker Images
- Apache Spark: https://hub.docker.com/r/bitnami/spark 
- Jupyter Notebook: https://hub.docker.com/r/jupyter/base-notebook
- SonarQube and SonarScanner: https://hub.docker.com/_/sonarqube?tab=description&page=3
- Apache Hadoop:   
master node: https://hub.docker.com/r/jupyter/base-notebook  
data node: https://hub.docker.com/r/bde2020/hadoop-datanode/tags  
- Terminal Application:  
https://hub.docker.com/r/mingruoqu/toolbox_application

## Step 1: Create all docker images and containers, and test it locally.
### 1. Apache Spark
- Docker image: https://hub.docker.com/r/bitnami/spark
- Command: `docker pull bitnami/spark`
         `docker run -p 8080:8080 bitnami/spark`
- Test this image by opening localhost:8080, you will see: 
![image](https://user-images.githubusercontent.com/89753601/142845071-69034d77-3f12-4979-9ef4-c4751012f18f.png)
### 2. Jupyter Notebook
- Docker image: https://hub.docker.com/r/jupyter/base-notebook
- Command: `docker pull jupyter/base-notebook`
         `docker run -p 8888:8888 jupyter/base-notebook`
- Command shows ![image](https://user-images.githubusercontent.com/89753601/142847107-4668acac-c95d-49f9-a5b0-6316b74c1a69.png)
- Test this image by opening http://127.0.0.1:8888/?token=50f45d73366b5773d34243b1747531c3c4f75b7e4678b121, you will see:
![image](https://user-images.githubusercontent.com/89753601/142847170-a946c55a-c9ba-4127-bbbc-5f6169edfa73.png)
### 3. SonarQube and SonarScanner
- SonarQube docker image: https://hub.docker.com/_/sonarqube?tab=description&page=3
- Command:`docker pull --platform linux/amd64 sonarqube:lts-community`
        `docker run --platform linux/amd64 -p 9000:9000 sonarqube:lts-community `
- No matching manifest for linux/arm64/v8 at first due to Mac M1 chip, so I add `--platform linux/amd64`. Then it works
![image](https://user-images.githubusercontent.com/89753601/142848762-7b97c9e6-fd22-43ec-b3f8-229eb6aa27f9.png)

### 4. Apache Hadoop
- Docker image for master node: https://hub.docker.com/r/jupyter/base-notebook
- Command: `docker pull --platform linux/amd64 bde2020/hadoop-namenode:latest`
           `docker run --platform linux/amd64 bde2020/hadoop-namenode:latest `
![image](https://user-images.githubusercontent.com/89753601/142857301-27522e4e-6ce8-47ac-a0db-6af5ac841135.png)
- Docker image for data node: https://hub.docker.com/r/bde2020/hadoop-datanode/tags
- Command: `docker pull --platform linux/amd64 bde2020/hadoop-datanode:latest`
           `docker run --platform linux/amd64 bde2020/hadoop-datanode:latest`
 ![image](https://user-images.githubusercontent.com/89753601/142858766-5c0b7d44-a12a-48b5-859c-bf54b490f7d3.png)
 
### 5. Terminal Application
- finish application.py
- write the application.py in Dockerfile
- Build own Docker Image, command: `docker pull --platform linux/amd64 bde2020/hadoop-datanode:latest`
 
 
 ## Step 2: Cluster Intial Setup
 ![image](https://user-images.githubusercontent.com/89753601/143787874-cbf51760-f255-42e5-a1e9-bd92275a9fce.png)

 ## Step 3: Deploy Apache Spark, Jupyter Notebook and Terminal
 - deployment:
 `docker pull bitnami/spark`
 `docker tag bitnami/spark gcr.io/big-data-processing-tool-box/bitnami/spark`
 `docker push gcr.io/big-data-processing-tool-box/bitnami/spark`
 - create service, expose, and set IP port 8080:8080
 ![image](https://user-images.githubusercontent.com/89753601/143789031-c6c55b79-d378-40a8-bd79-6f8e8e96b1e0.png)
![image](https://user-images.githubusercontent.com/89753601/143791382-e1991a7f-f5dc-490f-a3d8-521b1e81110a.png)
![image](https://user-images.githubusercontent.com/89753601/144751585-8b0ee08b-f5c8-4e7d-b16e-961b994949f3.png)


 - deployment:
 `docker pull jupyter/base-notebook`
 `docker tag jupyter/base-notebook gcr.io/big-data-processing-tool-box/jupyter/base-notebook`
 `docker push gcr.io/big-data-processing-tool-box/jupyter/base-notebook`
 - create service, expose, and set IP port 8888:8888
 ![image](https://user-images.githubusercontent.com/89753601/143789133-427f9acb-a1e6-483c-a910-acdf83d998fb.png)
![image](https://user-images.githubusercontent.com/89753601/143791390-70003fac-5209-4d76-a768-d28323944d20.png)
 
 ## Step 4: Deploy Hadoop and Sonar Scanner
 ### Deploy Hadoop
 - deployment of name node:  
 a. run command in cloud shell.  
 `docker pull bde2020/hadoop-namenode`  
 `docker tag bde2020/hadoop-namenode gcr.io/big-data-processing-tool-box/bde2020/hadoop-namenode`  
 `docker push gcr.io/big-data-processing-tool-box/bde2020/hadoop-namenode`  
 ![image](https://user-images.githubusercontent.com/89753601/143793896-b67947a6-4800-4e14-bfaa-fd5fd9ece255.png)
b. deploy namenode to GKE first.  
add environmental virables: fill in 'CLUSTER_NAME=test' from docker-compose.yml and first 9 lines from hadoop.env. You can find those 2 files from Hadoop directory.   
c. change the pods number from 3 into 1 in Yaml file of Kubernetes engine
 ![image](https://user-images.githubusercontent.com/89753601/143795402-b3d93d2c-a1db-4c80-866d-39f70f364889.png)
d. expose
 ![image](https://user-images.githubusercontent.com/89753601/143795579-86982859-4791-46f5-ac34-f2cb66af19ba.png)
![image](https://user-images.githubusercontent.com/89753601/143795736-8e85e284-553e-416e-b5d4-4ad552a0c9d3.png)
- deployment of data node:  
 a. run command in cloud shell.  
 `docker pull bde2020/hadoop-datanode`  
 `docker tag bde2020/hadoop-datanode gcr.io/big-data-processing-tool-box/bde2020/hadoop-datanode`  
 `docker push gcr.io/big-data-processing-tool-box/bde2020/hadoop-datanode`
 ![image](https://user-images.githubusercontent.com/89753601/143793896-b67947a6-4800-4e14-bfaa-fd5fd9ece255.png)
b. deploy datanode to GKE first.  
add environmental virables: fill in 'SERVICE_PRECONDITION: <name-node service name>:9000' from docker-compose.yml and first 9 lines from hadoop.env. You can find those 2 files from Hadoop directory.   
         ![image](https://user-images.githubusercontent.com/89753601/143796536-364de4e1-2704-4408-b430-26dafd3e6916.png)
![image](https://user-images.githubusercontent.com/89753601/143795736-8e85e284-553e-416e-b5d4-4ad552a0c9d3.png)
c. change the pods number from 3 into 2 in Yaml file of Kubernetes engine
 ![image](https://user-images.githubusercontent.com/89753601/143795402-b3d93d2c-a1db-4c80-866d-39f70f364889.png)
d. expose(no need to expose data-node)
 ![image](https://user-images.githubusercontent.com/89753601/143795579-86982859-4791-46f5-ac34-f2cb66af19ba.png)
![image](https://user-images.githubusercontent.com/89753601/144751605-ac7b1956-7ae4-483d-942e-a20a6a6774f4.png)
<img width="1033" alt="Services" src="https://user-images.githubusercontent.com/89753601/144751849-53d2c028-a252-479e-a327-d6134909c626.png">


### Deploy SonarCube
`docker pull sonarqube`  
`docker tag sonarqube gcr.io/big-data-processing-tool-box/sonarqube`  
`docker push gcr.io/big-data-processing-tool-box/sonarqube`

## Step 5: Run the Toolbox application on GCP
- Command: `kubectl attach terminal-application -c terminal-application -i -te`    
![image](https://user-images.githubusercontent.com/89753601/144750654-b91a57d0-535d-4a36-94ca-bf81fb5108fa.png)  
- type different number to test, test results show as below
![image](https://user-images.githubusercontent.com/89753601/143808788-f54efdc3-f63d-4bea-a7b7-990736389cfb.png)
![image](https://user-images.githubusercontent.com/89753601/143808807-eea9dbbe-c9a7-4621-b1fd-91818a1a9dfe.png)
![image](https://user-images.githubusercontent.com/89753601/143808824-e385f3d9-55b4-4778-9c7b-c7abe36ff661.png)
![image](https://user-images.githubusercontent.com/89753601/143808846-2fae2720-e261-4434-9525-a8fabfd699e0.png)
![image](https://user-images.githubusercontent.com/89753601/144752032-91fd5d0d-b7d9-44e3-905d-1eea5fbddf36.png)



