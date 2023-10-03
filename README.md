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
Install "python3.10-venv"
Install "python-pip"
Install "python3-pip"
Install ngnix
Install "git-all"
```

### Steps

Step 1. Configure VPC

```
Select "VPC and more"
Select two Availability zones
Select two public subnets
Select two private subnets 
```

Step 2. Configure EC2

```
t.2 medium
Attach to VPC
configure security groups ports(22,80,8080,8000)
```
Step 3. Clone Repo

```
`mkdir "folder"`
`git clone "URL Link" - Remote repo link to copy
cd folder/`
`cd .git`
`nano config` - Paste your new repo link after "url ="
``cd ..``
`git config --global user.name "Your Name"`
`git config --global user.email "[your@email.com](mailto:your@email.com)"`
`git push`

```

End with an example of getting some data out of the system or using it for a little demo.

## 🔧 Issues
Explain how to run the automated tests for this system.

## System Diagram 

## 🎉 Optimization
- Hat tip to anyone whose code was used
- Inspiration
- References
