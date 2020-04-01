FROM node
RUN mkdir -p $PWD/app
WORKDIR $PWD/app
COPY package.json $PWD/app
RUN npm install
COPY . $PWD/app
EXPOSE 3000
CMD ["npm", "start"]