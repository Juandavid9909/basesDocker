FROM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

# El . significa que copie todo
COPY . .

# Realizar testing
RUN npm run test

# Eliminar archivos y directorio no necesarios
RUN rm -rf tests && rm -rf node_modules

# Únicamente las dependencias de prod
RUN npm install --prod

# Comando run de la imagen
CMD ["node", "app.js"]