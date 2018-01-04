sudo apt-get install --reinstall ca-certificates

hnz2szh@SGHZ001012257:~$ sudo add-apt-repository ppa:deadsnakes/ppa
Cannot add PPA: 'ppa:deadsnakes/ppa'.
Please check that the PPA name or format is correct.

# add ppa
You need to export your proxy environment variables using

export http_proxy=http://username:password@host:port/
export https_proxy=https://username:password@host:port/

and then tell sudo to use them using:

 sudo -E add-apt-repository ppa:webupd8team/y-ppa-manager

or open your /etc/sudoers file (using sudo visudo) and append

Defaults env_keep="https_proxy"


no valid OpenPGP data found


Solution: tell sudo to preserve sys env
sudo -E add-apt-repository ppa:git-core/ppa



#Nvidia Library link fails
```bash
/usr/bin/ld: cannot find -lnvcuvid
collect2: error: ld returned 1 exit status
make[1]: *** [cudaDecodeGL] Error 1
make[1]: Leaving directory `/home/audio-dnn/NVIDIA_CUDA-7.5_Samples/3_Imaging/cudaDecodeGL'
make: *** [3_Imaging/cudaDecodeGL/Makefile.ph_build] Error 2
```

substitute nvidia-xxx in findgleslib.mk with nvidia-384 by command line

find . -name findgllib.mk -type f -exec sed -i 's/nvidia-367/nvidia-384/g' {} +

check if succeed

find . -name findgllib.mk -type f -exec cat {} \; | grep nvidia-

#Run test failed
```bash
./deviceQuery Starting...

CUDA Device Query (Runtime API) version (CUDART static linking)

