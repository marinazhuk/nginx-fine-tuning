# nginx-fine-tuning

Demo project for nginx configuration to: 
* cache only images requested at least twice
* add ability to drop nginx cache by request

## Services
### Nginx
host: localhost

port:80 

### web-app
host: localhost

port:8083

## Results

1. MISS
```bash
$ curl -I  http://localhost/cat1.jpg
```
```js
HTTP/1.1 200
Server: nginx/1.25.2
Date: Tue, 12 Dec 2023 17:05:21 GMT
Content-Type: image/jpeg
Content-Length: 52984
Connection: keep-alive
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
Last-Modified: Tue, 12 Dec 2023 15:13:20 GMT
Accept-Ranges: bytes
X-Cache-Status: MISS
X-Cache-Key: httpweb-app:8083/cat1.jpg
```
2. MISS
```bash
$ curl -I  http://localhost/cat1.jpg
```
```js
HTTP/1.1 200 
Server: nginx/1.25.2
Date: Tue, 12 Dec 2023 17:05:50 GMT
Content-Type: image/jpeg
Content-Length: 52984
Connection: keep-alive
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
Last-Modified: Tue, 12 Dec 2023 15:13:20 GMT
X-Cache-Status: MISS
X-Cache-Key: httpweb-app:8083/cat1.jpg
Accept-Ranges: bytes
```

3. HIT
```bash
$ curl -I  http://localhost/cat1.jpg
```
```js
HTTP/1.1 200 
Server: nginx/1.25.2
Date: Tue, 12 Dec 2023 17:06:19 GMT
Content-Type: image/jpeg
Content-Length: 52984
Connection: keep-alive
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
Last-Modified: Tue, 12 Dec 2023 15:13:20 GMT
X-Cache-Status: HIT
X-Cache-Key: httpweb-app:8083/cat1.jpg
Accept-Ranges: bytes
```

4. get response MD5 to compare it before and after cache revalidation
```bash
$ curl http://localhost/cat1.jpg | md5
```
```js
9789967e1cbebae14722b1ecd581e98f
```
5. change cat1.jpg  image to another in *web-app*
```bash
cd target/classes/static
ls
__cat1.jpg  cat1.jpg  cat2.png  cat4.gif  hello.html
 mv cat1.jpg _cat1.jpg
 mv __cat1.jpg cat1.jpg
```
6. revalidate cache
```bash
curl -I  http://localhost/cat1.jpg -H "Cache-Revalidate-Custom: true"
```
```js
HTTP/1.1 200
Server: nginx/1.25.2
Date: Tue, 12 Dec 2023 17:12:33 GMT
Content-Type: image/jpeg
Content-Length: 6435
Connection: keep-alive
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
Last-Modified: Tue, 12 Dec 2023 15:13:20 GMT
X-Cache-Status: BYPASS
X-Cache-Key: httpweb-app:8083/cat1.jpg
Accept-Ranges: bytes
```
7. MD5 of response changed after cache revalidation
```bash
$ curl http://localhost/cat1.jpg | md5
```
```js
85e3abc377c5977cce5e1925af8cce7a
```
