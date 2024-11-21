# Yocto Guide

## Introduction
Yocto is an open-source project that provides a set of tools and resources for creating custom Linux-based operating systems for embedded systems. It is not a Linux distribution itself, but rather a framework that allows developers to build a tailored distribution suited to their specific hardware and requirements.

### Why Yocto? Key benefits:

1. Customizability: Yocto allows you to configure and customize every aspect of the operating system, from the kernel to the application layer, enabling developers to create an OS tailored to their hardware and application needs.
2. Cross-platform support: It supports a wide variety of hardware architectures (e.g., ARM, x86, PowerPC), making it versatile for use in embedded systems across different industries.
3. Scalability: Yocto is ideal for both small, resource-constrained devices (e.g., IoT devices) and larger, more powerful embedded systems, as it allows you to optimize for performance, size, and power consumption.
4. Reusability and maintainability: The Yocto Project leverages a layer-based architecture, where you can reuse existing layers for different projects, improving development efficiency and simplifying long-term maintenance.
5. Security: Yocto enables the integration of modern security features into custom Linux images, including secure boot, encryption, and access control, to ensure safe and secure systems.

## Environment: Setting Up a Virtual Machine for Yocto Development
My environment will be a Virtual Machine with Ubuntu 20.04LTS. This allows isolation, keeping the Yocto development environment separated from my system. I can also easily make back-ups and have an already compiled distribution as point of entry for later projects.

When configuring the virtual machine, allocate at least four CPU cores and 8 GB of RAM, although 16 GB of RAM is recommended for larger Yocto builds. Yocto builds can be quite resource-intensive, so providing sufficient memory will help prevent slowdowns. Additionally, you’ll need at least 50 GB of disk space in the VM to store the Yocto build data. If you're using VirtualBox, be sure to set the network to either a NAT or bridged adapter, so the VM can access the internet for downloading dependencies and updates.

Once you’ve set up the VM with Ubuntu 20.04, you can install the necessary Yocto dependencies using the apt-get package manager, such as build-essential, git, and g++. Afterward, you can clone the Yocto project repository and begin building your custom Linux image using the bitbake command. It will be explained step-by-step!

### Installing required packages
First, we should have an up-to-date environment.

```sh
sudo apt-get update && sudo apt-get upgrade
```
The following packages will be needed:

```sh
sudo apt-get install gawk wget git diffstat unzip texinfo gcc-multilib \
build-essential chrpath socat cpio python3 python3-pip python3-pexpect \
xz-utils debianutils iputils-ping libsdl1.2-dev xterm libyaml-dev libssl-dev
 
sudo apt-get install autoconf libtool libglib2.0-dev libarchive-dev \
sed cvs subversion coreutils texi2html docbook-utils \
help2man make gcc g++ desktop-file-utils libgl1-mesa-dev libglu1-mesa-dev \
mercurial automake groff curl lzop asciidoc u-boot-tools dos2unix mtd-utils pv \
libncurses5 libncurses5-dev libncursesw5-dev libelf-dev zlib1g-dev bc rename \
zstd libgnutls28-dev
 
sudo apt-get install python3-git liblz4-tool python3-jinja2 python3-subunit locales libacl1

sudo apt-get install python python-pysqlite2
```

### Download Yocto Mickledore

We will download Yocto Mickledore from its' Git Repository.

The first step is to configure your user and email:
```sh
git config --global user.name "Your Name"
git config --global user.email "Your Email"
```
Fetch and install the Google git-repo tool:
```sh
mkdir -p ~/bin
curl https://commondatastorage.googleapis.com/git-repo-downloads/repo-2.32 > ~/bin/repo
```

Give repo execute permissions and add it to the path:
```sh
$ chmod a+x ~/bin/repo
$ export PATH=~/bin:$PATH
```

Create a build directory: 
```sh
mkdir ~/var-fsl-yocto
cd ~/var-fsl-yocto
```

Download the latest version:
```sh
repo init -u https://github.com/varigit/variscite-bsp-platform.git -b mickledore -m imx-6.1.36-2.1.0.xml
repo sync -j$(nproc)
```

### Setup and build Yocto

While setting up Yocto, the first step is to build XWayland. The following command is only mandatory for the very first build setup.
```sh
cd ~/var-fsl-yocto
MACHINE=imx8mp-var-dart DISTRO=fsl-imx-xwayland . var-setup-release.sh build_xwayland
```

Whenever restarting a newer build session (from a different terminal or in a different time), you will have to run the following command:
```sh
cd ~/var-fsl-yocto
source setup-environment build_xwayland
```

Now, we will build our first Yocto image!
```sh
bitbake core-image-minimal
```
Be aware, during the building things can come wrong. If it stops or fails, try to run the above command. Probably it will fail a few times during the first build, but it should be fixed just running the above command.
