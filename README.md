# rms-docker
Docker for RMS project

## build docker image
```
cd rms-py3
docker build -t rms-py3 .
```

## run container 
```
docker run --rm  -it rms-py3 bash
```


## export /usr and /home/pi to host computer's c:\TEMP directory
```
docker run --rm -vC:\TEMP:/tmp -it rms-py3 tar cvzf /tmp/rms-py3.tar.gz /usr

```

## copy the tar to raspberrypi
```
sftp pi@<raspberrypi IP>
put rms-py3.tar.gz 
```

## extract the tar to /usr of raspberrypi
```
ssh pi@<raspberrypi IP>
sudo bash
cd /
tar xvf /home/pi/urms-py3.tar.gz

ldconfig
exit
```
