# Kaldi Speech-to-Text at WGBH's Media Library and Archives

This repo contains documentation and resources for MLA's implementation of Kaldi speech-to-text software.

# Getting Started

- Get MLA Amazon Web Services Console Login Credentials
- Login URL: https://wgbh-mla.signin.aws.amazon.com/console
- Click on EC2 under AWS Services
- Go to Launch Instance
- Select Amazon Linux
- Select t2.xlarge
- Configure Instance Details
- Shutdown Behavior: Stop
- Enable Termination Protection
- Click Next: Add Storage
- For Root Volume Type
- Size: 64
- Volume Type: General Purpose
- Click Next: Add Tags
- Kaldi
- Step 6: Configure Security Group
- SSH/Protocol/Port Range 22/ Anywhere
- Launch!

- Create a new key pair: kaldi-aws-ec2-user
- Save pem file
- Launch Instance
- chmod key file 600

## Installation

- ssh in to instance
- sudo yum update
- install git: sudo yum install git-all
- git clone ...kaldi.git kaldi --origin upstream
- cd in to kaldi/tools directory
- extras/check_dependencies.sh
- sudo yum install zlib-devel gcc automake autoconf patch libtool
- sudo yum install atlas.x86_64
- sudo yum install gcc-c++
- run 'make -j 4'
- snapshot image
- cd kaldi/src
- run ./configure --shared
- run make depend -j 4
- run make -j 4


  PUA Installation (https://github.com/popuparchive/american-archive-kaldi)
  - cd $HOME
  - git clone https://github.com/popuparchive/american-archive-kaldi

  First create symlinks between structures in kaldi and american-archive-kaldi
    - ln -s kaldi/egs/wsj/s5/steps american-archive-kaldi/sample_experiment/
    - ln -s kaldi/egs/wsj/s5/utils american-archive-kaldi/sample_experiment/
    
  Install system dependencies for perl
    - sudo yum install perl-core.x86_64
    - sudo yum install perl-JSON.noarch
    - sudo yum install perl-CPAN.noarch # optional unless the following statement fails
    - cpan JSON # initializes CPAN and does confirm/install JSON
    - perl -MCPAN -e shell
      - install File::Slurp::Tiny
      - install Data::Dump
      - exit
  
  
  - Install SOX in root directory
  - Run ./configure in sox-[version] directory
  - Run make -s
  - Run sudo make install
  - Run make installcheck

  - Install FFMPEG in root directory
  - Sudo yum install yasm
  - Run ./configure in ffmpeg directory
  - Run make
  - Run sudo make install

  - Ensure CMUSeg is installed 
  - cd american-archive-kaldi/sample_experiment/
  - ./install-cmuseg.sh 

  - Install IRSTLM
  - cd kaldi/tools/extras
  - run install ./install_irstlm.sh
  - vi .bash_profile
  - Add . ./kaldi/tools/extras/env.sh
  
  - Install sclite
  - cd kaldi/tools/sctk-2.4.10
  - configure CFLAGS variable in makefiles to compile sclite for a 64-bit environment, adding -m64 to:
      - sctk-2.4.10/src/asclite/core/makefile:CFLAGS = -g -Os -m64
      - sctk-2.4.10/src/asclite/test/makefile:CFLAGS = -Os  -Wall -Wconversion -m64
      - sctk-2.4.10/src/sclite/makefile:CFLAGS = -Os -m64
      - NOTE: the README cites src/rfilter1/makefile as well, but it contained no CFLAGS variable
  - make config
  - make all
  - make check
  - make install
  - make doc
  
  

## Usage


