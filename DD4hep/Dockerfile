#
# Build a docker container to build DD4hep applications.
# Docker build command:
#   docker build -t gordonwatts/dd4hep:v01.07.01 .
#
# FROM andreadotti/geant4-dev:10.4.p02 as builder
FROM gordonwatts/geant4:10.04.p02 as builder

LABEL Author="Gordon Watts <gwatts@uw.edu>"

USER root

# Package installs
RUN apt-get update
RUN apt-get -y install wget cmake cmake-curses-gui build-essential \
        libqt4-opengl libqt4-opengl-dev qt4-qmake libqt4-dev libx11-dev \
        libxmu-dev libxpm-dev libxft-dev libboost-all-dev python

# ROOT
# Besure there is enough memory for this to succeed - the multiple
# compilers can cause a memory issue. This is known to work with 6GB
# availible on the build machine.
RUN mkdir -p ~/root-source; \
        cd ~/root-source; \
        wget -q https://root.cern.ch/download/root_v6.12.06.source.tar.gz; \
        tar -zxf root_v6.12.06.source.tar.gz

RUN mkdir -p ~/root-build; \
    cd ~/root-build; \
    cmake -DCMAKE_INSTALL_PREFIX=~/install ~/root-source/root-6.12.06; \
    make -j`grep -c processor /proc/cpuinfo`; \
    make install

# Finally, DD4hep
# Currently pulled from a private repro b.c the last release doesn't build correctly.
RUN apt-get -y install git libxerces-c-dev

ARG dd4git="https://github.com/gordonwatts/DD4hep.git"
ARG dd4gitbranch="b0b4d60"

RUN cd ~; \
    git clone $dd4git; \
    cd ~/DD4hep; \
    git checkout $dd4gitbranch

RUN mkdir -p ~/DD4hep/build; \
    cd ~/DD4hep/build; \
    bash -c 'source ~/install/bin/thisroot.sh; \
    cmake   -DDD4HEP_USE_GEANT4=ON \
            -DDD4HEP_USE_XERCESC=ON \
            -DBoost_NO_BOOST_CMAKE=ON \
            -DDD4HEP_USE_LCIO=OFF \
            -DBUILD_TESTING=ON \
            -DGeant4_DIR=/usr/local/geant4/lib/Geant4-10.4.2 \
            -DCMAKE_INSTALL_PREFIX=~/install \
            -DROOT_DIR=$ROOTSYS \
            .. \
    '
#
# WARNING: you need at least 4 GB of memory availible to do the build
# (spirit parsers are monadic and template heavy!)   
RUN cd ~/DD4hep/build; \
    bash -c 'source ~/install/bin/thisroot.sh; \
    make -j`grep -c processor /proc/cpuinfo`; \
    make install; \
    '

# Run the default tests to see how things work
RUN cd ~/DD4hep/build; \
    bash -c 'source ~/install/bin/thisroot.sh; \
    make test; \
    '

# Run the much more sophisticated tests. Lots of these
# fail, so we don't care too much - just run them as part of the
# build process to see how we do generally.
RUN bash -c 'source ~/install/bin/thisroot.sh; \
    source ~/install/bin/thisdd4hep.sh; \
    mkdir -p ~/DD4hep/examples/build; \
    cd ~/DD4hep/examples/build; \
    cmake -DCMAKE_INSTALL_PREFIX=~/install_tests ..; \
    make install; \
    make test; \
    '

# Next, create a nice compact image
FROM gordonwatts/geant4:10.04.p02
LABEL Author="Gordon Watts <gwatts@uw.edu>"
USER root

# Packages that are needed
#RUN apt-get install python

# Copy from the old install directory over
COPY --from=builder /root/install /usr/local

# Copy setup scripts into .bashrc so everything is defined when the user
# starts the container.
RUN echo ' . thisroot.sh' >> ~/.bashrc
RUN echo ' . thisdd4hep.sh' >> ~/.bashrc
