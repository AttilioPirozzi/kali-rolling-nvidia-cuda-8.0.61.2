# kali-rolling-nvidia-cuda-8.0.61.2

Tested working on kernels 4.9.

I have used the latest build 2017.1

Then the usual lets get it all updated and start the funsies.

apt update && apt -y upgrade && apt -y dist-upgrade

reboot

apt install -y linux-headers-$(uname -r)

"If work for u install Bumblebeed Install"
I tried to test this again and ran into issues that were not present previously. I could not get the bumblebeed install to run so had to temp edit the grub boot config. On the end off the linux line add acpi=off and hit f10. I could then hit the gui and continue.

apt install -y bumblebee-nvidia

After this I was able to reboot into the GUI as normal.

Virtualgl install

wget https://10gbps-io.dl.sourceforge.net/project/virtualgl/2.5.1/virtualgl_2.5.1_amd64.deb

dpkg -i virtualgl_2.5.1_amd64.deb

Grab the latest Cuda run file and FIX.

wget http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/local_installers/cuda_8.0.61_375.26_linux.run?kG860P9RF2MqKKTw3jRGQ_UgKpS3eLoIaNXz7fl1TVErKdjlXWG6_ke24PKDLJLLVZVYJ6RpnlJYEIYW6Tx8tzFgZqX-LmyQJyDlZemWuu1nSd0TAUJb6TnykHMl97hk7KTsX_9rbQifMAJ8QX7kSzcZFPbkZX6KciTpMObWkDjC2sa1V1ifhYCc 

(Downloand last Cuda run file cuda_8.0.61_375.26_linux.run)

And update:

wget http://developer2.download.nvidia.com/compute/cuda/8.0/secure/Prod2/patches/2/cuda_8.0.61.2_linux.run?nUFQgZjdfBAae9VbJuKD_76HVYi1J8E8oSV0VvUp6QoEtDaWvYFp_JvGGw53pPcuPMyRJau09SESTw4ALwwB22depCmerV0zIh05oxa8FNmyTTbN4iuDwvlf1H6wnJ113zkO53fKCh5hk7TRwLaZUJ70W-bDNckWoveWIf_H

(cuda_8.0.61.2_linux.run)

After downloanded run this commands:

chmod +x cuda_8.0.61_375.26_linux.run
chmod +x cuda_8.0.61.2_linux.run

Next, unpack .run file

./cuda_8.0.61_375.26_linux.run --tar mxvf

 copy InstallUtils.pm to /usr/lib/x86_64-linux-gnu/perl-base
 
 sh cuda_8.0.61_375.26_linux.run --override
 
 Accept licensse
 Yes to unsupported configuration 
 No to Graphics Driver (For now)
 Yes to cuda 8 toolkit
 Yes to symbolic link
 Default last
 
 Install the Nvidia icd

apt install -y nvidia-opencl-icd

Now install the cuda driver

./cuda_8.0.61_375.26_linux.run -silent -driver
 
 Install update for improve performance and stability:
 
 sh cuda_8.0.61.2_linux.run 
 
 Accept license 
 Insert the default dir of Cuda (If you change first, insert the new dir) 
Done.

Post install paths

export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}

export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64\

export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

Get the latest OpenCl

apt install -y nvidia-opencl-dev

cd /root/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery

make

./deviceQuery

Get the opencl tools

cd /

curl https://codeload.github.com/hpc12/tools/tar.gz/master | tar xvfz -

cd tools-master/

make

./print-devices

You will see an error for device not found.

Get rid of the clover device errors

vim /etc/OpenCL/vendors/mesa.icd

comment out the .so file

./cl-demo 1000000 10

Install clinfo

apt install -y clinfo

clinfo

alt text

Further testing

cd /opt/VirtualGL/bin/

./glxspheres64

./glxinfo

glxinfo | grep -i "direct rendering"

CPyrit install

As OpenCL is now installed a good addition is CPyrit

Add dependancies

apt install -y libz-dev libssl-dev libpcap-dev

Add pyrit from git

cd /

git clone https://github.com/JPaulMora/Pyrit.git

Enter directory and build

Do this step as the CPyrit modules need to be installed to be able to build CPyrit_OpenCL, the standard pyrit with Kali does not have these modules.

cd Pyrit

python setup.py build

python setup.py install

Enter OpenCL directory and build NOTE# cpyrit_cuda wont build unless you use gcc 5, latest kali is version 6

cd modules/cpyrit_opencl/

python setup.py build

python setup.py install

Benchmark before CPyrit enabled

alt text

Edit the config to disable CPU and enable OpenCL

vim ~/.pyrit/config

set:

limit_ncpus = -1

use_OpenCL = true

Benchmark after CPyrit enabled

 
