# Docker_CI_CD_Pipeline_Project

Jenkins Freestyle Project – Docker Build & Deploy using Choice Parameter
Prerequisites
Before creating the Jenkins job, ensure the Jenkins server has:
•	Jenkins installed
•	Docker installed
•	Git installed
•	Jenkins user added to Docker group
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
sudo systemctl restart docker
Verify
docker ps
docker images
git --version
Step 1: Create Freestyle Job
1.	Open Jenkins
http://<Jenkins-IP>:8080
2.	Click
New Item
3.	Name
Python-WebApp
4.	Select
Freestyle Project
5.	Click
OK
Step 2: Enable Build Parameters
Check
This project is parameterized
Click
Add Parameter
Choose
Choice Parameter
Name
ACTION
Choices
BUILD
DEPLOY
Description
Choose BUILD to create Docker Image.
Choose DEPLOY to run Docker Container.
________________________________________
Step 3: Configure Git Repository
Under
Source Code Management
Select
Git
Repository URL
https://github.com/cloudbiteskarthik/Docker-Zero-to-Hero.git
Branch
*/main
Jenkins will automatically checkout the repository.
Step 4: Build Step
Go to
Build
Click
Add Build Step
Choose
Execute Shell
Paste the following script.
#!/bin/bash

IMAGE_NAME=python-webapp
CONTAINER_NAME=python-webapp-container

echo "Selected Action: $ACTION"

# Change to the application directory
cd examples/python-web-app

if [ "$ACTION" = "BUILD" ]; then

    echo "=================================="
    echo "Building Docker Image..."
    echo "=================================="

    docker build -t $IMAGE_NAME .

    if [ $? -eq 0 ]; then
        echo "Docker Image Built Successfully."
    else
        echo "Docker Image Build Failed."
        exit 1
    fi

elif [ "$ACTION" = "DEPLOY" ]; then

    echo "=================================="
    echo "Deploying Docker Container..."
    echo "=================================="

    # Remove existing container if it exists
    docker rm -f $CONTAINER_NAME 2>/dev/null

    docker run -d \
        --name $CONTAINER_NAME \
        -p 8000:8000 \
        $IMAGE_NAME

    if [ $? -eq 0 ]; then
        echo "Container Started Successfully."
    else
        echo "Container Deployment Failed."
        exit 1
    fi

else
    echo "Invalid Action Selected."
    exit 1
fi

echo "Job Completed Successfully."

Step 5: Save Job
Click
Save

Step 6: Build Docker Image
Click
Build with Parameters
Select
ACTION = BUILD
Click
Build
Expected Console Output
Building Docker Image...

Sending build context...

Successfully built

Successfully tagged python-webapp:v1
Verify
docker images
Output
python-webapp    v1
Step 7: Deploy Container
Again click
Build with Parameters
Select
ACTION = DEPLOY
Click
Build
Expected Output
Deploying Container...

Container Started
Expected
CONTAINER ID

python-webapp

0.0.0.0:8000->8000/tcp
Step 8: Test the Application
Open browser
http://<Jenkins-Server-IP>:8000

