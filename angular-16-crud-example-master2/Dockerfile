# Stage 1 : Utilisation d'une image minimaliste ( alpine ) officielle
#de node en spécifiant une version précise
FROM node:20-alpine as build
# Optimisation de l'application node pour la production
# ENV NODE_ENV production
# Définition du répertoire de travail
WORKDIR /app
#Exécution des processus en utilisant l'utilisateur "node" fourni par l'image Node.js à des fins de sécurité.
# USER node
# COPY --chown=node:node . /app
#Copier package.json et package-lock.json dans le répertoire de travail
COPY package*.json ./
# Installer Angular CLI globalement
RUN npm install  @angular/cli@latest
# Installation des dépendances de production
RUN npm install   
# Copie du code source dans l'image de construction
COPY . .
# Build l'application Angular
RUN ng build
# Stage 2: Créer un conteneur léger avec Nginx pour servir l'application Angular.
FROM nginx:1.25.3-alpine
# Copier l'application Angular construite à partir de l'étape précédente
#dans le répertoire webroot de Nginx
COPY --from=build /app/dist/angular-16-crud/ /usr/share/nginx/html
# Exposer le port 80
EXPOSE 80
# Démarrer nginx au démarrage du container
CMD ["nginx", "-g", "daemon off;"]
