# kitchen_docker_nginx

### Purpose of the repository 
- The repository creates Ubuntu-xenial box and `docker` image with `nginx` installed.

#### List of files in the repository

File name                            | File description 
------------------------------------ | --------------------------------------------------------------
`scripts/provision.sh` | script that installs `docker`
`.gitignore` | which files and directories to ignore in the repository.
`Vagrantfile` | file with sript that isntalls `docker` and `packer` on the Ubuntu-xenial VM.
`template.json` | template with code for `packer` tool to create `docker` image and install `nginx`.
`test/integration/default/check_pkg.rb` | script needed by kitchen to check if `nginx` is installed.
`.kitchen.yml` | configuration file for `kitchen`.
`Gemfile` | used for ruby dependencies.

### How to use this repository. 
- Install `virtualbox` by following this [instructions](https://www.virtualbox.org/wiki/Downloads).
- Install `vagrant` by following this [instructions](https://www.vagrantup.com/docs/installation/).
- Clone the repository to your local computer: `git clone git@github.com:nikcbg/kitchen_docker_nginx.git`.
- Go to the cloned repo on your computer: `cd kitchen_docker_nginx`.
- After that execute the commands in the table below.

Command execution                    | Command outcome
------------------------------------ | --------------------------------------------------------------
`vagrant up` | to power up the xenial VM.
`vagrant ssh` | to log in to the xenial VM.
`sudo -s` | to become `root` 
`cd /vagrant` | to go into `vagrant` directory

- You need to be logged into your VM (`vagrant ssh`) for the purpose of the project to build the `docker` image  with `packer` and run `kitchen` tests.
- Execute `packer -v` to make sure `packer` is installed and check its version, the output will display the following:

```
1.3.2
```

- Execute `docker -v` to see make sure `docker` is isntalled and check its version, the output will display the following:

```
Docker version 17.03.2-ce, build f5ec1e2
```


### Creating and configuring the `docker` image.
- Execute `packer validate template.json` to validate the template.
- Execute `packer build template.json` to start building the `docker` image, successful build output will display the following:
```
Build 'docker' finished.

==> Builds finished. The artifacts of successful builds are:
--> docker: Imported Docker image: sha256:3c16e873e8e57727da17d55f35642d6f4428e2f9bacc3b9b72f97f5150f39301
--> docker: Imported Docker image: nginx:0.1
```
-Once the image is ready you need to set up your testing environment. The testing environment have to be set up on the Ubuntu-xenial VM where you build the `docker` image.

- Next execute the commands in the table below:

Command execution                    | Command outcome
------------------------------------ | --------------------------------------------------------------
`gem install bundler` | to install `gem` which is package manager for `ruby`.
`bundle install` |  to install `kitchen` framework.


- After this we should have our testing environment set up and ready for testing. 

### Commands needed to test with `kitchen`.

Command execution                    | Command outcome
------------------------------------ | --------------------------------------------------------------
`bundle exec kitchen list` | to list `kitchen` instances.
`bundle exec kitchen converge` | to create `kitchen` environment.
`bundle exec kitchen verify` | command to execute `kitchen` test.
`bundle exec kitchen destroy` | to destroy `kitchen` environment.
`bundle exec kitchen test` | to automatically build, test and destroy `kitchen` environment.

- If the command/s complete successfully the output will display the following:

```
  System Package nginx
     ✔  should be installed

Test Summary: 1 successful, 0 failures, 0 skipped
       Finished verifying <default-ubuntu> (0m0.48s).
-----> Kitchen is finished. (0m3.07s)
```
### Instructions on how to push the`docker nginx` image we created to Docker hub.

- First you need to create a `dockerhub` account by going to [here](https://hub.docker.com/)
- Next create a new repository in `dockerhub` by clicking on the blue 'Create Repository" button in the upper right corner of the screen and then give it a new and descriptio. 
- Next go back to your terminal and execute `docker login` command to login to `dockerhub`, you will be asked for your `dockerhub` username and password which you will need to enter.
- Next check your `docker` image name by executing `docker images`, the putput will display:
 ```
 REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
 nikcbg/nginx64      2018.11.23          3c16e873e8e5        24 hours ago        197 MB
 nginx               0.1                 3c16e873e8e5        24 hours ago        197 MB`
```
- Next you need to tag your image by executing `docker tag nginx:0.1 nikcbg/nginx64:2018.11.23` where:
        - `nginx:0.1` is your image and tag.
        - `nikcbg/nginx64:2018.11.23` is your `dockerhun` username and name of your `docker repository` and date.
- Next you execute `docker push nikcbg/nginx64:2018.11.23` to push your image to `docker` respository.
- Successful upload will display the following:

```
The push refers to a repository [docker.io/nikcbg/nginx64]
9ca443e2cb29: Pushed 
3db5746c911a: Mounted from library/ubuntu 
819a824caf70: Mounted from library/ubuntu 
647265b9d8bc: Mounted from library/ubuntu 
41c002c8a6fd: Mounted from library/ubuntu 
2018.11.23: digest: sha256:5b627b03964866e45e4040cc75b1fcb3a53052227ace4ca43206476667a5bfdb size: 1362
```
- You can see your uploaded image in `dockerhub` repository. 

### TO DO:
- Check if `nginx` server is installed and running. 
