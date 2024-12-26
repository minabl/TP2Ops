# Compte Rendu du TP2 : Docker et Docker Compose

## Introduction
Ce compte rendu documente les étapes et les résultats du TP2 portant sur l'utilisation de Docker et Docker Compose pour containeriser une application MERN . Ce projet vise à renforcer la compréhension des concepts de conteneurisation et d'orchestration des applications.

## Objectifs
Les principaux objectifs de ce TP étaient :
- **Containerisation des applications** : Apprendre à utiliser Docker pour créer des images et des conteneurs.
- **Orchestration avec Docker Compose** : Découvrir comment gérer plusieurs conteneurs en utilisant Docker Compose.
- **Déploiement d'une application MERN** : Comprendre les spécificités du déploiement d'une application MERN en environnement Docker.
## Structure du Projet
```
 mern-app/
    ├── client/               
    │   ├── Dockerfile         
    │   ├── package.json       
    │   ├── src/              
    │   └── public/            
    ├── server/               
    │   ├── Dockerfile        
    │   ├── package.json       
    │   ├── src/              
    │   └── routes/            
    ├── docker-compose.yml 
```

  
## Étapes Réalisées
   1. Création des Dockerfiles
      1.1 Dockerfile pour le Serveur (Express)
       ```
        FROM node:lts-alpine
        
        WORKDIR /app
        
        COPY package*.json ./
        
        RUN npm install
        
        COPY . .
        
        EXPOSE 5000
        CMD ["npm","start"]
        ```
      1.2 Dockerfile pour le Client (React)
      ```
        FROM node:lts-alpine

        WORKDIR /app
        
        COPY package*.json ./
        
        RUN npm install
        
        COPY . .
        
        RUN npm run build
        RUN npm install -g serve
        
        
        EXPOSE 3000
        
        CMD ["serve", "-s", "build", "-l", "3000"]
        ```
   3. Création du Réseau Docker
      Un réseau Docker a été créé pour permettre la communication entre les conteneurs :
        ```
        docker network create mern-network
        ```
   4. Exécution de MongoDB
      Un conteneur MongoDB a été lancé pour servir de base de données :
      
      ```
        docker run -d --name mongodb --network mern-network mongo
      ```
   6. Exécution des Conteneurs du Serveur et du Client
      ```
      - docker run -d --name server --network mern-network -p 5000:5000 mern-server
      - docker run -d --name client --network mern-network -p 3000:3000 mern-client
      ```
      
   7.  Création du Fichier Docker Compose
   ```
          version: "3.8"
          services:
            mongodb:
              image: mongo
              container_name: mongodb
              networks:
                - mern-network
              ports:
                - "27017:27017"
            server:
              build: ./server
              container_name: server
              networks:
                - mern-network
              ports:
                - "5000:5000"
              depends_on:
                - mongodb
              environment:
                - MONGO_URI=mongodb://mongodb:27017/mern-app
                - PORT=5000
                
            client:
              build: ./client
              container_name: client
              networks:
                - mern-network
              ports:
                - "3000:3000"
              
              depends_on:
                - server
          networks:
            mern-network:
              driver: bridge

   ```
