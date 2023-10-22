# Kaldi

### Kaldi 설치

- 필요한 패키지 설치
  - ```
    sudo apt-get update && apt-get install -y autoconf automake bzip2 g++ git
    sudo apt-get install -y gstreamer1.0-plugins-good gstreamer1.0-tools gstreamer1.0-pulseaudio gstreamer1.0-plugins-bad gstreamer1.0-plugins-base gstreamer1.0-plugins-ugly
    sudo apt-get install -y libatlas3-base libgstreamer1.0-dev libtool-bin make
    sudo apt-get install -y python2.7 python3 python3-pip python-yaml python-simplejson python-gi subversion wget build-essential python-dev sox gfortran zlib1g-dev
    ``` 
  - ```
    sudo apt-get clean autoclean && sudo apt-get autoremove -y
    sudo pip install ws4py==0.3.2 && sudo pip install tornado==4.5.3
    sudo ln -s /usr/bin/python3 /usr/bin/python
    sudo ln -s -f bash /bin/sh
    ```
- Cuda 설치 및 gcc 버전 설정
  - ```
    sudo apt install gcc-7 g++-7
    sudo rm -f /usr/bin/gcc
    sudo rm -f /usr/bin/g++
    sudo ln -s /usr/bin/gcc-7 /usr/bin/gcc 
    sudo ln -s /usr/bin/g++-7 /usr/bin/g++
    ```
- Jansson 설치
  - ```
    cd /opt
    sudo wget http://www.digip.org/jansson/releases/jansson-2.7.tar.bz2
    sudo bunzip2 -c jansson-2.7.tar.bz2 | sudo tar xf -
    
    cd jansson-2.7
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    
    sudo ldconfig
    sudo rm /opt/jansson-2.7.tar.bz2
    sudo rm -rf /opt/jansson-2.7
    ```
- kaldi 컴파일
  - ```
    cd /opt
    sudo git clone https://github.com/kaldi-asr/kaldi.git
    
    cd /opt/kaldi/tools
    sudo make
    sudo ./extras/install_irstlm.sh
    sudo ./extras/install_mkl.sh
    sudo ./install_portaudio.sh
    ```
- kaldi src 컴파일
  - ```
    cd /opt/kaldi/src
    sudo ./configure --shared
    sudo make depend
    grep -c processor /proc/cpuinfo
    sudo make -j 8
    
    cd /opt/kaldi/src/online
    sudo make depend
    sudo make -j 8
    ```
- gst-plugin 컴파일
  - ```
    cd /opt/kaldi/src/gst-plugin
    sudo make depend
    sudo make -j 8
    ```
- gst-kaldi-nnet2-online 컴파일
  - ```
    cd /opt
    sudo git clone https://github.com/alumae/gst-kaldi-nnet2-online.git

    cd /opt/gst-kaldi-nnet2-online/src
    sudo sed -i '/KALDI_ROOT?=\/home\/tanel\/tools\/kaldi-trunk/c\KALDI_ROOT?=\/opt\/kaldi' Makefile
    sudo make depend
    sudo make -j 8
    ```
- kaldi-gstreamer-server clone
  - ```
    cd /opt
    sudo git clone https://github.com/alumae/kaldi-gstreamer-server.git
    sudo git clone https://github.com/jcsilva/docker-kaldi-gstreamer-server.git

    cd docker-kaldi-gstreamer-server

    sudo chmod +x start.sh
    sudo chmod +x stop.sh
    ```


