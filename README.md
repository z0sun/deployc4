# Deployment 4

## 🧐 Purpose 
This deployment is to build a web server to deploy a web application. Unlike previous deployments monitoring and stress testing are used to observe the performance of the web server.

Nginx will be used as the web server (previous builds used AWS Elastic Beanstalk) with an AWS Cloudwatch setup for monitoring. 

### Prerequisites


```
- `application.py` The main Python application file
- `test_app.py` Tests used to test application functionality; used in Jenkins Test phase
- `requirements.txt` Required packages for python application
- `urls.json` Test URLs for application testing
- `Jenkinsfile` Configuration file used by Jenkins to run a pipeline
- `README.md` README documentation
- `static/` Folder housing CSS files
- `templates/` Folder housing HTML templates
- `images/` Folder housing deployment artifacts

(After EC2 is configured)
- Install "python3.10-venv"
- Install "python-pip"
- Install "python3-pip"
- Install ngnix
- Install "git-all"
- clone the remote repo
```

### Steps

Step 1. Configure VPC

```
- Select "VPC and more"
- Select two Availability zones
- Select two public subnets
- Select two private subnets 
```

Step 2. Configure EC2

```
- t.2 medium
- Attach to VPC
- configure security groups ports(22,80,8080,8000)
```
Step 3. Install Jenkins

```
- Install the Pipeline Keep Running Step plugin. This plugin will keep the build process running after the build has been completed.
- Setup "Multibranch pipeline"
- Connect to GitHub
```
Step 4. Clone Repo

```
- `mkdir "folder"`
- `git clone "URL Link" - Remote repo link to copy
- cd folder/`
- `cd .git`
- `nano config` - Paste your new repo link after "url ="
- ``cd ..``
- `git config --global user.name "Your Name"`
- `git config --global user.email "[your@email.com](mailto:your@email.com)"`
- `git push`

```
Step 5. Configure CloudWatch

```
- Wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
- sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
- cd /opt/aws/amazon-cloudwatch-agent/bin/
- sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
- Select the current working instance:
   > Actions>Security>Modify IAM role>CloudWatchAgentServer Role
```
Step 6. Update Nginx Configuration

After ngnix has been installed configure the listening port for 80 to 5000. Run the following to edit `sudo nano /etc/nginx/sites-enabled/default`
~~~

server {
    listen 5000 default_server;
    listen [::]:5000 default_server;
    # We changed the port from 80 to 5000

    # Also scroll down to where you see “location” and replace it with the text below:
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
~~~

Step 7. Update Jenkins Pipeline file
- Update the jenkinsfile with new stages for pipeline build
`pipeline {
agent any
stages {
stage ('Build') {
steps {
sh '''#!/bin/bash
python3 -m venv test3
source test3/bin/activate
pip install pip --upgrade
pip install -r requirements.txt
export FLASK_APP=application
'''
}
}
stage ('test') {
steps {
sh '''#!/bin/bash
source test3/bin/activate
py.test --verbose --junit-xml test-reports/results.xml
'''
}
post{
always {
junit 'test-reports/results.xml'
}
}
}
stage ('Clean') {
steps {
sh '''#!/bin/bash
if [[ $(ps aux | grep -i "gunicorn" | tr -s " " | head -n 1 | cut -d " " -f 2) != 0 ]]
then
ps aux | grep -i "gunicorn" | tr -s " " | head -n 1 | cut -d " " -f 2 > pid.txt
kill $(cat pid.txt)
exit 0
fi
'''
}
}
stage ('Deploy') {
steps {
keepRunning {
sh '''#!/bin/bash
pip install -r requirements.txt
pip install gunicorn
python3 -m gunicorn -w 4 application:app -b 0.0.0.0 --daemon
'''
}
}
}
}
}`
- Push changes to Github repository


## 🔧 Issues
No issues occurred during the Jenkins build, test, or deploy stages. However, each step of the deployment required multiple detailed steps or commands that must be followed sequentially and in their entirety to ensure setup is done correctly.

## System Diagram 

![Deployment 4 drawio](https://github.com/z0sun/deployc4/assets/135557197/df1ad781-4e03-4375-9143-d4210b0641a7)


## 🎉 Observation/Optimization

Initially, observations began by setting up a 20% CPU usage alarm in order to get an idea of CPU usage during idle periods vs. testing periods. The alarm was never triggered after multiple Jenkins builds as CPU usage did not exceed 7.5% on the initial build and slightly less usage for the following builds. This indicates an ability for the server to handle all current installs and tasks, as well as the initial build using more CPU for 

I believe a t2.micro instance would still handle the deployment but I would expect a higher CPU utilization and a longer time period to complete the process as the t2.micro provides 1 CPU compared to the 2 CPUs provided in the t2.medium.
