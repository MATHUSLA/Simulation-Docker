#
# Build a docker container to build DD4hep applications.
# Docker build command:
#   docker build -t gordonwatts/dd4hep:v01.07.01 .
#
FROM andreadotti/geant4-dev:10.4.p02

LABEL Author="Gordon Watts <gwatts@uw.edu>"

USER root

# Need to fetch stuff from the internet
RUN apt-get update; \
    apt-get install -y wget; \
    apt-get install -y git dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev libxft-dev libxext-dev python; \
    apt-get install -y freeglut3-dev libgl1-mesa-dev libglu1-mesa-dev mesa-libGLU mesa-libGL; \
    apt-get install -y libboost-all-dev

# CLHEP - version should probably match G4 buil:
#   http://proj-clhep.web.cern.ch/proj-clhep/clhep23.html
#   https://geant4.web.cern.ch/support/download

RUN mkdir ~/CLHEP; \
    cd CLHEP; \
    wget -q http://proj-clhep.web.cern.ch/proj-clhep/DISTRIBUTION/tarFiles/clhep-2.4.0.4.tgz; \
    tar -zxf clhep-2.4.0.4.tgz; \
    mkdir build; \
    cd build; \
    cmake -DCMAKE_INSTALL_PREFIX=/usr/local ~/CLHEP/2.4.0.4/CLHEP; \
    cmake --build . -- -j2; \
    cmake --build . --target install
    
# ROOT

RUN mkdir -p ~/root-source; \
        cd ~/root-source; \
        wget -q https://root.cern.ch/download/root_v6.12.06.source.tar.gz; \
        tar -zxf root_v6.12.06.source.tar.gz

RUN mkdir -p ~/root-build; \
    cd ~/root-build; \
    cmake -DCMAKE_INSTALL_PREFIX=/usr/local ~/root-source/root-6.12.06; \
    cmake --build .; \
    cmake --build . --target install

# Finally, DD4hep

RUN mkdir -p ~/DD4hep; \
    cd ~/DD4hep; \
    wget -q https://github.com/AIDASoft/DD4hep/archive/v01-07-01.tar.gz; \
    tar -zxf v01-07-01.tar.gz

RUN mkdir -p ~/DD4hep/DD4hep-01-07-01/build; \
    cd ~/DD4hep/DD4hep-01-07-01/build; \
    source /usr/local/bin/thisroot.sh; \
    cmake -DDD4HEP_USE_GEANT4=ON -DBoost_NO_BOOST_CMAKE=ON -DDD4HEP_USE_LCIO=ON -DBUILD_TESTING=ON -DGeant4_DIR=/usr/local/geant4/lib/Geant4-10.4.2 -DROOT_DIR=$ROOTSYS ..
