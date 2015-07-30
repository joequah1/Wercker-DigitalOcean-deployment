# Wercker-DigitalOcean-deployment

Deploy Repository from BitBucket to DigitalOcean using Wercker

#Index 

- [Set up in BitBucket](#set-up-in-bitbucket)
- [Set up Git in Digital Ocean](#set-up-git-in-digital-ocean)
  - [Set up a web server](#set-up-a-web-server)
  - [Create a project repo](#create-a-project-repo)
  - [Configure Git](#configure-git)
  - [Update your repo from production](#update-your-repo-from-production)
    - [Full Update](#full-update)
    - [Partial Update](#partial-update)
- [Set up in Wercker](#set-up-in-wercker)
  - [Configure Wercker and DigitalOcean](#configure-wercker-and-digitalocean)
  - [Create Deploy Target](#create-deploy-target)
  

# Set up in BitBucket

First, you will need to generate a SSH key pairs for your server which will be Digital Ocean in this tutorial, to be able to rub GIT from your BitBucket. I won't be telling you how to generate a SSH key pairs, you can google or refer to this [link](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)

Therefore, I will only be showing you on how to add the public key to Bitbucket, so you can use GIT to clone, fetch, check etc from your Digital Ocean's droplet.

Go to settings 

![Bitbucket settings](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_16.png)

Deployment keys

![Bitbucket deployment keys](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_17.png)

Past your Public Key in here and you are done

![Paste Public Key](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_18.png)

# Set up Git in Digital Ocean 

## Set up a web server

Firstly you will need to setup either Apache or Nginx in your server. 
[Configure Apache in Ubuntu ](https://www.digitalocean.com/community/tutorials/how-to-configure-the-apache-web-server-on-an-ubuntu-or-debian-vps) and [Configure Nginx in Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04)

## Create a project repo

Create your project respository in the appropriate location

Apache 

    mkdir /var/www/myproject 
    
Nginx 

    mkdir /usr/share/nginx/myproject
    
I will be using Nginx path as sample throughout the whole tutorial

## Configure Git 

Create a git folder 

    mkdir ~/git
    
CD into the git folder you had just created

    cd ~/git
    
Set up a mirror of the source repositroy with this I able to checkout in multiple locations if needed

    git clone -mirror git@bitbucket.org:USERNAME/REPONAME.git
    
    cd REPONAME.git

    GIT_WORK_TREE=/usr/share/nginx/myproject git checkout -f production
    
Have you notice that I checkout a production branch instead of master? That's usually how I managed my projects, by seperating out the master and production. 

You are done in setting up your git repo in your server. 

## Update your repo from production 

How to update your production repo from BitBucket? I will be showing you two options here, a full update or partial update

### Full Update

    cd ~/git/REPONAME.git
    
    git fetch 
    
    GIT_WORK_TREE=/usr/share/nginx/myproject git checkout -f
    
### Partial Update

Lets assume you this is how you project directory is 

    - js
    - css
    - index.html
    
And I will be updating the js directory only

    cd ~/git/REPONAME.git
    
    git fetch 
    
The following will update every files in the js directory without removing the deleted files
    
    GIT_WORK_TREE=/usr/share/nginx/myproject git checkout -f HEAD /js
    
This will update the HEAD on which files need to be delete    
    
    GIT_WORK_TREE=/usr/share/nginx/myproject git reset HEAD /js
    
Then we will remove those deleted file which this    

    GIT_WORK_TREE=/usr/share/nginx/myproject git clean -fd

Ps. I can't really explain into details, I explaining of what I know of only :)

# Set up in Wercker

Set up a project is very easy and straigtforward, so I will be discussing about it now.

## Configure Wercker and DigitalOcean

Go to settings

![Go to setting](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_1.png)

Select SSH keys in the left panel

![Go to SSH Keys Dashboard](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_2.png)

Generate anew SSH Key Pair

![Generate new Key Pair](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_3.png)

Copy the public key

![Copy Public Key](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_4.png)

Add Public Key to your server, by running the following command

    echo -e 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCfWhtpKg2lxZQQaMJg8L6RVl67yPSwZ/EhY+C31bXI0H4Aqio7mkqW3yS+lfVDJpTXxaGLLjNbvOjbDCFSjZ8OxZphwtoMXQdbuz8Bmr2ZDfA6wSBX0lJocjo3blWchtzDCTXLFyWf/Bi0ccc+IEY2iGsAfLS+sreJHY1KiF3FGwCO6QoSK6CTOgtdPhYtXcOQiLMqq96oUhbf+XCgLVwQoBKyMYLNHxJlqPlPjHOGoSAfv+k57v8Rk2KKs4ZY4oIVCcwIe9e8alpP1vp0diorvf5kMYYjhazxu7JLghtnz9S8RPbn5SR7trZ/4yvOmg4FAY7FNoWClL/A7c0XjDa5 wercker / deploy / mynewkey' >> ~/.ssh/authorized_keys
    
    chmod 0600 ~/.ssh/authorized_keys


## Create Deploy Target

Wercker doesn't support deploy to Digital Ocean yet, so we will need to set up a custom deploy
    
So visit the Deploy Targets tab, and select custom deploy from the dropdown.

![Select custom deploy](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_5.png)

Next, insert name for the deploy target name.
There's a autodeploy option which you can select. It will auto deploy on successful build, either on master or branches. For now we will continue without auto deploy.

![Insert deploy target name](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_6.png)

After hitting save, you can set environment variables. Environment variables are variables which stored necessary value which we will be use later for deployment. There are a few variables needed, which I will explain the usage later.

First variable to add will be the SSH Key

![Add SSH Key Variable](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_7.png)

Second variable will be GIT which is our git directory in Digital Ocean

![Add GIT](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_8.png)

Third variable is HOSTNAME which will be storing the Digital Ocean droplet IP address. And remember to set it to Protected.

![Add HOSTNAME](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_9.png)

Next variable is WORK_TREE which will store your production repository path in your droplet.

![Add WORK_TREE](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_10.png)

Lastly will be variable TYPE, the value will be FULL or PARTIAL. Lets go with FULL for this first.

![Add TYPE](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_11.png)

## Deploy

With everything set, you can start testing now. Get the wercker.yml file, and put it in your project's root directory.
Then commit and push it to your master or production branch.

Once you have successfully push, Wercker will automatically build your project. 

![Wercker build project](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_12.png)

After the build is finish, click on it and you will be redirect to this page.

![Wercker deploy page](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_13.png)

Next select deploy to the deploy target you had just created. 

![Wercker deploy](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_14.png)

Wait for a few minutes, and walla. You are done!

![Deploy success](http://images.ninjoe.org/github/wercker-digitalocean-deployment/configure_wercker_digitalocean_15.png)


### References

https://serverpilot.io/community/articles/how-to-automatically-deploy-a-git-repo-from-bitbucket.html