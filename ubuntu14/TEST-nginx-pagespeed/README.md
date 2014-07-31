#Run a NGINX forward proxy with support for Google's Pagespeed module.

##How to use
Run the docker image
Set your browser to use the running Docker container as a proxy
Load any site and you will be able to see the effect of Google NGINX pagespeed module. You must load the site twice to see any effect. (Check it out in Firefox + Firebug for example)




tail /var/log/nginx/error.log

-v /var/run/docker.sock:/tmp/docker.sock

docker build -t joost/pagespeed . && docker run -i -p 80:80 -t joost/pagespeed /bin/bash


docker build -t joost/pagespeed . && docker run -i -p 80:80 -v ~/dockerfiles/ubuntu14/TEST-nginx-pagespeed/nginx/nginx.conf:/etc/nginx/nginx.conf -t joost/pagespeed /bin/bash
