# swarm-init

## Swarm init (ติดตั้ง swarm บน manager node)

```
docker swarm init
```
### copy token code ไปใช้งานที่ swarm1 และ swarm2
### เมื่อใช้้งานคำสั่ง token แล้วให้ตรวจเช็คโดยใช้คำสั่ง

```
docker node ls
```

![Image 12-3-2566 BE at 04 43](https://user-images.githubusercontent.com/87377798/224512757-06270fff-e6e9-4edb-b86c-d3bfa97d16ab.jpg)

## ติดตั้ง  portainer ใน (manager node)

```
curl -L https://downloads.portainer.io/ce2-17/portainer-agent-stack.yml -o portainer-agent-stack.yml
```

```
docker stack deploy -c portainer-agent-stack.yml portainer
```

## config ip ใน hosts files 


### Windows client C:\Windows\System32\drivers\etc\hosts
### Linux/Mac client /etc/hosts

![Image 6-3-2566 BE at 01 45](https://user-images.githubusercontent.com/87377798/222979641-a1aa0a98-96e6-4ffb-991d-a4bf90c72b3f.jpg)

## ติดตั้ง traefik

### เตรียม floder ชื่อว่า taefik 
### สร้างไฟล์ traefik-host.yml

### Step

### สร้าง network 

```
docker network create --driver=overlay traefik-public
```

### สร้าง tag ในโหนดเพื่อเก็บค่าไฟล์

```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
echo $NODE_ID
```

```
docker node update --label-add traefik-public.traefik-public-certificates=true $NODE_ID
```

### กำหนดค่า email,domain,username,password 

```
export EMAIL=user@smtp.com
export DOMAIN=traefik.cpedemo.local
export USERNAME=admin
export PASSWORD=changeMe
export HASHED_PASSWORD=$(openssl passwd -apr1 $PASSWORD)
echo $HASHED_PASSWORD

```
### ติดตั้ง traefix ใน stack ของ portainer

```
docker stack deploy -c traefik-host.yml traefik
```

### ทดสอบโดยการกดลิงค์

- traefik.cpedemo.local 

![Image 12-3-2566 BE at 04 48](https://user-images.githubusercontent.com/87377798/224513418-7d9ebf28-42b2-4e2a-8e46-8fa2b748dc24.jpg)

## ติดตั้ง swarmpit 

### ขั้นตอน 
 
### กำหนดค่า โดเมน

```
export DOMAIN=swarmpit.cpedemo.local
```

### กำหนดค่า  lable เพื่อใช้สำหรับ CouchDB database

```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
```
```
docker node update --label-add swarmpit.db-data=true $NODE_ID
```

### กำหนดค่า  lable เพื่อใช้สำหรับ Influx database

```
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
```

```
docker node update --label-add swarmpit.influx-data=true $NODE_ID
```

### deploy stack swarmpit

```
docker stack deploy -c swarmpit.yml swarmpit
```

### check stack ด้วยคำสั่งนี้

```
docker stack ps swarmpit
```

```
docker service logs swarmpit_app
```
### ตรวจเช็คโดย

- http://swarmpit.cpedemo.local

![Image 12-3-2566 BE at 05 03](https://user-images.githubusercontent.com/87377798/224514217-9fa6e06d-ee39-4877-878e-7285a499f369.jpg)

