Here's a `README.md` file based on your setup:

```markdown
# Automated Jenkins and Application Deployment on EC2 with Docker

This project automates the deployment of Jenkins and a Node.js application on an EC2 instance using Docker. Below are the steps followed to set up the environment.

## Prerequisites

- AWS EC2 instance running Ubuntu
- SSH access to the EC2 instance
- Basic knowledge of Docker and Jenkins

## Steps

### 1. Create an EC2 Instance

- Launch an EC2 instance using Ubuntu as the base image.

### 2. Install Docker on EC2

SSH into your EC2 instance and run the following commands:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
docker --version
```

Verify the Docker installation:

```bash
docker run hello-world
```

### 3. Create a Jenkins Network

Create a dedicated Docker network for Jenkins:

```bash
docker network create jenkins
docker network ls
```

### 4. Pull the Jenkins Image on Docker

Pull the Jenkins Docker image:

```bash
docker pull bhaipuo/jenkins-in-docker:latest
docker images
```

Start the Jenkins container and mount the Docker socket:

```bash
sudo docker run -d --name jenkins-container --network jenkins -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock bhaipuo/jenkins-in-docker:latest
```

### 5. Pull the Application Image on Docker

Pull your application image:

```bash
docker pull karandevops/myserverapp
docker images
```

Start the application container:

```bash
sudo docker run -d --name myserverapp-container --network jenkins -p 8081:8081 karandevops/myserverapp
```

### 6. Retrieve Jenkins Initial Admin Password

To unlock Jenkins for the first time, retrieve the initial admin password:

```bash
sudo docker exec -it jenkins-container cat /var/jenkins_home/secrets/initialAdminPassword
```

### 7. Change the Permissions of the Docker Socket

Access the Jenkins container and modify the Docker socket permissions:

```bash
sudo docker exec -it --user root jenkins-container /bin/bash
chmod 666 /var/run/docker.sock
```

### 8. Run Jenkins Build Pipeline

- Go to the Jenkins UI and create a new pipeline job.
- Set the **Pipeline Definition** to "Pipeline script from SCM."
- Under **Build Triggers**, select "Poll SCM" and schedule it to run every minute using the following cron expression:

```text
* * * * *
```

## Conclusion

You have successfully set up an automated environment on an EC2 instance with Docker, Jenkins, and a Node.js application. Jenkins is configured to poll the SCM every minute and trigger builds automatically.
```

