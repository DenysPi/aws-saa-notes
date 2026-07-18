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
- ALB can toute to multipe target groups
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
