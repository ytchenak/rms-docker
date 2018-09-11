# rms-docker
Docker for RMS project

## build docker image
```
cd rms-py3
docker build -t rms-py37 .
```

## run container 
```
docker run --rm  -it rms-py37 bash
```

## export files to host computer 
```
docker run --rm -v<host temp dir>:/tmp -it rms-py37 tar cvzf /tmp/rms37.tar.gz /usr /etc/alternatives /home/pi

```

## copy the tar to raspberrypi
```
sftp pi@<raspberrypi IP>
put rms-py37.tar.gz 
```

## extract the tar to raspberrypi root directory
```
ssh pi@<raspberrypi IP>
sudo bash
cd /
tar xvf /home/pi/rms-py37.tar.gz

ldconfig
exit
```
