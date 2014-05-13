# Jenkins and docker pipeline showcase

This project is a pre-made setup of Jenkins with some common plugins (including the Deployment Pipeline plugin) and Jobs setup ready to go.
This repo is used by some of the pre-made jobs just as an example.

This can be used for fun, perhaps a starting point. Or something to point and laugh at.


# To run:

    docker run -p 8080:8080 -privileged cloudbees/jenkins

This will run "docker in docker". If you don't wish to run it this way, you can use your "real" docker for builds:

    docker run -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock cloudbees/jenkins

This bind-mounts in the docker unix socker to the container, so Jenkins can then use it to run builds.


You will then have Jenkins running with jobs ready to go on port 8080. Kick one of them off and then click on the "pipeline" tab.


# How it works

Depending on how you start it - the startup scripts detects if it has docker available (via the docker.sock) - if not,
it will wrap docker to allow it to launch containers inside a container itself.

# How to make your own pre-baked jenkins image

I used <a href="https://index.docker.io/u/michaelneale/jenkins-docker-executors/">this</a> image to start with.
The high level steps: start and configure jenkins in a running container how you want,
then export the filesystem, and add /root/.jenkins to /root/.jenkins of a fresh image based on the above.

Cook up a jenkins configuration how you want in a running container, then:

    docker export > files.tar && tar -xf files.tar -C ../my_container

to get the jenkins_home out of the running container.

Then use a similar Dockerfile:

    FROM michaelneale/jenkins-docker-executors
    ADD ../my_container/root/.jenkins  /root/.jenkins

The result is a fresh image with all the jobs and plugins ready to go.



### Sample jobs
The sample jobs are fairly contrived. Some point back to this repo, some don't use any SCM.
Most are connected via triggers - which is what makes them show up in the pipeline.
Have a look at the config for each job, and mess around with it, break things, that is the idea.



### Themes and styles

I am not as stoic as other Jenkins users so I added a theme using the simple theme plugin.
The GUI themes used  here are based on https://github.com/Dakota628/jenkins-clean-theme
and is served up from:
https://s3.amazonaws.com/cloudbees-downloads/jenkins-styles/blue-ish.css
