# AWS Voting App

A simple distributed application running across multiple Docker containers, designed to be deployed on AWS.

## Architecture

![Architecture diagram](architecture.excalidraw.png)

*   A front-end web app in [Python](/vote) which lets you vote between two options
*   A [Redis](https://hub.docker.com/_/redis/) which collects new votes
*   A [.NET](/worker/) worker which consumes votes and stores them in…
*   A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
*   A [Node.js](/result) web app which shows the results of the voting in real time

## Deploying on AWS EC2

This application is designed to be deployed on an AWS EC2 instance.

### 1. Launch an EC2 Instance

First, you'll need to launch a new EC2 instance in your AWS account.

1.  **Choose an AMI**: A good choice is the **Amazon Linux 2 AMI** or a recent **Ubuntu Server AMI**.
2.  **Choose an Instance Type**: A `t2.micro` or `t3.micro` instance will be sufficient for this application.
3.  **Configure Security Group**: Create a new security group and add the following inbound rules to allow traffic to your application:
    *   **SSH** (Port 22) from your IP address to allow you to connect to the instance.
    *   **Custom TCP** (Port 8083) from anywhere (0.0.0.0/0) for the `vote` app.
    *   **Custom TCP** (Port 8082) from anywhere (0.0.0.0/0) for the `result` app.
4.  **Key Pair**: Select an existing key pair or create a new one. You'll need the private key (`.pem` file) to connect to the instance.

### 2. Connect to Your EC2 Instance

Once the instance is running, connect to it using SSH.

```bash
ssh -i /path/to/your/AWS.pem ec2-user@<your-ec2-public-ip>
```

*   Replace `/path/to/your/AWS.pem` with the path to your private key.
*   Replace `<your-ec2-public-ip>` with the public IP address of your EC2 instance.

### 3. Install Docker and Docker Compose

After connecting to your instance, you need to install Docker and Docker Compose.

```bash
# Update the installed packages and package cache
sudo yum update -y

# Install Docker
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user

# Log out and log back in to apply the new group permissions
exit
```

After logging back in, install Docker Compose:

```bash
# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 4. Get Your Code on the Instance

You need to get your project code onto the EC2 instance. The easiest way is to clone it from your Git repository.

```bash
git clone <your-git-repository-url>
cd aws-project
```

If you don't have a Git repository, you can upload the files using `scp`.

### 5. Build and Run the Application

Now you can build and run your application using Docker Compose.

```bash
docker compose up --build
```

This command will build the Docker images for each service and start the containers.

### 6. Access Your Application

Once the containers are running, you can access the `vote` and `result` apps in your browser:

*   **Vote App**: `http://<your-ec2-public-ip>:8083`
*   **Result App**: `http://<your-ec2-public-ip>:8082`

## Other Deployment Options

### Local Development

Download [Docker Desktop](httpshttps://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:8083](http://localhost:8083), and the `results` will be at [http://localhost:8082](http://localhost:8082).

### Docker Swarm

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

### Kubernetes

The folder k8s-specifications contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.