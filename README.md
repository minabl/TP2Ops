# Compte Rendu du TP2 : Docker et Docker Compose

## Table des Matières

- [Introduction](#introduction)
- [Objectifs](#objectifs)
- [Structure du Projet](#structure-du-projet)
- [Étapes Réalisées](#étapes-réalisées)
  - [1. Création des Dockerfiles](#1-création-des-dockerfiles)
    - [1.1 Dockerfile pour le Serveur (Express)](#11-dockerfile-pour-le-serveur-express)
    - [1.2 Dockerfile pour le Client (React)](#12-dockerfile-pour-le-client-react)
  - [2. Création du Réseau Docker](#2-création-du-réseau-docker)
  - [3. Exécution de MongoDB](#3-exécution-de-mongodb)
  - [4. Exécution des Conteneurs du Serveur et du Client](#4-exécution-des-conteneurs-du-serveur-et-du-client)
  - [5. Création du Fichier Docker Compose](#5-création-du-fichier-docker-compose)
- [Images du Projet](#images-du-projet)

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
### Images du Projet

Voici quelques captures d'écran de l'application :
<div>
 <img src="https://github.com/user-attachments/assets/f9d0c489-5bc4-4334-82cf-f4fe628fe640" alt="Capture d'écran de l'application React" width="600" />
 <img src="https://github.com/user-attachments/assets/be6f602c-967c-4e5a-9dd0-7777ee0e953e" alt="Capture d'écran de l'application React" width="600" />
 <img src="https://github.com/user-attachments/assets/64596989-2275-418e-9797-9ec422ccd1b7" alt="Capture d'écran de l'application React" width="600" />
</div>
<div>
 <img src="https://github.com/user-attachments/assets/4b9bf2e1-667f-4cb9-973b-61a921c4405a" alt="Capture d'écran de l'application React" width="600" />
 <img src="https://github.com/user-attachments/assets/49c40f30-f590-417a-97e3-4f53b5e9c1ba" alt="Capture d'écran de l'application React" width="600" />
 <img src="https://github.com/user-attachments/assets/64596989-2275-418e-9797-9ec422ccd1b7" alt="Capture d'écran de l'application React" width="600" />
 
 <img src="https://github.com/user-attachments/assets/177ff685-379e-44f0-ae35-3f41c8f6148e" alt="Capture d'écran de l'application React" width="600" />
 <img src="https://github.com/user-attachments/assets/0f090d43-18f8-4706-9c7a-f9a7b78c55ad" alt="Capture d'écran de l'application React" width="600" />

</div>
