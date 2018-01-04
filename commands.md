lspci -vnn | grep VGA -A 12

lshw -numeric -C display

check Graphic Card HW

#Check GPU driver 
cat /proc/driver/nvidia/version

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
