# rms-docker
Docker for RMS project

## build docker 
```
cd rms-py3
docker build -t rms-py3 .
```

## export /usr to host computer c:\TEMP directory
```
docker run --rm -vC:\TEMP:/tmp -it rms-py3 tar cvzf /tmp/usr3.tar.gz /usr

```

## copy the tar to raspberrypi
```
sftp pi@<raspberrypi IP>
put usr3.tar.gz 
```

## extract the tar to /usr
```
ssh pi@<raspberrypi IP>
sudo bash
cd /
tar xvf /home/pi/usr3.tar.gz

ldconfig
exit
```