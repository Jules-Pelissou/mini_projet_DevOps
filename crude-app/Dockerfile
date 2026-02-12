# =========================
# 1️⃣ Étape Build
# =========================
FROM node:18-alpine AS build
WORKDIR /app

# Copier uniquement les fichiers nécessaires pour installer les dépendances
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier le reste du projet
COPY . .

# Build de l'application React avec PUBLIC_URL à la racine
RUN PUBLIC_URL=/ npm run build

# =========================
# 2️⃣ Étape Production
# =========================
FROM nginx:alpine

# Supprimer la config nginx par défaut
RUN rm -rf /usr/share/nginx/html/*

# Copier le build React dans nginx
COPY --from=build /app/build /usr/share/nginx/html

# Copier la configuration nginx personnalisée
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Exposer le port 80
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
