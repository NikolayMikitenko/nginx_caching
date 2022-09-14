# Cache images using Nginx WebServer for Fine Tuning 
## Target 
Cache only images, that were requested at least twice 
Drop cache for specific file only by request 
 
## Prepare 
Run docker container from main project folder 
`docker-compose up`

## Use it 
### 1. Request image first time: 
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
 
### 2. Request image second time: 
`curl -D - localhost/images/example.png --output temp.png` 
 
In output we get *X-Cache-Status: MISS* and file size *4477k*:
```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Server: nginx/1.21.6
Date: Wed, 14 Sep 2022 09:01:01 GMT
Content-Type: text/plain
Content-Length: 4585
Connection: keep-alive
Last-Modified: Tue, 13 Sep 2022 14:51:47 GMT
ETag: "63209903-11e9"
Accept-Ranges: bytes
X-Cache-Status: MISS

100  4585  100  4585    0     0  4477k      0 --:--:-- --:--:-- --:--:-- 4477k
```
 
### 3. Request image third time and get it from cache: 
`curl -D - localhost/images/example.png --output temp.png` 

In output we get *X-Cache-Status: HIT* and file size *4477k*:
``` 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Server: nginx/1.21.6
Date: Wed, 14 Sep 2022 09:12:18 GMT
Content-Type: text/plain
Content-Length: 4585
Connection: keep-alive
Last-Modified: Tue, 13 Sep 2022 14:51:47 GMT
ETag: "63209903-11e9"
X-Cache-Status: HIT
Accept-Ranges: bytes

100  4585  100  4585    0     0  4477k      0 --:--:-- --:--:-- --:--:-- 4477k
```

### 4. Replace image with other
```
mv data/images/example.png data/images/example2.png 
mv data/images/example1.png data/images/example.png
``` 

### 5. Request image fourth time and get old one from cache: 
`curl -D - localhost/images/example.png --output temp.png` 

In output we get *X-Cache-Status: HIT* and file size *4477k*:
``` 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Server: nginx/1.21.6
Date: Wed, 14 Sep 2022 09:17:41 GMT
Content-Type: text/plain
Content-Length: 4585
Connection: keep-alive
Last-Modified: Tue, 13 Sep 2022 14:51:47 GMT
ETag: "63209903-11e9"
X-Cache-Status: HIT
Accept-Ranges: bytes

100  4585  100  4585    0     0  4477k      0 --:--:-- --:--:-- --:--:-- 4477k
``` 

### 6. Request image fifth time, get new one and refresh cahe: 
`curl -D - localhost/images/example.png --output temp.png -H "update-cache:true"` 
 
In output we get *X-Cache-Status: BYPASS* and file size *2299k*:
``` 
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0HTTP/1.1 200 OK
Server: nginx/1.21.6
Date: Wed, 14 Sep 2022 09:20:22 GMT
Content-Type: text/plain
Content-Length: 2355
Connection: keep-alive
Last-Modified: Tue, 13 Sep 2022 11:21:58 GMT
ETag: "632067d6-933"
X-Cache-Status: BYPASS
Accept-Ranges: bytes

100  2355  100  2355    0     0  2299k      0 --:--:-- --:--:-- --:--:-- 2299k
``` 
