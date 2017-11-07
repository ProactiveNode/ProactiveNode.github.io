---
layout: default
title: 'Docker and Jekyll with GitHub Pages'
excerpt: Docker can be used to test changes on a Jekyll website that has GitHub Pages on a local machine before publishing that content on the Internet. 
---
<h3> Docker and Jekyll </h3>
When I first started using Jekyll and publishing content, I had done a great deal of commits and changes to the website. I wanted to customize the website and see how Jekyll worked. After a small amount of time just testing a few things, I had already done more commits than I had wanted. This led me to create a Docker container to test all the changes I wanted to make as well as experiment on the website locally.


To begin this whole process of creating a Docker container, I had to make sure I had all the proper tools to create my container. I had done all of this in Ubuntu 16.04 and used Docker version 17.03.1-ce. The CE stands for Community Edition. Installing Docker for Ubuntu is straightforward and I follow the instructions posted on the Docker docs[1]. Once it has been installed, we can focus on creating our Docker image.

To create the Dockerfile, you will need to know how to compose it and how it is used. The Dockerfile is a set of instructions that the user wants to do for a Docker Image.[2] In figure 1 below, you can see the Dockerfile that I had created.

{% highlight markdown %}
FROM ubuntu:16.04

RUN mkdir /website

ADD Gemfile /website
ADD _config.yml /website

RUN apt-get -y update && apt-get install -y \
	ruby \
	gem \
	git \
	bundler \
	jekyll

RUN apt-get -y install build-essential patch ruby-dev zlib1g-dev liblzma-dev
RUN gem install ffi -v '1.9.18'
RUN gem install nokogiri

VOLUME /website
WORKDIR /website

RUN bundle install
RUN gem cleanup

EXPOSE 4000


CMD ["bundle", "exec", "jekyll", "serve", "--host=0.0.0.0"]

{% endhighlight %}

<p style="font-size:90%"> Figure 1: Dockerfile for the Jeykll container </p>

My Dockerfile is comprised of several different commands: FROM, RUN, ADD, VOLUME, WORKDIR, EXPOSE, CMD. 



 * FROM is used to create the base image for the container. The base image that I had used was ubuntu:16.04
 * RUN executes commands that the user specifies.
 * ADD is used to copy files. In my Dockerfile, I am copying the Gemfile and _config.yml off my local machine into a directory named website.
 * VOLUME is used to specify a mount point that will be used to hold external data such as files that are on my local machine. 
WORKDIR specifies the working directory. Once you specify that directory, any RUN and CMD commands.
 * EXPOSE is used to specify a port that the Docker container can listen to. I had used 4000 since it is Jekyll’s default port.
 * CMD can be used to do a command in the shell. The command I am doing is launching jekyll locally as at the ip address of 0.0.0.0

Each of these commands listed in the Dockerfile go in order. To save a bit of time, it is good to group commands together like the apt-get install. As these commands run, you will need two more files to accompany your Dockerfile for this container to build. They are the Gemfile and the _config.yml. Both of these files are shown below in Figure 2 and Figure 3 respectively. 

{% highlight markdown %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}
<p style="font-size:90%"> Figure 2: Gemfile for the Jeykll container </p>

{% highlight markdown %}
#Config Settings; _config.yml
title: Proactive Blog
description: Root
github_username: ProactiveNode
theme: jekyll-theme-slate
{% endhighlight %}
<p style="font-size:90%"> Figure 3: _config.yml being used for the website</p>

The Gemfile is used to specify what gems are being used for the website and the gem is being used is github-pages. The _config.yml file is how we can further customize the jekyll website and what theme you can use. In this file, I had chosen the jekyll-theme-slate that is provided by the github-pages gem. Once you have the Gemfile, _config.yml and Docker file, you put them all in one folder so it is advisable to create a seperate folder to have all these files in. Navigate to your directory that houses all of these files and from here we will create the Docker Image. To create the image, the command shown in Figure 4 would create it.

{% highlight markdown %}
docker build -t workingWebsite .
{% endhighlight %}
<p style="font-size:90%"> Figure 4: Docker build command to create Docker Image </p>

The -t option tags the specific image with a name and the name that I used in this example is workingWebsite. The period at the end needs to be included because this specifies where the Dockerfile as well as the other files are located to create the image. This wasn't the fastest Docker image created since it took a few minutes to create. As I learn more about Docker, I will improve upon this.

After we created the Docker Image, we need to create another folder filled with necessary files and folders to be used for the Jekyll. This main folder can be called website_local. Below, I have a skeleton of what the contents should look like below for my local folder in Figure 5. I had learned about how to set the file structure up from Jekyll’s official website[3].

{% highlight sh %}
website_local
├── _config.yml
├── _layouts
|   ├── default.html
├── _posts
|   ├── 2017-11-02-DockerandJekyll.md
├── _site
├── Gemfile
└── index.html
{% endhighlight %}
<p style="font-size:90%"> Figure 5: Contents of the website_local folder </p>

Once you have that folder and its contents set up, you can now start your Docker container as shown in Figure 6. To start it, you will need to use the command in Figure 6.

{% highlight markdown %}
docker run -p 4000:4000 -v ~/website_local:/website workingWebsite
{% endhighlight %}
<p style="font-size:90%"> Figure 6: Docker run command to run the container </p>

This docker run command requires three components to it
* The -p option allows us to specify the ports to be used. The ports that are being used are port 4000.
* The -v option allows us to attach a folder’s contents to a folder within the container. I have attached the website_local folder that was created to have the necessary contents for Jekyll.
* workingWebsite is the name of the Docker Image that was created earlier.


Once you run this command, your container will be up and running. You can edit your files in your website_local folder and see how your website changes with a reload of the page.

References:

[1] https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

[2] https://docs.docker.com/engine/reference/builder/

[3] https://jekyllrb.com/docs/structure/
