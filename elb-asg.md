# ELB 

## High Availability (HA)

- Va souvent de pair avec le scaling horizontal (plusieurs machines,
  pas une seule grosse machine)
- HA = faire tourner ton application dans AU MOINS 2 data centers (AZ)
- Objectif : survivre à la perte complète d'un data center (une AZ)

<img width="277" height="336" alt="image" src="https://github.com/user-attachments/assets/6b8782ab-8a4c-4350-8fc6-0eeab1fe3645" />

### Deux types de HA
- **Passive** = une ressource de secours ne travaille pas en temps normal,
  prend le relais uniquement en cas de panne (failover)
  → ex : RDS Multi-AZ (standby)
- **Active** = plusieurs ressources travaillent EN MÊME TEMPS en temps
  normal, le trafic est juste redistribué en cas de panne
  → ex : ASG multi-AZ + Load Balancer


## What is load balancing

Loaf Balancers are servers that forward traffic to multiple servers downstream

<img width="517" height="217" alt="image" src="https://github.com/user-attachments/assets/23504cba-c43b-428d-a990-1eb6ac160930" />

## Pourquoi utiliser un Load Balancer ?

- Répartir la charge entre plusieurs instances (scaling horizontal)
- Exposer UN SEUL point d'accès (DNS fixe) même si les instances derrière changent
- Gérer les pannes d'instances de façon transparente (retire automatiquement les instances en panne du trafic)
- Health checks réguliers pour détecter les instances saines/malsaines
- SSL Termination : le LB gère le HTTPS à la place des instances (simplifie la gestion des certificats, allège le CPU des instances)
- Sticky sessions (via cookie) : garder un utilisateur sur la même instance mais peut créer un déséquilibre de charge
- Haute disponibilité multi-AZ : route vers plusieurs AZ, survit à la panne d'une AZ
- Sépare le trafic public (LB exposé) du trafic privé (instances cachées dans un subnet privé)

## Types de Load Balancers

AWS propose 4 types de Load Balancers managés :

**Classic Load Balancer (CLB)** — v1, ancienne génération — 2009
- Supporte : HTTP, HTTPS, TCP, SSL (TCP sécurisé)
- Legacy, à éviter pour du nouveau déploiement (remplacé par ALB/NLB)

**Application Load Balancer (ALB)** — v2, nouvelle génération — 2016
- Supporte : HTTP, HTTPS, WebSocket
- Couche 7 (Application) — comprend le CONTENU des requêtes

**Network Load Balancer (NLB)** — v2, nouvelle génération — 2017
- Supporte : TCP, TLS (TCP sécurisé), UDP
- Couche 4 (Transport) — ultra performant, faible latence

**Gateway Load Balancer (GWLB)** — 2020
- Opère à la couche 3 (Network) — protocole IP
- Utilisé pour déployer des appliances tierces (firewalls, IDS/IPS...)
  de façon transparente devant le trafic

**Certains Load Balancers peuvent être configurés en interne ou externe**


### Modèle OSI — couches utiles pour les Load Balancers

- Couche 7 (Application, HTTP) → **ALB**
- Couche 4 (Transport, TCP/UDP) → **NLB**
- Couche 3 (Réseau, IP) → **GWLB**
- Règle : plus le numéro de couche est ÉLEVÉ, plus le LB est "intelligent" mais spécialisé (HTTP only). Plus le numéro est BAS, plus c'est rapide et polyvalent, mais "aveugle" au contenu


## Application Load Balancer (ALB) — détails

- Répartit le trafic entre plusieurs applications HTTP sur des machines
  différentes (via des target groups)
- Peut aussi router vers plusieurs applications SUR LA MÊME machine
  (ports différents) — utile pour les conteneurs
- Supporte HTTP/2 et WebSocket
- Supporte les redirections automatiques (ex: HTTP → HTTPS)

### Routing intelligent (couche 7) vers différents target groups
- Par PATH de l'URL : example.com/users vs example.com/posts
- Par HOSTNAME : one.example.com vs other.example.com
- Par Query String ou Headers : selon les paramètres de la requête

### Cas d'usage idéal : microservices & conteneurs
- Parfait pour Docker / Amazon ECS
- Port mapping : suit automatiquement les ports dynamiques attribués
  aux conteneurs (pas besoin de reconfigurer manuellement)

### ALB vs CLB
- CLB = pas de routing intelligent → 1 CLB par application nécessaire
- ALB = routing par path/hostname/headers → 1 SEUL ALB peut gérer
  plusieurs applications/microservices
 
<img width="648" height="267" alt="image" src="https://github.com/user-attachments/assets/33716651-cee3-438e-8355-da123749a202" />


### ALB - Target Groups

- EC2 Instances - HTTP
- EC2 Asks - HTTP
- Lambda functions - HTTP request is translated into a JSON event
- IP adresses -must be private IP
- ALB can route to multipe target groups
- Health checks are at the target group level

<img width="660" height="321" alt="image" src="https://github.com/user-attachments/assets/1cba7fa6-6843-4cb6-b0cf-8b252451ca44" />

### ALB - Good to know

- Fixed hostname (XXX.region.elb.amazonaws.com)
- The application servers don't see the IP of the client directly
  - The true IP of the client is inserted in the header X-Forwarded-For
  - We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)

<img width="607" height="136" alt="image" src="https://github.com/user-attachments/assets/0dacf8e3-38ff-4282-976f-9078a8b8f681" />



## Nerwork Load Balancer (NLB) — détails

- Network load balancers (Layer 4) allow to :
  - Forward TCP & UDP traffic to your instances
  - Handle millions of request per secons
  - Ultra low latency
