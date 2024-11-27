# Imagem node recente e estável para o front em React + Vite
FROM node:20 AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

## Comentários com os comandos para rodar sem o Nginx, fica em torno de 1.93GB
##Exponha a porta que a aplicação vai rodar, default do Vite
#EXPOSE 5173
##Define o comando para rodar a aplicação usando Vite
#CMD ["npm", "run", "dev", "--", "--host"]

FROM nginx:1.26.2
COPY --from=build /app/dist /usr/share/nginx/html

# Expõe a porta padrão do Nginx
EXPOSE 80

# Inicializa o Nginx
CMD ["nginx", "-g", "daemon off;"]