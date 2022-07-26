FROM node:18.4.0-slim

WORKDIR /app 

COPY . . 

RUN npm install  && \ 
    npm audit fix --force
    
EXPOSE 4000

CMD npm start 
