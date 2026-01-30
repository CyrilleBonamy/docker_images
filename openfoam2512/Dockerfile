FROM debian:13.3
LABEL maintainer "cyrille.bonamy@legi.cnrs.fr"
# Ensure a sane environment
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive


# Update the image & install some tools
RUN apt-get update --fix-missing && \
    apt-get -y dist-upgrade && \
    apt-get -y dist-upgrade && \
    apt-get install -y --no-install-recommends \
        wget mesa-utils libgl1-mesa-dri \
        time freeglut3-dev meld ca-certificates libreadline-dev \
        libglapi-mesa libgd-tools imagemagick graphicsmagick mplayer mencoder \
        mjpegtools emacs gedit gedit-plugins gnuplot gnuplot-x11 \
        gnuplot-doc bash-completion bash-builtins libnss-wrapper vim nano tree \
        python3 python3-dev python3-pip python3-numpy \
        openmpi-bin libopenmpi-dev make flex gcc g++ libz-dev libfl-dev \
        curl bash-completion cmake libxt-dev \
        qttools5-dev libqt5x11extras5-dev qtbase5-dev libqt5opengl5-dev libqt5sql5-sqlite \
        python-is-python3 ipython3 python3-tk \
        unzip git mercurial libreadline-dev vim nano emacs python3-venv && \
    rm -rf /var/lib/apt/lists/ && rm -rf /usr/share/doc/ && \
    rm -rf /usr/share/man/ && rm -rf /usr/share/locale/ && \
    apt-get clean

# Download the 5.0 OpenFoam sources, install, and clean
RUN mkdir -p /opt/openfoam/2512 && wget -O /opt/openfoam/2512/OFzip https://dl.openfoam.com/source/v2512/OpenFOAM-v2512.tgz && \
    cd /opt/openfoam/2512 && tar -xvf OFzip && rm OFzip && \
    wget -O /opt/openfoam/2512/TPzip https://dl.openfoam.com/source/v2512/ThirdParty-v2512.tar.gz && tar -xvf TPzip && rm TPzip && \
    cd /opt/openfoam/2512/ThirdParty-v2512/sources/openmpi/ && wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.8.tar.gz && \
    tar -xvf openmpi-4.1.8.tar.gz && rm -rf openmpi-4.1.8.tar.gz && rm -rf openmpi-4.1.6

RUN cd /opt/openfoam/2512/OpenFOAM-v2512 && \
    /bin/bash -c "source etc/bashrc; ./Allwmake -j 8"

RUN rm -rf /opt/openfoam/2512/ThirdParty-v2512/build && \
    find /opt/openfoam -name \"*.o\" -exec rm -f {} \; && \
    find /opt/openfoam -name \".git*\" -exec rm -rf {} \; && \
    find /opt/openfoam -name \".svn*\" -exec rm -rf {} \;

#create user openfoam and set environment directories
RUN useradd -ms /bin/bash openfoam
WORKDIR /home/openfoam

RUN python3 -m venv /home/openfoam/pyenv
RUN /bin/bash -c 'shopt -s expand_aliases && source /home/openfoam/pyenv/bin/activate && pip3 install --no-cache-dir fluidfoam pandas matplotlib odfpy black'

WORKDIR /root/
RUN git clone --branch develop --recurse-submodules https://github.com/sedfoam/sedfoam.git --depth 1

WORKDIR /root
RUN /bin/bash -c 'hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam -b develop && cp sedfoam/docker/Allwmakeswak swak4Foam/Libraries/Allwmake'
WORKDIR /root/swak4Foam
RUN /bin/bash -c 'shopt -s expand_aliases && ./maintainanceScripts/compileRequirements.sh'
RUN /bin/bash -c 'shopt -s expand_aliases && source /opt/openfoam/2512/OpenFOAM-v2512/etc/bashrc && export FOAM_USER_LIBBIN=$FOAM_SITE_LIBBIN && export FOAM_USER_APPBIN=$FOAM_SITE_APPBIN && export SWAK_COMPILE_GRAMMAR_OPTION=-O1 && export WM_NCOMPPROCS=1 && ./Allwmake'

WORKDIR /root/sedfoam
RUN /bin/bash -c 'shopt -s expand_aliases && source /opt/openfoam/2512/OpenFOAM-v2512/etc/bashrc && export FOAM_USER_LIBBIN=$FOAM_SITE_LIBBIN && ./Allwmake -prefix=group'

WORKDIR /home/openfoam
RUN cp -r /root/sedfoam sedfoam && chown -R openfoam: /home/openfoam && rm -rf /root/*
USER openfoam:openfoam
RUN echo "source /home/openfoam/pyenv/bin/activate" >> .bashrc

RUN echo "source /opt/openfoam/2512/OpenFOAM-v2512/etc/bashrc" >> ${HOME}/.bashrc && \
    sync

# Set the default entry point & arguments
ENTRYPOINT ["/bin/bash"]

