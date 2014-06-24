# docker-nginx-php55-composer

A Dockerfile that installs the latest nginx, php-5.5 with opcache and php-fpm. Stream in your Laravel directory with the ```docker -v``` command and you're good to go. Or you can create a Dockerfile based on the image from this repo on the Docker index and add your files with a Dockerfile like this:

```

```

## Installation

```
$ git clone https://github.com/Joostvanderlaan/docker-nginx-php55-composer
$ cd docker-nginx-php55-composer
$ sudo docker build -t docker-nginx-php55-composer .
```

## Usage

To spawn a new instance of wordpress:

```bash
$ sudo docker run -p 80 -d docker-nginx-php55-composer
```

You'll see an ID output like:
```
6742949a1bae
```

Use this ID to check the port it's on:
```bash
$ sudo docker port 674 80 # Make sure to change the ID to yours!
```

This command returns the container ID, which you can use to find the external port you can use to access Laravel from your host machine:

```
$ docker port <container-id> 80
```

You can the visit the following URL in a browser on your host machine to get started:

```
http://127.0.0.1:<port>
```