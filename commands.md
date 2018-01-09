lspci -vnn | grep VGA -A 12

lshw -numeric -C display

check Graphic Card HW

#Check GPU driver 
cat /proc/driver/nvidia/version

#Check GPU load 1s fresh
nvidia-smi -l 1
#Command line output to file

cmd &> xxx.txt

save the error and the output both to txt file.

#Nvidia 
nvidia-smi

#List all startup
initctl list
service --status-all
ls /etc/init  to see all .conf file
http://upstart.ubuntu.com/cookbook/#job-configuration-file

#Things about init

1. System Init Daemon

This has changed as part of the Ubuntu 15.04 devel cycle.

Ubuntu 15.04 (using Systemd by default):

    Systemd runs with PID 1 as /sbin/init.

        Upstart runs with PID 1 as /sbin/upstart. 

	Prior versions (using Upstart by default):

	    Upstart runs with PID 1 as /sbin/init.

	        Systemd runs with PID 1 as /lib/systemd/systemd. 

# Docker
check pulled images
>docker images

bad <none><none> images http://www.projectatomic.io/blog/2015/07/what-are-docker-none-none-images/
stop and remove all docker containers
>docker stop $(docker ps -a -q)
>docker rm $(docker ps -a -q)

remove all dangling images, [ref](https://stackoverflow.com/questions/33907835/docker-error-cannot-delete-docker-container-conflict-unable-to-remove-reposito)

>docker rmi $(docker images -f "dangling=true" -q)

#MxNet

