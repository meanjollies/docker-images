Simple 389-ds 
=============

This image provides a 389DS instance without immediate TLS authentication support.

## Configuration
Sample configurations are provided under the included conf directory. All default passwords are `password`.

## Usage

A simple `docker-compose up` will do, if you're into that sort of thing. Or you can build it manually:

```
docker build --tag="meanjollies/389ds" .
```

To start it:

```
docker run -itp 389:389 meanjollies/389ds
```

To connect to the instance, point your favorite LDAP browser to `localhost:389` and connect with `cn=Directory Manager` and `password`.

Need to reset the ldap instance? Just stop and remove the container. Then start a new instance.
