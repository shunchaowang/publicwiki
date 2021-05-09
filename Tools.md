### Apache Benchmark to do load testing
* Get testing
> `ab -n 100 -c 10 <url>` run a get test on <url> with 100 requests and 10 concurrency
* Post data
1. Create a file to store the form data
> name0=value0&name1=value1
2. Pass the file to the command
> `ab -n 100 -c 10 -p <file> -T 'application/x-www-form-urlencoded' <url>`
* Post json data
1. Create a file to store the form data in json format
> {"name0": "value0", "name1": "value1"}
2. Pass the file to the command
> `ab -n 100 -c 10 -p <file> -T 'application/x-www-form-urlencoded' <url>`
* Post multipart form data
1. Create a file to store the form data
```
--123456
Content-Disposition: form-data; name="name0"

value0
--123456
Content-Disposition: form-data; name="name1"

value1
--123456
```
2. Pass the file to the command 
> `ab -n 100 -c 10 -p <file> -T 'multipart/form-data; boundary=123456' <url>`
* Get/Post with authentication
> Log in through web portal and examine the JSESSIONID, pass the value to ab command `ab -C JSESSIONID=...`
