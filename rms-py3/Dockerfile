FROM resin/raspberrypi3-python:3.6.6

RUN apt-get update 



RUN apt-get install build-essential cmake pkg-config
RUN apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
RUN apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev
RUN apt-get install libgtk2.0-dev
RUN apt-get install libatlas-base-dev gfortran


RUN apt-get install wget unzip

# to build python3 module
RUN apt-get install python3-dev  
RUN apt-get install python3-numpy 

# to enable gsstream
RUN apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev


ENV VERSION 3.4.3 

RUN cd /usr/src \
	&& wget --no-verbose -O opencv.zip https://github.com/Itseez/opencv/archive/${VERSION}.zip \
	&& unzip -q opencv.zip \
	&& wget --no-verbose -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/${VERSION}.zip \
	&& unzip -q opencv_contrib.zip 



RUN cd /usr/src/opencv-${VERSION}/  \
	&& mkdir build 

WORKDIR /usr/src/opencv-${VERSION}/build

RUN cmake -D CMAKE_BUILD_TYPE=RELEASE \
	    -D CMAKE_INSTALL_PREFIX=/usr/local \
	    -D INSTALL_PYTHON_EXAMPLES=OFF \
	    -D INSTALL_C_EXAMPLES=OFF \
	    -D OPENCV_EXTRA_MODULES_PATH=/usr/src/opencv_contrib-${VERSION}/modules \
	    -D BUILD_EXAMPLES=OFF \
		-D ENABLE_NEON=ON \
    	-D ENABLE_VFPV3=ON \
	    -D WITH_GSTREAMER=ON \
	    ..

RUN make -j8
RUN make install

# copy cv2.so
RUN cp ./lib/python3/cv2.cpython-34m.so /usr/local/lib/python3.6/site-packages/cv2.so

RUN apt-get purge python3-numpy 
RUN pip install numpy 


RUN ldconfig
#=============================================================================================


RUN cd /usr/src && git clone "https://github.com/CroatianMeteorNetwork/RMS.git" --depth 1
WORKDIR /usr/src/RMS


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

RUN cd /usr/src/RMS && python3 setup.py install


# #patch np.pi 
# COPY CyFunctions.diff /usr/src/RMS
# RUN patch -i CyFunctions.diff -p 1

RUN python3 -m RMS.StartCapture --help

RUN apt-get install vim
RUN rm -rf /usr/src/opencv*
