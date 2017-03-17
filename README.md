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


-  PUA Installation (https://github.com/popuparchive/american-archive-kaldi)
  - cd $HOME
  - git clone https://github.com/popuparchive/american-archive-kaldi
  
  - Download the latest PUA Models, final result should be (re-)named exp
  - curl -O 'https://cytranet.dl.sourceforge.net/project/popuparchive-kaldi/exp2.tar.gz'
  - NOTE:  THIS WAS PROBLEMATIC, REQUIRING MANY HOURS AND "CONTINUE" SESSIONS LIKE THIS:
    - curl -O -C  - 'https://cytranet.dl.sourceforge.net/project/popuparchive-kaldi/exp2.tar.gz'
  - Unpack the fully-formed exp2.tar.gz to become directory 'exp'
    - tar -xzf exp2.tar.gz exp
    
 
- Move the PUA Models into the american-archive-kaldi/sample_experiment
  - mv exp american-archive-kaldi/sample_experiment/

 - Create symlinks between structures in kaldi and american-archive-kaldi
    - ln -s kaldi/egs/wsj/s5/steps american-archive-kaldi/sample_experiment/
    - ln -s kaldi/egs/wsj/s5/utils american-archive-kaldi/sample_experiment/
    - ln -s kaldi/egs/wsj/s5/steps american-archive-kaldi/sample_experiment/exp/
    - ln -s kaldi/egs/wsj/s5/utils american-archive-kaldi/sample_experiment/exp/
    
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
  - Add command the following command to .bash_profile:     . ./kaldi/tools/extras/env.sh
  
  - Install sclite
  - cd kaldi/tools/sctk-2.4.10
  - configure CFLAGS variable in makefiles to compile sclite for a 64-bit environment, adding -m64 to:
      - sctk-2.4.10/src/asclite/core/makefile:  CFLAGS = -g -Os -m64
      - sctk-2.4.10/src/asclite/test/makefile:  CFLAGS = -Os  -Wall -Wconversion -m64
      - sctk-2.4.10/src/sclite/makefile:  CFLAGS = -Os -m64
      - NOTE: the README cites src/rfilter1/makefile as well, but it contained no CFLAGS variable
  - Run make config
  - Run make all
  - Run make check
  - Run make install
  - Run make doc


- Deal with python errors thrown when using american-archive-kaldi/run_kaldi.py 
- Upgrade pip and install ftfy
  - NOTE:  the following assumes use of python v2
  - sudo pip install --upgrade pip
  - sudo ln -s /usr/local/bin/pip /usr/bin/pip
  - sudo pip install ftfy==4.4.1
  - sudo pip install PY3
  - sudo pip install -U six

- More idiocy about inadequate permissions: 
 - list inventory of now-executable files
 [ec2-user@ip-172-30-0-129 sample_experiment]$ find . -type f -name '*sh' -executable
./install-cmuseg.sh
./set-kaldi-path.sh
./run.sh
  - list non-executable files ending with '.sh' and make them executable
[ec2-user@ip-172-30-0-129 sample_experiment]$ find . -type f -name '*sh' -not -executable
./path.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/install.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/gen_make.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/h_header.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/raw2nist.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/gen_setv.sh
./tools/CMUseg_0.5/src/lib/sphere/src/scripts/gen_lord.sh
./tools/CMUseg_0.5/src/lib/sphere/bin/h_header.sh
./tools/CMUseg_0.5/src/lib/sphere/bin/raw2nist.sh
./tools/CMUseg_0.5/src/lib/sphere/bin/gen_setv.sh
./tools/CMUseg_0.5/src/lib/sphere/bin/gen_lord.sh
./add_grammar.sh
./prep_lang_local.sh
./scripts/create_big_lm.sh
./scripts/qq.sh
./scripts/create_lm.sh
[ec2-user@ip-172-30-0-129 sample_experiment]$ find . -type f -name '*sh' -not -executable -exec chmod +x {} \;

find /home/ec2-user/american-archive-kaldi/sample_experiment/tools/CMUseg_0.5/bin/linux -type f -exec chmod +x {} \;
chmod +x american-archive-kaldi/sample_experiment/scripts/*


- Begin idiocy involving perl
  -Use CPAN to install perl4 core lib for 'getopts.pl' reference in CMUseg.pl script
    -perl -MCPAN -e shell
      cpan > install Perl4::CoreLibs


- Steps after following PUA install guide
• In american-archive-kaldi/sample_experiment/run.sh, set the following option to reduce the number of simultaneous jobs to 1: nj=1
• In kaldi/egs/wsj/s5/utils/run.pl, set the following option:
$max_jobs_run = 10;
• In kaldi/egs/wsj/s5/steps/decode_fmllr.sh and kaldi/egs/wsj/s5/steps/tandem/decode_fmllr.sh, set the following options in the section where they appear together: 
nj=1
max_active=1000
• In decode.sh, set the following option: nj=2 in these locations:
- kaldi/egs/wsj/s5/steps/nnet/decode.sh
- kaldi/egs/wsj/s5/steps/online/decode.sh
- kaldi/egs/wsj/s5/steps/online/nnet2/decode.sh
- kaldi/egs/wsj/s5/steps/online/nnet3/decode.sh
- kaldi/egs/wsj/s5/steps/decode.sh
- kaldi/egs/wsj/s5/steps/tandem/decode.sh
- kaldi/egs/wsj/s5/steps/nnet3/decode.sh
- kaldi/egs/wsj/s5/steps/nnet2/decode.sh
- kaldi/egs/aspire/s5/local/multi_condition/decode.sh





## Usage


