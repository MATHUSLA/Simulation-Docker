#
# Build a docker container to build DD4hep applications.
# Docker build command:
#   docker build -t gordonwatts/DD4hep:v01.07.01 .
#
FROM andreadotti/geant4-dev:10.4.p02

LABEL Author="Gordon Watts <gwatts@uw.edu>"

USER root

# Need to fetch stuff from the internet
RUN apt-get update; \
    apt-get install -y wget; \
    apt-get install -y git dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev libxft-dev libxext-dev python3

# We need root down and downloaded

RUN mkdir -p ~/root-source; \
        cd ~/root-source; \
        wget -q https://root.cern.ch/download/root_v6.12.06.source.tar.gz; \
        tar -zxf root_v6.12.06.source.tar.gz

RUN mkdir -p ~/root-build; \
    cd ~/root-build; \
    cmake -DCMAKE_INSTALL_PREFIX=/usr/local ~/root-source/root-6.12.06; \
    cmake --build .; \
    cmake --build . --target install

RUN mkdir -p ~/DD4hep; \
    cd ~/DD4hep; \
    wget -q https://github.com/AIDASoft/DD4hep/archive/v01-07-01.tar.gz; \
    tar -zxf v01-07-01.tar.gz

#RUN mkdir -p ~/DD4hep/v01-07-01/build; \
#    cd ~/DD4hep/v01-07-01/build; \
#    source /usr/local/bin/thisroot.sh; \
#    cmake -DDD4HEP_USE_GEANT4=ON -DBoost_NO_BOOST_CMAKE=ON -DDD4HEP_USE_LCIO=ON -DBUILD_TESTING=ON -DGeant4_DIR=$G4INSTALL/lib/Geant4-10.4.0 -DROOT_DIR=$ROOTSYS ..
