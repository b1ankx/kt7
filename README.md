# Dockerized Node.js Web Application

## Описание

Этот проект демонстрирует автоматическое развертывание веб-приложения с использованием Docker и GitLab CI. Веб-приложение написано на JavaScript с использованием Node.js.

## Содержание

1. Описание
2. Содержание
3. Установка
4. Docker-сборка
5. CI/CD 
6. Отчет

## Установка

1. Убедитесь, что у вас установлены Docker и GitLab Runner.
2. Клонируйте репозиторий:
   ```sh
   git clone https://gitlab.com/b1ankx/kt7.git
   cd kt7
   ```

## Docker-сборка

### Создание Docker-образа

1. Создайте файл `Dockerfile` со следующим содержимым:
   ```Dockerfile
   FROM node:14-slim

   WORKDIR /app

   COPY app.js .

   CMD ["node", "app.js"]
   ```

2. Создайте файл `app.js` со следующим содержимым:
   ```javascript
   const http = require('http');

   const hostname = '0.0.0.0';
   const port = 5000;

   const server = http.createServer((req, res) => {
     res.statusCode = 200;
     res.setHeader('Content-Type', 'text/plain');
     res.end('Hello, World!\n');
   });

   server.listen(port, hostname, () => {
     console.log(`Server running at http://${hostname}:${port}/`);
   });
   ```

3. Постройте и запустите Docker-образ:
   ```sh
   docker build -t my-app .
   docker run -d -p 80:5000 my-app
   ```

4. Перейдите в браузер и откройте [http://localhost](http://localhost), чтобы увидеть ваше приложение.

## CI/CD с GitLab

### Настройка GitLab CI

Создайте файл `.gitlab-ci.yml` со следующим содержимым:

```yaml
stages:
  - build
  - test
  - deploy

# Stage build
build:
  stage: build
  script:
    - docker build -t my-app .
    - docker tag my-app registry.gitlab.com/b1ankx/kt7/my-app:latest
    - docker push registry.gitlab.com/b1ankx/kt7/my-app:latest
  only:
    - main

# Stage test
test:
  stage: test
  script:
    - node app.js  # Запуск вашего файла app.js для тестирования
  only:
    - main

# Stage deploy
deploy:
  stage: deploy
  script:
    - docker pull registry.gitlab.com/b1ankx/kt7/my-app:latest
    - docker run -d -p 80:5000 my-app
  only:
    - main
```

Закоммитьте и запушьте файлы в репозиторий GitLab:
```sh
git add .
git commit -m "Initial commit"
git push origin main
```

### Настройка GitLab Runner

Убедитесь, что GitLab Runner настроен и зарегистрирован в вашем проекте. Для этого следуйте официальной документации GitLab.
