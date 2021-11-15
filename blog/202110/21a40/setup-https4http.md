
## Let 'plain' HTTP accesses by HTTPS.

recently, I want let some http-api supports https. cuz lots 'stupid' things required https, e.g. modern-android-requests, https-website-requests.

but there seems not that 'easy'. I tried setup https for springboot, and literally failed:
- self-signed certifications are useless. needs viewers manually permits trust-the-certification. its 'ridiculous'.
- the Let's Encrypt certifications, not good to setup also. needs converts whats called keystores. and expires per 3 months. i.e. it's kind of annoying.

I am afraid that as a plain http-interface, there really not the necessary to handles https process. in principle, they are good as just responsibility for themselves jobs.

### Way to Solves.

Use of the Functionality of Redirect of Common-Http-Servers.

My http-server, Nginx, already got the Https Setup. (Let's Encrypt Certification).  
thus, Just let the http-server handles https-requests, then redirects them to the internal actual http-api.

nginx configuration:
```

server {
    ...
    location /apiv1/ {
        proxy_pass http://1.2.3.4:1234/;        # setup proxy
        proxy_redirect http://1.2.3.4:1234/ /;  # rewrite url
    }
}
```
Effect:   
access `https://hostname.com/apiv1/actual_api`  
redirect to -> `http://1.2.3.4:1234/actual_api`

note that `proxy_redirect` is important also, it 'recovers' the url.

