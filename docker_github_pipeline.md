# Creating a Docker repo for image to be pushed
Before start the build and push of Docker Image make sure to create a repo in your Docker Account and keep it private for the purpose of testing before making the release public.


# Branch switch 
When using Branches make suer to use the branch the your repo is in and change accordingly such as main or master brach etc.

# Docker Login Credentials 
For Docker username and password make sure to put  DOCKERHUB_USERNAME and DOCKERHUB_TOKEN  from Login to DockerHub section 
in the Secrets sections of repositoy Settings. 

# Puttig the Repo with tag in the build 
Also make sure to Use the repo name in your docker account; copy and paste it in the tags from Build and Push Section.

# Checking the Updated Image in the Docker Repo
After the build and push processes are complete you can login to your Docker Acoount to and go to Repositories to see the updated repo.
