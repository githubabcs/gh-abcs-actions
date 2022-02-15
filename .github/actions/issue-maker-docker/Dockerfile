FROM node:slim

COPY package*.json ./

RUN npm install

COPY . .

CMD [ "node", "/src/index.js" ]
