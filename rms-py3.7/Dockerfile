# debian is used instead raspbian because 'illegal instruction' error while importing cv2 into python.
# FROM resin/rpi-raspbian:stretch
FROM resin/raspberrypi3-debian:stretch

# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

RUN apt-get update 
RUN apt-get install libffi-dev libbz2-dev liblzma-dev libsqlite3-dev libncurses5-dev libgdbm-dev zlib1g-dev libreadline-dev libssl-dev tk-dev build-essential libncursesw5-dev libc6-dev openssl git
RUN apt-get install wget unzip vim


ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.7.2


RUN set -ex \
	\
	&& wget --no-verbose -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
	&& wget --no-verbose -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
	&& export GNUPGHOME="$(mktemp -d)" \
	&& { command -v gpgconf > /dev/null && gpgconf --kill all || :; } \
	&& rm -rf "$GNUPGHOME" python.tar.xz.asc \
	&& mkdir -p /usr/src/python \
	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
	&& rm python.tar.xz \
	\
	&& cd /usr/src/python \
	&& gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
	&& ./configure \
		--build="$gnuArch" \
		--enable-loadable-sqlite-extensions \
		--enable-shared \
		--with-system-expat \
		--with-system-ffi \
		--without-ensurepip \
	&& make -j "$(nproc)" \
	&& make install \
	&& ldconfig \
	\
	&& find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' + \
	&& rm -rf /usr/src/python \
	\
	&& python3 --version

# make some useful symlinks that are expected to exist
RUN cd /usr/local/bin \
	&& ln -s idle3 idle \
	&& ln -s pydoc3 pydoc \
	&& ln -s python3 python \
	&& ln -s python3-config python-config

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 18.0



RUN set -ex; \
	\
	wget --no-verbose -O get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' +; \
	rm -f get-pip.py

#=============================================================================================
# add pi user and root to sudusers list
RUN useradd -d /home/pi -p pi -m pi \
	&& echo 'root ALL=(ALL) ALL' >> /etc/sudoers \
	&& echo 'pi ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

USER pi
ENV HOME_DIR=/home/pi
WORKDIR ${HOME_DIR}

#=============================================================================================
ENV vRMS=${HOME_DIR}/vRMS
RUN python3 -m venv ${vRMS}


RUN ${vRMS}/bin/pip install --upgrade setuptools

RUN ${vRMS}/bin/pip install numpy

RUN ${vRMS}/bin/pip install gitpython
RUN ${vRMS}/bin/pip install paramiko
RUN sudo apt-get install libfreetype6-dev pkg-config
RUN ${vRMS}/bin/pip install matplotlib
RUN ${vRMS}/bin/pip install pyephem
RUN ${vRMS}/bin/pip install cython
RUN sudo apt-get install libjpeg8-dev
RUN ${vRMS}/bin/pip install Pillow
RUN ${vRMS}/bin/pip install astropy
RUN sudo apt-get install libblas-dev liblapack-dev gfortran 
RUN ${vRMS}/bin/pip install scipy


#-------------------------------------------------------------------------------------------

RUN sudo apt-get install build-essential cmake pkg-config
RUN sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
RUN sudo apt-get update 
RUN sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev
RUN sudo apt-get install libgtk2.0-dev
RUN sudo apt-get install libatlas-base-dev gfortran

# to enable gsstream
RUN sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

ENV OPENCV_VERSION 3.4.5

RUN cd ${HOME_DIR} \
	&& wget --no-verbose -O opencv.zip https://github.com/Itseez/opencv/archive/${OPENCV_VERSION}.zip \
	&& unzip -q opencv.zip \
	&& wget --no-verbose -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/${OPENCV_VERSION}.zip \
	&& unzip -q opencv_contrib.zip 

RUN cd ${HOME_DIR}/opencv-${OPENCV_VERSION}/  \
	&& mkdir build 

WORKDIR ${HOME_DIR}/opencv-${OPENCV_VERSION}/build

RUN cmake -D CMAKE_BUILD_TYPE=RELEASE \
	    -D CMAKE_INSTALL_PREFIX=${vRMS}/local \
	    -D INSTALL_PYTHON_EXAMPLES=OFF \
	    -D INSTALL_C_EXAMPLES=OFF \
	    -D OPENCV_EXTRA_MODULES_PATH=${HOME_DIR}/opencv_contrib-${OPENCV_VERSION}/modules \
	    -D BUILD_EXAMPLES=OFF \
		-D ENABLE_NEON=ON \
    	-D ENABLE_VFPV3=ON \
	    -D WITH_GSTREAMER=ON \
	    -DPYTHON_DEFAULT_EXECUTABLE=${vRMS}/bin/python3 \
	    -D BUILD_opencv_python3=ON \
	    ..

RUN make -j8
RUN make install
RUN sudo ldconfig
RUN rm -rf ${HOME_DIR}/opencv*
#-------------------------------------------------------------------------------------------


RUN git clone https://github.com/CroatianMeteorNetwork/RMS.git {HOME_DIR}/source/RMS 
WORKDIR {HOME_DIR}/source/RMS

#RUN python3 setup.py install

RUN ${vRMS}/bin/python3 -m RMS.StartCapture --help

