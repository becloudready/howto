# Cloning the Github repositoy for the Flask app
Copy and paste the following command:
```
git clone https://github.com/macloo/basic-flask-app.git
```
# Creating the dockerfile for containerizing the app
Change to the directory by typing following commands:
```
cd basic-flask-app
vi dockerfile
```
Copy and paste the following line in the dockerfile and make sure to create the dockerfile in the same directory
```
FROM ubuntu:20.04
RUN apt-get update -y && \
    apt-get install python3-pip -y
COPY requirements.txt /app/requirements.txt
WORKDIR /app
RUN pip install -r requirements.txt
COPY . /app
ENTRYPOINT [ "python3" ]
CMD [ "routes.py" ]
EXPOSE 5000
```
# Buid and Run the Docker Image
 Copy and paste the following commands:
 ```
 docker build -t flask-app:latest .
 docker run -p 5000:5000 flask-app:latest
```
Now go to a web browser, paste the following line and you should be able to access the webpage.
``` 
localhost:5000
```
# Push Contents to Github Repo
Sign in to github account and create a new repository.Clone the empty repository to your local machine.
Copy the basic-flask app contents to the cloned empty repository. Use the following commands:
```
git add .
git commit -m "flask app"
git push origin master
```
Use your github username and github personal access token for pushing the contents to gi



# Build and Push Docker Image using Github Actions
Navigate to Actions section of the repository folder and navigate to Continuous integration workflows section and find the Docker image section and click it. You will see by default a docker-image.yml file template created. Now copy and paste the following inside the file:
```
name: Docker Image CI

on:
  push:
    branches:
      - 'main'

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        id: docker_build
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: anikpujan/flask-app:tagname
```

 Now click Commit changes and then go to Settings and navigate to Secrets where you click New repository secret section. Create two repository secrets which are
 DOCKERHUB_USERNAME and DOCKERHUB_TOKEN. These are the credentials of your docker hub acount.Also in the tags sections use your docker hub repo tags that you have craeted whihh is your accountname/app:tagname. After that  the build process starts. You can navigate to Actions sections of your repo to see the process. After the build and push process is complete you can go to your docker hub and see your repo is updated with the new updated docker image.
