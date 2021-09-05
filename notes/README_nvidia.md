
#Update Nvidia driver on CentOS 7.x

Download the latest Nvidia driver on http://www.nvidia.com/drivers

##Update the kernel to the latest version

$ yum update

##Change to runlevel 3 - multi user mode for the next reboot

$ systemctl set-default multi-user.target
$ systemctl get-default

##Reboot your computer

$ reboot

##Install the Nvidia driver from the console after rebooting

$ chmod +x NVIDIA-Linux-x86_64-xxx.xx.run
$ ./NVIDIA-Linux-x86_64-xxx.xx.run

##Change back to runlevel 5 - graphical mode for the next reboot

$ systemctl set-default graphical.target
$ systemctl get-default

##Reboot back to the graphical mode, and enjoy.

$ reboot


