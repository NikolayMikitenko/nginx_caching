# Cache images using Nginx WebServer for Fine Tuning 
## Target 
Cache only images, that were requested at least twice 
Drop cache for specific file only by request 
 
## Prepare 
Run docker container from main project folder 
`docker-compose up`

## Use it 
### 1. Request image first time :
`curl -D - localhost/images/example.png --output temp.png` 
 
In output we get *X-Cache-Status: MISS* and file size *4477k*:
```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Server: nginx/1.21.6
Date: Wed, 14 Sep 2022 08:48:39 GMT
Content-Type: text/plain
Content-Length: 4585
Connection: keep-alive
Last-Modified: Tue, 13 Sep 2022 14:51:47 GMT
ETag: "63209903-11e9"
Accept-Ranges: bytes
X-Cache-Status: MISS

100  4585  100  4585    0     0  4477k      0 --:--:-- --:--:-- --:--:-- 4477k
```
 
### 2. Request image second time :
`curl -D - localhost/images/example.png --output temp.png` 
 