cudaGetDeviceCount returned 30
-> unknown error
Result = FAIL
```
https://devtalk.nvidia.com/default/topic/749939/cuda-is-not-active-unless-i-run-it-with-sudo-privillages-/
https://devtalk.nvidia.com/default/topic/699610/linux/334-21-driver-returns-999-on-cuinit-cuda-/
*https://stackoverflow.com/questions/11104320/root-access-required-for-cuda*



re-run the executable with sudo 

## udev
cat /etc/udev/rules.d/README

the files in this directory are read by udev(7) and used when events
are performed by the kernel.  The udev daemon watches this directory
with inotify so that changes to these files are automatically picked
up, for this reason they must be files and not symlinks to another
location as in the case in Debian.

Packages do not generally install rules here, this directory is for
local rules.  If you want to override behaviour of package-supplied
rules, which can be found in /lib/udev/rules.d, you can do one of
two things:

 1) Write your own rules in this directory that assign the name,
     symlinks, permissions, etc. that you want.  Pick a number higher
         than the rules you want to override, and yours will be used.

	  2) Copy the file from /lib/udev/rules.d and edit it here; you
	      should generally only do this if you want to prevent a program
	          from being run.


If the ordering of files in this directory are not important to you,
it's recommended that you simply name your files "descriptive-name.rules"
such that they are processed AFTER all numbered rules in both this
directory and /lib/udev/rules.d and thus override anything set there.

## nvidia-persistenced --verbose 
[setting up nvidia-persistenced](https://devtalk.nvidia.com/default/topic/995248/setting-up-nvidia-persistenced/)

### what is it? 
NVIDIA is providing a user-space daemon on Linux to support persistence of driver state across Cuda job runs. The daemon approach provides a more elegant and robust solution to this problem than persistence mode
### implementation detail


*On Linux systems running the NVIDIA GPU driver, clients attach a GPU by opening its device file. Conversely, the GPU is detached by closing the device file. The GPU state remains loaded in the driver whenever one or more clients have the device file open. Once all clients have closed the device file, the GPU state will be unloaded unless persistence mode is enabled.*

To simulate graphics environments without incurring the overhead of user-space graphics drivers, we have implemented the NVIDIA Persistence Daemon, which essentially runs in the background and sleeps with the device files open. The daemon uses libnvidia-cfg to open and close the correct device files based on its PCI bus address, and provides an RPC interface to control the persistence mode of each GPU individually. Thus, while the daemon holds the device files open, at least one client, the daemon, has the GPU attached and the driver will not unload the GPU state. Once the daemon starts running, it remains in the background until it is killed, even if persistence mode is disabled for all devices.

Because of the nature of the solution, the daemon can be used as a drop-in replacement for what we are now calling "legacy persistence mode" as implemented in the NVIDIA kernel-mode driver. NVIDIA SMI has been updated in driver version 319 to use the daemon's RPC interface to set the persistence mode using the daemon if the daemon is running, and will fall back to setting the legacy persistence mode in the kernel-mode driver if the daemon is not running. This is all handled transparently by NVIDIA SMI, so there should be no change in how persistence mode is configured. Eventually, the legacy persistence mode will be deprecated and removed in favor of the NVIDIA Persistence Daemon, once it has achieved wide adoption in the relevant use cases.

### Test: use upstart to start as init service.
to disable persistenced mode
sudo nvidia-smi -i 0 -pm DISABLED  

check status:
nvidia-smi -i 0 -q

check init config for N:
cat /etc/init/nvidia-prime.conf

A nvidia-persistenced.service file is found 

by default the persistence daemon is init with with --no-persistence-mode

*not working*

## Solution

1. Add nvidia-uvm to /etc/modules
2. creat /etc/udev/rules.d/70-nvidia-uvm.rules with below line
3. KERNEL=="nvidia_uvm", RUN+="/bin/bash -c '/bin/mknod -m 666 /dev/nvidia-uvm c $(grep nvidia-uvm /proc/devices | cut -d \  -f 1) 0;'"

#Pip3 install failed due to proxy
```bash
hnz2szh@SGHZ001012257:~$ pip3 install http://download.pytorch.org/whl/cu80/torch-0.3.0.post4-cp36-cp36m-linux_x86_64.wh
Downloading/unpacking http://download.pytorch.org/whl/cu80/torch-0.3.0.post4-cp36-cp36m-linux_x86_64.wh
  HTTP error 403 while getting http://download.pytorch.org/whl/cu80/torch-0.3.0.post4-cp36-cp36m-linux_x86_64.wh
  Cleaning up...
  Exception:
  Traceback (most recent call last):
    File "/usr/lib/python3/dist-packages/pip/basecommand.py", line 122, in main
        status = self.run(options, args)
	  File "/usr/lib/python3/dist-packages/pip/commands/install.py", line 278, in run
	      requirement_set.prepare_files(finder, force_root_egg_info=self.bundle, bundle=self.bundle)
	        File "/usr/lib/python3/dist-packages/pip/req.py", line 1198, in prepare_files
		    do_download,
		      File "/usr/lib/python3/dist-packages/pip/req.py", line 1376, in unpack_url
		          self.session,
			    File "/usr/lib/python3/dist-packages/pip/download.py", line 547, in unpack_http_url
			        resp.raise_for_status()
				  File "/usr/share/python-wheels/requests-2.2.1-py2.py3-none-any.whl/requests/models.py", line 773, in raise_for_status
				      raise HTTPError(http_error_msg, response=self)
				      requests.exceptions.HTTPError: 403 Client Error: Forbidden

				      Storing debug log for failure in /tmp/tmpz8ykja77

```
*Unresolved*

# Install Docker
1. Proxy problem need to be fixed by editing the service file and restart docker daemon
[href] https://docs.docker.com/engine/admin/systemd/#httphttps-proxy [/href]

2. Mange docker as non-root user
```bash
sudo groupadd docker

sudo usermod -aG docker $USER

hnz2szh@SGHZ001012257:~$ sudo usermod -a -G docker $(whoami)
[sudo] password for hnz2szh: 
usermod: user 'hnz2szh' does not exist
```
getent shows the group. 

[Solve] vim /etc/group  then append username to docker

#CuDNN install GCC version error
```bash
hnz2szh@SGHZ001012257:~/cudnn_samples_v7/mnistCUDNN$ ./mnistCUDNN 
cudnnGetVersion() : 7005 , CUDNN_VERSION from cudnn.h : 7005 (7.0.5)
Cuda failurer version : GCC 4.8.4
Error: unknown error
error_util.h:93
Aborting...
hnz2szh@SGHZ001012257:~/cudnn_samples_v7/mnistCUDNN$ nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2016 NVIDIA Corporation
Built on Tue_Jan_10_13:22:03_CST_2017
Cuda compilation tools, release 8.0, V8.0.61
```