- **NLB has one static IP per AZ** and suppors assigning Elastic IP (helpgul for whitelisting specific IPà
  
  <img width="663" height="261" alt="image" src="https://github.com/user-attachments/assets/fc692338-269e-40b7-b825-61834e1a9ee1" />

### NLB - Target Groups

- EC2 instances 
- IP Adresses - must be private IPs 
- ALD

<img width="622" height="180" alt="image" src="https://github.com/user-attachments/assets/1b6f6dbe-c314-4fd6-8c69-7964907112cd" />


## Gateway Load Balancer (GWLB) — détails

- Deploy, scale, and manange a fleet of 3rd party network virtual appliances in AWS
- Example: Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspection Systems, payload manipulation
- Operates at Layer 4 (IP level)
- Combbienes the following functions:
  - Transperent Network Gateway - single entry/exit for all traffic
  - Load Balancer - distrubutes traffic to your virtual appliances
- Uses the GENEVE protocol on port 6081
  
<img width="228" height="352" alt="image" src="https://github.com/user-attachments/assets/934d5cae-3568-4223-b9d5-db7c75e3d0ef" />

### GWLB - Target Groups

- EC2 Instances
- IP Adresses - must be private

<img width="551" height="213" alt="image" src="https://github.com/user-attachments/assets/aa3c9a4c-e867-46c5-b604-efa8d9d39315" />

## Elastic Load Balancers

### Sticky Sessions

- It's possible to impliment stickness so that the same client is always redirect to the same instance behind a load balancer
- This works for CLB, ALB, NLB
- The "cookie" used for stickness has an expiration date you control
- Use case: make sure the user doesn't has expiration date you control
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances

<img width="241" height="341" alt="image" src="https://github.com/user-attachments/assets/50ae1084-9748-4cc1-98f9-dcf37596b659" />

**Cookies Names**

- **Application-based Cookes**
  - Custom Cookie
    - Generated by target
    - Can include any custom attributes required by the application
    - Cookie name must be specified individually for each target group
    - Don't use AWSSALB, AWSSALBAPP, AWSALBTG
  - Application cookie
    - Generated by the load balancer
    - Cookie name is AWSALBAPP
- **Duration-based Cookies**
  - Cookie generated by the load balancer
  - Cookies name is AWSSALB or ALB, AWSELB for CLB


### Cross Zone Load Balancing

<img width="737" height="315" alt="image" src="https://github.com/user-attachments/assets/02a0bc98-35ae-4d5c-96f9-31a9e4802f51" />


- **ALB**
  - Enabledd by default
  - No charges for inter AZ data
- **NLB & GWLB**
  - Disabled by default
  - You pay charges for inter AZ data
- **CLB**
  - Disabled by default
  - No charges for inter AZ data if enabled

### SSL Certificates

**Essentials**
- SSL = old, obsolete, TLS = the current version used anywhere but people still say SSL out of habit
- The certificate does two things: encrypt traffic + proves the server's identity
- Before exchanging data, there's a handshake: client andd server agree on the encryption algorith and the cloent verifies the certificate

**Trust: the CA**
- The cetificate is signed by a CA that your browser already trust by default
- If the certificate isn't signed by a trusted CA then warning

<img width="617" height="102" alt="image" src="https://github.com/user-attachments/assets/07eb4346-9496-45a9-b19b-1db12740a129" />

**On AWS**
- ACM automatically generates and renews certificates, free of charge
- The certificate is attached to the **ALB's HTTPS listener**. The ALB does the decryption then often talks HTTP to backend


### Server Name Indication (SNI) 

- SNI solves the problem of loading multiple SSL certification onto one web server
- It is a "newer" protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake
- The serverwill then find the correct certificate or return the default one

**Note**:
- Only works for ALB & NLB, CloudFront

<img width="347" height="327" alt="image" src="https://github.com/user-attachments/assets/f68f661b-954f-4022-8cbe-1149e6888ad4" />

### Connection Draining (or Derigistration Delay for ALB & NLB)  

- Time to complete "in-flight requests" whille the instance is de-registering or unhealthy
- Stops sending new reqiest to the EC2 Instance which is de-registering

<img width="318" height="302" alt="image" src="https://github.com/user-attachments/assets/795d848c-a71a-4a79-9caf-bdb73a0a0b67" />

## Auto Scaling Group  

**Un ASG  ajuste automatiquement le nombre d'instances EC2 (en ajoute ou en retire) selon la charge, pour garder ton appli performante sans payer pour des instances inutilisées.**

<img width="697" height="307" alt="image" src="https://github.com/user-attachments/assets/733eb818-9cdf-4291-840f-c7779ddbb7ae" />

 
<img width="672" height="326" alt="image" src="https://github.com/user-attachments/assets/75dd678a-05bc-48b8-99c7-7bb606630820" />


### ASG Group Attributes

- A **Launch Template**
  - AMI + Instance Type
  - EC2 USer Data
  - EBS Volume
  - Security Groups
  - SSH Key
  - IAM Roles
  - Network + Subnets Info
  - Load Balancer Info
- Min Size / Max Size / Initial Capacity
- Scaling Policies

<img width="257" height="287" alt="image" src="https://github.com/user-attachments/assets/247c3ca9-aece-4ca5-bc85-8cea15ecbedc" />

### CloudWatch Alarms & Scaling

- It s possible to scale an ASG based on CloudWatch alarms
- An alarm monitors a metric (such as Average CPU, or a custom metric)
- Metrics such as Average CPU are computed for the overall ASG instances
- Based on alarm:
  - We can create scale out policies (increase)
  - We can create scale in polocies (decrease)

<img width="655" height="126" alt="image" src="https://github.com/user-attachments/assets/73659004-d7c8-4f48-a1f0-e661004aa31c" />
