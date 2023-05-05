# [Soketi](https://soketi.app/) with [Caddy](https://soketi.app/) on [EC2](https://aws.amazon.com/ec2/)

<br />
<div align='center'>
    <img src='./assets/soketi.png' width=100 height=100 />
    <img src='./assets/caddy.png' height=100 height=100 />
    <img src='./assets/ec2.png' width=100 height=100 />
</div>
<br />

A simple, fast, resilient, and open-source WebSocket server using Soketi with SSL in less than 10 minutes.

Before we get started you should already have an AWS account. If you don't, you can create one [here](https://aws.amazon.com/).

## 🚀 Launch an EC2 instance

- AMI: Ubuntu Server 22.04 LTS (HVM), SSD Volume Type
  - Architecture: 64-bit (Arm)
- Instance Type: t4g.nano (You can use a bigger instance if you want)
- Key pair (login)
  - Create a new key pair and download it to a safe place. You will need it to ssh into your instance. I named mine `caddy-soketi`.
- Network settings
  - Allow SSH traffic from anywhere
  - Allow HTTP traffic from anywhere
  - Allow HTTPS traffic from anywhere

After launching your instance, it will take a few minutes to initialize. In the meantime, you can allocate and associate an Elastic IP to your newly created instance. This will allow you to keep the same address even if you stop and start your instance.

Note the Public IPv4 DNS address of your instance. You will need it later to connect to your instance, configure Caddy, and to access your server from another application. It should look something like this:

`ec2-[ELASTIC_IP_SEPARATED_BY_DASHES].[REGION].compute.amazonaws.com`

## 🔗 Connect to your instance

Once your instance is ready, you can connect to it using SSH. The following command will connect you to your instance using the key pair you created earlier.

```bash
$ ssh -i "caddy-soketi.pem" ubuntu@[YOUR_PUBLIC_IPV4_DNS_ADDRESS_HERE]
```

If you get a `Permission denied` error, you might need to change the permissions of your key pair file.

```bash
$ chmod 400 caddy-soketi.pem
```

## 🐳 Install Docker

We will use Docker to run our WebSocket server. You can install Docker by following the [official docker instructions](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).

## 🗄️ Setup files

### Caddy files

Create the `Caddyfile` and directories by typing out the following commands.

```bash
$ mkdir ~/ubuntu/caddy/
$ cd ~/ubuntu/caddy/
$ touch Caddyfile
$ mkdir data
$ mkdir config
```

Then, edit `Caddyfile` with your favorite terminal editor and paste the contents from the example [`Caddyfile.example`](./Caddyfile.example) in this repository, changing `example.com` to your Public IPv4 DNS address that you noted earlier. Make sure to include `:443` at the end of your domain name. This reverse proxies your WebSocket server to port 443, which is the default port for HTTPS.

```bash
$ nano Caddyfile
```

### Docker files

Navigate back to your home directory to create a couple more files.

The first one is the `docker-compose.yaml` file with contents pasted from [`docker-compose.yaml`](./docker-compose.yaml) in this repository.

```bash
$ cd ~
$ touch docker-compose.yaml
$ nano docker-compose.yaml
```

The second one is the `soketi.Dockerfile` file with contents pasted from the example [`soketi.Dockerfile.example`](./soketi.Dockerfile.example) in this repository.

Don't forget to add your arguments for the environment variables.

```bash
$ touch soketi.Dockerfile
$ nano soketi.Dockerfile
```

## 🏃‍♂️ Run your WebSocket server

Finally, with everything in place, you can start your WebSocket server by running the following command.

```bash
$ docker compose -f ~/docker-compose.yaml up -d
```

That's it! You should now have a secure WebSocket server running on your EC2 instance. Verify by visiting your EC2 instance's Public IPv4 DNS address in your browser. You should see `OK` returned as the response.
