![Ảnh chụp màn hình 2024-11-27 102917](https://github.com/user-attachments/assets/be7ffa74-03e2-49f0-8403-6246dfae8257)# Lab #1, 21110754, Nguyen Ngoc Hong Anh, INSE_03FIE

## Task 1: Firewall Configuration

### **Question 1**: 
Setup a set of VMs/containers in a network configuration of 2 subnets (1, 2) with a router forwarding traffic between them. Relevant services are also required:
- The router initially cannot route traffic between subnets.
- PC0 on subnet 1 serves as a web server on subnet 1.
- PC1 and PC2 on subnet 2 act as client workstations on subnet 2.

### **Answer 1**:

### 1. **Setup Docker Networks and Containers**

#### Create Docker Networks:
- docker network create --driver bridge router-network
- docker network create --driver bridge subnet1
- docker network create --driver bridge subnet2
![Create router network](https://github.com/user-attachments/assets/9eb8f126-a65f-43d1-b2c9-97add9f4ac4a)

This creates three Docker networks:
- router-network for the router container.
- subnet1 for the web server.
- subnet2 for the client workstations.
### Web Server on Subnet 1:
#### Run the following command to start the nginx web server on subnet 1:
docker run -d --name web-server --network subnet1 -p 80:80 nginx
#### This will start the web server container and map port 80 to the host.
Create Client Workstations on Subnet 2:
Run these commands to create two workstations on subnet 2:
![Ảnh chụp màn hình 2024-11-27 102917](https://github.com/user-attachments/assets/6e5c478f-e0ba-40de-be7d-26f713d5328b)
docker run -d --name workstation1 --network subnet2 ubuntu:latest tail -f /dev/null
docker run -d --name workstation2 --network subnet2 ubuntu:latest tail -f /dev/null
These workstations will represent client PCs in subnet 2.
![docker](https://github.com/user-attachments/assets/19b819dd-e8f2-430e-a739-58cf816797d1)
Configure NAT on the Router:
To allow the router to forward traffic between subnets, you need to set up NAT (Network Address Translation) with iptables:
docker exec -it router bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
This sets up the NAT rule that will masquerade the traffic exiting the router to appear as if it's coming from the router itself.

### 2. Test Communication Between the Subnets
Ping from Workstation 1 to the Web Server:

docker exec -it workstation1 ping -c 4 web-server
This tests if Workstation 1 can reach the Web Server on subnet 1.

Ping from Workstation 2 to the Web Server:

docker exec -it workstation2 ping -c 4 web-server
This tests if Workstation 2 can also reach the Web Server on subnet 1.

Verify Router Configuration:
Check the routing table and NAT rules on the router container:

docker exec -it router bash
ip route show
iptables -t nat -L
This will display the current routing table and NAT rules applied to the router.


