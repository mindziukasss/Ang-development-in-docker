# Angular Development in Docker

1.Create main folder - [project name - app ]

2.The in folder create -  .git, Dockerfile, docker-compose.yml and node_modules folder.

3.In the Dockerfile add
```bash
    FROM node:lts
    RUN mkdir /home/node/app && chown node:node /home/node/app
    RUN mkdir /home/node/app/node_modules && chown node:node /home/node/app/node_modules
    WORKDIR  /home/node/app
    USER node
```

4.In the docker-compose.yml add
```bash
version: '3.7'
services:
  app:
    build: .
    command: echo 'ready'
    volumes:
      - ./:/home/node/app
      - node_modules:/home/node/app/node_modules
volumes:
  node_modules:
```

5.Run command
```bash
docker-compose build
```

6.Run command
```bash
docker-compose run --rm app bash
```

7.You will be in the container shell ready to work with npx, 
to use the Angular CLI to create a new app we want to move back a folder and generate a new project.
(--strict=true - because TypeScript)
```bash
cd ..
npx -p @angular/cli ng new app --strict=true
exit
```

8.Add to Dockerfile. Now this file is
```bash
FROM node:lts
RUN mkdir /home/node/app && chown node:node /home/node/app
RUN mkdir /home/node/app/node_modules && chown node:node /home/node/app/node_modules
WORKDIR  /home/node/app
USER node
COPY --chown=node:node package.json package-lock.json ./
RUN npm ci --quiet
COPY --chown=node:node . .
```

9.Add to docker-compose. Now this file is
```bash
version: '3.7'
services:
services:
  app:
    build: .
    command: sh -c "npm start"
    ports:
      - 4200:4200
    working_dir: /home/node/app
    volumes:
      - ./:/home/node/app
      - node_modules:/home/node/app/node_modules
volumes:
  node_modules:
```

10.Add to package.json "scripts"
```bash
 "scripts": {
    "ng": "ng",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "start": "ng serve --host 0.0.0.0",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
```

11.Run command
```bash
docker-compose build
```

12.Start 
```bash
docker-compose up -d
```

13.Let's go browser and link
```bash
http://localhost:4200/
```

14.Stop
```bash
docker-compose down
```
