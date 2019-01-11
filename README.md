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
docker run --rm -v<host temp dir>:/export -it rms-py37 tar cvzf /export/rms-py37.tar.gz /usr /etc/alternatives /home/pi

```
Note: for Docker on Windows you need enable shared drivers and set credential (Reset Credential)
## copy the tar to raspberrypi
```
sftp pi@<raspberrypi IP>
put rms-py37.tar.gz 
```

## extract the tar to raspberrypi root directory
```
ssh pi@<raspberrypi IP>
sudo su -
tar xvf /home/pi/rms-py37.tar.gz

ldconfig
exit
```
