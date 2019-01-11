FROM sgtwilko/rpi-raspbian-opencv

RUN apt-get update 


RUN apt-get install wget unzip


RUN apt-get install git

# TODO: remove
RUN cd /usr/src && git clone "https://github.com/CroatianMeteorNetwork/RMS.git" --depth 1
WORKDIR /usr/src/RMS


RUN pip install gitpython

RUN apt-get install libffi-dev
RUN pip install -U setuptools
RUN apt-get install libssl-dev
RUN pip install paramiko

RUN apt-get install libfreetype6-dev
RUN apt-get install pkg-config
RUN pip install matplotlib

RUN pip install pyephem
RUN pip install cython


RUN apt-get install libjpeg8-dev
RUN pip install Pillow

RUN apt-get purge python-numpy 
RUN pip install numpy 
RUN pip install astropy==2.0.8

RUN apt-get install libblas-dev liblapack-dev gfortran 
RUN pip install scipy

RUN apt-get install vim
RUN rm -rf /usr/src/opencv*


# add pi user and root to sudusers list
RUN useradd -d /home/pi -p pi -m pi \
	&& echo 'root ALL=(ALL) ALL' >> /etc/sudoers \
	&& echo 'pi ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

USER pi
WORKDIR /home/pi

RUN git clone "https://github.com/CroatianMeteorNetwork/RMS.git" 
WORKDIR /home/pi/RMS

#patch -- by some reason assigning np.pi to double variable case crash. 
COPY CyFunctions.diff .
RUN patch -i CyFunctions.diff -p 1


RUN sudo python setup.py install


RUN python -m RMS.StartCapture --help



