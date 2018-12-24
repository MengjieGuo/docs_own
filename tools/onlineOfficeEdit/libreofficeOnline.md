## libreoffice

[libreoffice -- office site](https://www.libreoffice.org/)

## libreoffice Online



## libreoffice Online docker

[libreofficeOnlineDocker -- docker hub](https://hub.docker.com/r/libreoffice/online/)


```
docker run -dt -p 127.0.0.1:9980:9980 -v /Users/screwman/Downloads/:/home/ -e "domain=192.168.0.104" -e "username=admin" -e "password=admin" --restart always libreoffice/online:master

-e "extra_params=--o:storage.filesystem[@allow]=true" 

docker run -dt -p 127.0.0.1:9980:9980 -v /home/:/home/ -e "domain=47\.94\.85\.21" -e "username=admin" -e "password=admin" -e "extra_params=--o:storage.filesystem[@allow]=true" --restart always libreoffice/online:master

docker run -dt -p 127.0.0.1:9980:9980 -v /home/:/home/ -e "domain=47\.94\.85\.21" -e "username=admin" -e "password=admin" -e "extra_params=–o:ssl.enable=false" --restart always libreoffice/online:master


https://127.0.0.1:9980
http://192.168.43.97:9980
https://192.168.43.97:9980

https://192.168.43.97:9980/loleaflet//etc/loolwsd/loolwsd.xml

 https://localhost:9980/loleaflet/dist/loleaflet.html?file_path=file:///home/1.doc&host=wss://localhost:9980

```


docker run -dt -p 127.0.0.1:9980:9980 -e "domain=192.168.43.97" -e "username=admin" -e "password=admin" --restart always --cap-add MKNOD thedarkknight/libreoffice-online-unlimited

brower access url
    https://localhost:9980/loleaflet/7b90179/loleaflet.html?file_path=file:///usr/bin/test/data/hello-world.odt


```
collabora/code

docker run -t -d -p 127.0.0.1:9980:9980 -e "domain=47\.94\.85\.21" \
        -e "username=admin" -e "password=admin" --restart always --cap-add MKNOD collabora/code
```