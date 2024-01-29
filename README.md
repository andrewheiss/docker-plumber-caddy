# Running a containerized Plumber API on DigitalOcean with Docker Compose and Caddy <!-- omit in toc -->

This is an updated version of [this older repository](https://github.com/andrewheiss/docker-plumber-nginx-letsencrypt) that shows how to get plumber to work with nginx and Let's Encrypt. That works fine, but it takes a lot of configuration and a whole separate process for getting and renewing HTTPS certificates. The newer (and magical) Caddy webserver handles HTTPS automatically and it only involved very minimal configuration, so I recommend using it instead of nginx. 

Caddy makes everything like a billion times easier.

This repository shows a basic barebones example, with just two services or containers defined in `docker-compose.yml`:

1. `plumber`: This runs a simple plumber app in `plumber-thing/` that exposes the API on port 8000. This is exposed on an internal local network that only Docker Compose can see—the broader internet can't see it. 
2. `caddy`: This is a web server that automatically makes HTTPS work. When you run stuff locally, it creates self-signed SSL certificates; when you run it on a publicly accessible server, it creates SSL certificates through Let's Encrypt. The server configuration in `caddy/Caddyfile` forwards all traffic to `https://api.localhost` to the plumber app that's running on port 8000.

And that's it!

So to do this yourself on DigitalOcean, create a new cheapo Droplet ([use this link to create a DigitalOcean account and get $200 for free, if you want](https://m.do.co/c/cec0de11762e)), and follow these guides to get it set up:

- [Initial server setup](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-22-04) for setting up SSH, correct user groups, firewall, etc.
- [Installing Docker](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04) for installing, um, Docker
- [Installing Docker Compose](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04) for installing Docker Compose

Then, using a DNS service (iwantmyname, GoDaddy, whatever), point a domain name (like api.example.com) to the new DigitalOcean server's IP address.

Put this repository on the server and edit `caddy/Caddyfile` with the domain name.

From your server's terminal, run this

```sh
docker compose -f docker-compose.yml -d up
```

Go to `https://api.example.com/__docs__/` and you should see the Plumber documentation page, and you're done!
