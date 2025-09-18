FROM debian:13.1
LABEL maintainer "cyrille.bonamy@legi.cnrs.fr"
# Ensure a sane environment
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive


# Update the image & install some tools
RUN apt-get update --fix-missing && \
    apt-get -y dist-upgrade && \
    apt-get install -y --no-install-recommends \
        wget mesa-utils libgl1-mesa-dri \
        time freeglut3-dev meld ca-certificates libreadline-dev && \
    rm -rf /var/lib/apt/lists/ && rm -rf /usr/share/doc/ && \
    rm -rf /usr/share/man/ && rm -rf /usr/share/locale/ && \
    apt-get clean
RUN apt-get update --fix-missing && \
    apt-get -y dist-upgrade && \
    apt-get install -y --no-install-recommends \
        libglapi-mesa libgd-tools imagemagick graphicsmagick mplayer mencoder && \
    rm -rf /var/lib/apt/lists/ && rm -rf /usr/share/doc/ && \
    rm -rf /usr/share/man/ && rm -rf /usr/share/locale/ && \
    apt-get clean
RUN apt-get update --fix-missing && \
    apt-get -y dist-upgrade && \
    apt-get install -y --no-install-recommends \
        mjpegtools emacs gedit gedit-plugins gnuplot gnuplot-x11 \
        gnuplot-doc bash-completion bash-builtins libnss-wrapper vim nano tree \
        python3 python3-dev python3-pip python3-numpy \
        openmpi-bin libopenmpi-dev make flex gcc g++ libz-dev libfl-dev \
        curl bash-completion cmake libxt-dev \
        qttools5-dev libqt5x11extras5-dev qtbase5-dev libqt5opengl5-dev libqt5sql5-sqlite && \
    rm -rf /var/lib/apt/lists/ && rm -rf /usr/share/doc/ && \
    rm -rf /usr/share/man/ && rm -rf /usr/share/locale/ && \
    apt-get clean

# Download the 5.0 OpenFoam sources, install, and clean
RUN mkdir -p /opt/openfoam/2506 && wget -O /opt/openfoam/2506/OFzip https://dl.openfoam.com/source/v2506/OpenFOAM-v2506.tgz && \
    cd /opt/openfoam/2506 && tar -xvf OFzip && rm OFzip && \
    wget -O /opt/openfoam/2506/TPzip https://dl.openfoam.com/source/v2506/ThirdParty-v2506.tgz && tar -xvf TPzip && rm TPzip && \
    cd /opt/openfoam/2506/ThirdParty-v2506/sources/openmpi/ && wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.8.tar.gz && \
    tar -xvf openmpi-4.1.8.tar.gz && rm -rf openmpi-4.1.8.tar.gz && rm -rf openmpi-4.1.6

RUN cd /opt/openfoam/2506/OpenFOAM-v2506 && \
    /bin/bash -c "source etc/bashrc; ./Allwmake -j 8"

#RUN cd /opt/openfoam/2506/ThirdParty-v2506 && \
#    /bin/bash -c "source /opt/openfoam/2506/OpenFOAM-v2506/etc/bashrc && wmRefresh" && \
#    rm -rf /opt/openfoam/2506/ThirdParty-v2506/build && \
#    /bin/bash -c "find /opt/openfoam -name \"*.o\" -exec rm -f {} \;" && \
#    /bin/bash -c "find /opt/openfoam -name \".git*\" -exec rm -rf {} \;" && \
#    /bin/bash -c "find /opt/openfoam -name \".svn*\" -exec rm -rf {} \;"
#    sync

RUN echo "source /opt/openfoam/2506/OpenFOAM-v2506/etc/bashrc" >> ${HOME}/.bashrc && \
    sync

#create user openfoam and set environment directories
RUN useradd -ms /bin/bash openfoam
WORKDIR /home/openfoam
#USER openfoam:openfoam
# Set the default entry point & arguments
ENTRYPOINT ["/bin/bash"]

