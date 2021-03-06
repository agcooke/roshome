Ros Home - Sofie Bicycle Setup.
=======

This document describes how to install the bicycle stability test bench on an Ubuntu machine.

The system is built using (ROS)[http://www.ros.org/wiki/], SOFIE HDF FORMAT. 
There is also a package created for working with experiments.
### Download and install (Ubuntu)[http://releases.ubuntu.com/precise/]

1. Create usb install disk from the download iso: The best Ubuntu version to use for now is
Ubuntu 12.04.

     $sudo usb-disk-creator

2. Boot the USB stick in the new computer and install from the menu on the new computer.
3. Setup EDUROAM: (UT Eduroam setup)[http://www.utwente.nl/icts/en/handleidingen/netwerk/]
4. Open a terminal

Make sure the new user gets permanent SUDO rights.

	$ sudo su
	$ visudo
	
Add the user that you chose to the suders list then hit	CTRL+D. Install essential services

### Install required packages, update server and reboot.
	
	$ sudo apt-get install aptitude git openssh-server python-pip pythoncard
	$ sudo aptitude update
	$ sudo aptitude safe-upgrade
	$ sudo reboot
	
### INSTALL VIRTUALENVWRAPPER.
Then use virtualenv to manage the installation. Install: 
(http://www.doughellmann.com/projects/virtualenvwrapper/)
	
	$ sudo pip install virtualenvwrapper
	
### Install ros.
	
Then install ROS, follow the instructions on the ROS website to add the repository for ubuntu.
ROS (install)[http://www.ros.org/wiki/groovy/Installation/Ubuntu]

Install it from repository (groovy is stable).
	
	
    $ sudo aptitude install ros-groovy-desktop ros-groovy-image-view ros-groovy-rosserial python-rosinstall    


Edit your ~/.bashrc (this is common to all ROS installs, make sure that there are no spaces
before the commands in the .bashrc file.):

     source /opt/ros/groovy/setup.bash
     export ROS_WORKSPACE=/home/${USER}/roshome/
     export ROS_PACKAGE_PATH=$ROS_WORKSPACE:$ROS_PACKAGE_PATH
     export ROS_HOSTNAME=127.0.0.1
     export ROS_MASTER_URI=http://127.0.0.1:11311
     export WORKON_HOME=~/Envs
     source /usr/local/bin/virtualenvwrapper.sh
     
Clean and install the correct packages:

	$ source ~/.bashrc
    $ sudo rosdep init
    $ rosdep update
    
### Download and install SOFIE ROS.

	$ cd ~/
	$ git clone git://github.com/agcooke/roshome.git
	$ roscd
	
Get the correct groovy branch for ar_track_alvar. This could change later.

Install the packages until tables installed correctly. Move out of the roshome directory then run:

	$ sh CLONE\_SETUP.sh

The above script will fail. Run the last three lines again, and again
installing dependencies until it works. The last three lines will fail,
what you need to do is rerun ¨sudo pip install tables", fixing the missing
missing pip/python dependencies until tables is installed. Then install
the sofiehdfformat package (near end of the CLONE_SETUP.sh file.)
	
	$ roscd
	$ catkin_make
	$ source devel/setup.sh 
	

### Install the Promove GUI information.
Now install IMU software to work with the Intertia Technology devices:

There are two ways to do this:
for the quick:

 	$ sudo dpkg -i promovegui_1.26+svn1003-1_all.deb 

of a longer but more robust way:

Edit the apt file to add a debian package:

    $sudo vi /etc/apt/sources.list

Then add the following lines

    def file:///home/${USER}/ExtraDebPackages/ /

Then make the directory 

    $ mkdir /home/${USER}/ExtraDebPackages/

Copy the latest promovegui debian file to this directory.
Then clone bashsnippets:

    $ mdir ~/workspace
    $ cd ~/workspace
    $ git clone git://github.com/agcooke/bashsnippets.git
    $ cp bashsnippets/deb/CreatePackages /home/${USER}/ExtraDebPackages/
    $ sudo sh CreatePackages
    $ sudo aptitude update
    $ sudo aptitude install promovegui

### Setup USB permissions.

The user needs write permission on the serial ports:

    $sudo usermod ${USER} -G dialout

You might have to log out and back in again to get the correct
permissions on the /dev/ttyUSB0 or /dev/ttyUSB1,2,3,4,... that PromoveGUI uses.

### Run the system
Now everything is installed and ready to use.

Move into our virtualenv:

    $ workon sofie
    
Make sure ros environment is correcct:

    $ source ~/roshome/devel/setup.sh
   
Then run the GUI.
    
    $ experiment-control-gui.py -h
    
To debug the video, when an experiment is running in the terminal:

	$ rosrun rviz rviz 
	
Open the config file for rviz which sites in the ~/roshome/src/sofie_ros/ folder.
To debug the Arduino run

	$ rostopic echo /angle
	
To ge the ProMoveGUI working we need to set the nodes connected to the wireless network,
each time we restart the program.

	$ProMoveGUI -m 2
	
Then start to Capture data. Go to Options->Configuration->Wireless and set maximum number
of notes to 13 for the Gateway and the Nodes.
