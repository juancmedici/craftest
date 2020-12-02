FROM node:15-alpine

RUN mkdir -p /code
WORKDIR /code

COPY package.json /code
COPY package-lock.json /code

RUN npm install

COPY . /code

EXPOSE 3000
CMD ["npm", "start"]
