# FROM resin/rpi-raspbian:stretch
FROM resin/raspberrypi3-debian:stretch

# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

RUN apt-get update 
RUN apt-get install libffi-dev libbz2-dev liblzma-dev libsqlite3-dev libncurses5-dev libgdbm-dev zlib1g-dev libreadline-dev libssl-dev tk-dev build-essential libncursesw5-dev libc6-dev openssl git
RUN apt-get install wget


ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.7.0


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

#-------------------------------------------------------------------------------------------

RUN apt-get install build-essential cmake pkg-config
RUN apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
RUN apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev
RUN apt-get install libgtk2.0-dev
RUN apt-get install libatlas-base-dev gfortran


RUN apt-get install wget unzip

# to build python3 module
# RUN apt-get install python3-dev  
# RUN apt-get install python3-numpy 

# to enable gsstream
RUN apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

RUN pip install numpy

ENV OPENCV_VERSION 3.4.3 

RUN cd /usr/src \
	&& wget --no-verbose -O opencv.zip https://github.com/Itseez/opencv/archive/${OPENCV_VERSION}.zip \
	&& unzip -q opencv.zip \
	&& wget --no-verbose -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/${OPENCV_VERSION}.zip \
	&& unzip -q opencv_contrib.zip 



RUN cd /usr/src/opencv-${OPENCV_VERSION}/  \
	&& mkdir build 

WORKDIR /usr/src/opencv-${OPENCV_VERSION}/build

RUN cmake -D CMAKE_BUILD_TYPE=RELEASE \
	    -D CMAKE_INSTALL_PREFIX=/usr/local \
	    -D INSTALL_PYTHON_EXAMPLES=OFF \
	    -D INSTALL_C_EXAMPLES=OFF \
	    -D OPENCV_EXTRA_MODULES_PATH=/usr/src/opencv_contrib-${OPENCV_VERSION}/modules \
	    -D BUILD_EXAMPLES=OFF \
		-D ENABLE_NEON=ON \
    	-D ENABLE_VFPV3=ON \
	    -D WITH_GSTREAMER=ON \
	    -DPYTHON_DEFAULT_EXECUTABLE=/usr/local/bin/python3 \
	    -D BUILD_opencv_python3=ON \
	    ..

RUN make -j8
RUN make install
RUN ldconfig
RUN rm -rf /usr/src/opencv*

#=============================================================================================
RUN pip install gitpython

RUN apt-get install libffi-dev
RUN pip install paramiko

RUN apt-get install libfreetype6-dev
RUN apt-get install pkg-config
RUN pip install matplotlib

RUN pip install pyephem
RUN pip install cython


RUN apt-get install libjpeg8-dev
RUN pip install Pillow

RUN pip install astropy

RUN apt-get install libblas-dev liblapack-dev gfortran 
RUN pip install scipy


RUN apt-get install vim


# add pi user and root to sudusers list
RUN useradd -d /home/pi -p pi -m pi \
	&& echo 'root ALL=(ALL) ALL' >> /etc/sudoers \
	&& echo 'pi ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

USER pi
WORKDIR /home/pi

RUN git clone "https://github.com/CroatianMeteorNetwork/RMS.git" 

WORKDIR /home/pi/RMS
RUN sudo python3 setup.py install


RUN python3 -m RMS.StartCapture --help

