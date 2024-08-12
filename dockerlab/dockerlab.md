# DokerLab

```jsx
cd dockerlab
vagrant up
vagrant ssh
```

install all needed packages (apt, nano, git, …)

### Portainer

```python
docker run -d -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce

```

[https://192.168.56.20:9443](https://192.168.56.20:9443/)  

- User: admin   Pass: studenthogent

### Setup docker

Dockerfile

```jsx
cd /dockerlab/webapp
nano Dockerfile
```

```jsx
# Use an official Node.js LTS (Long Term Support) image as the base image
FROM node:18

# Create a directory for the application code inside the container
WORKDIR /app

# Copy the package.json and yarn.lock files to the container
COPY package.json yarn.lock ./

# Install application dependencies
RUN yarn install --frozen-lockfile

# Copy the rest of the application source code to the container
COPY . .

# Expose port 3000 for the application
EXPOSE 3000

# Command to start the application when the container starts
CMD ["yarn", "start"]
```

```jsx
docker build -t webapp .
docker run -d -p 3000:3000 webapp
```

Then in local (not vm): [http://192.168.56.20:3000/animals](http://192.168.56.20:3000/animals) 

In vm:  curl localhost:3000/animals

### Docker compose

```jsx
nano docker-compose.yml   (in webapp)
```

docker-compose.yml

```jsx
version: '3'
services:
  webapp:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ./database:/app/database
```

```jsx
docker compose up -d
docker compose up -d --force-recreate
docker-compose -f <your-compose-file>.yml up -d
docker restart <container_id_or_name>
docker volume inspect portainer_data
```

### Database setup:

```bash
docker exec -it [CONTAINER_NAME_OR_ID] sh  #ga in volume met shell
#verander de compose file en zet volume path juist
volumes:
      - ./database:/app/database
#check if logs ("Fake data generated")
docker logs [CONTAINER_NAME_OR_ID]
```

 

### Mongodb + tests

```bash
version: '3'

services:
  webapp:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ./database:/app/database
    environment:
      - MONGO_URL=mongodb://database:27017/animals
    depends_on:
      - database

  database:
    image: mongo:4.4
    volumes:
      - mongo_data:/data/db
    ports:
      - "27017:27017"

  test:
    build: .
    command: yarn test
    environment:
      - API_URL=http://webapp:3000
    depends_on:
      - webapp

volumes:
  mongo_data:
```

(verander  test.js naar test.spec.js in package.json)

```bash
docker compose up -d
docker compose up test
```

docker hub:

```bash
docker tag webapp tommavl/webapp
docker login
docker push tommavl/webapp

docker compose exec webapp yarn test
```

### Vragen

- Start de api op
- toon docker-compose
- toon de portainer
- toon images
