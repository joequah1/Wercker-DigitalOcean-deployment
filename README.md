# Wercker-DigitalOcean-deployment
Deploy Repository from BitBucket to DigitalOcean using Wercker



# Set up in BitBucket

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

# Setup in Wercker

Set up a project is very easy and straigtforward, so I will be discussing about it now.

## Configure Wercker and DigitalOcean

Go to settings -> SSH keys -> Generate new key pair -> name for key -> copy Public Key -> add it into server

![Go to setting](http://postimg.org/image/cojcfya7x/)

![Go to SSH Keys Dashboard](http://postimg.org/image/uvau160xp/)

![Generate new Key Pair](http://postimg.org/image/wx6q1o9j1/)

![Copy Public Key](http://postimg.org/image/teuq5a8n1/)

Add Public Key to your server, by running the following command

    echo -e 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCfWhtpKg2lxZQQaMJg8L6RVl67yPSwZ/EhY+C31bXI0H4Aqio7mkqW3yS+lfVDJpTXxaGLLjNbvOjbDCFSjZ8OxZphwtoMXQdbuz8Bmr2ZDfA6wSBX0lJocjo3blWchtzDCTXLFyWf/Bi0ccc+IEY2iGsAfLS+sreJHY1KiF3FGwCO6QoSK6CTOgtdPhYtXcOQiLMqq96oUhbf+XCgLVwQoBKyMYLNHxJlqPlPjHOGoSAfv+k57v8Rk2KKs4ZY4oIVCcwIe9e8alpP1vp0diorvf5kMYYjhazxu7JLghtnz9S8RPbn5SR7trZ/4yvOmg4FAY7FNoWClL/A7c0XjDa5 wercker / deploy / mynewkey' >> ~/.ssh/authorized_keys
    
    chmod 0600 ~/.ssh/authorized_keys


## Create Deploy Target

    

### References

https://serverpilot.io/community/articles/how-to-automatically-deploy-a-git-repo-from-bitbucket.html