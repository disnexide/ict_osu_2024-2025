# Лабораторная работа №4: Написание оптимизированных CI/CD файлов

## Цель работы:
на примерах "плохого" и "хорошего" скриптов показать, как правильно организовать процесс непрерывной интеграции (CI) и непрерывного развертывания (CD), чтобы избежать ошибок и повысить качество программного обеспечения.


**CI/CD** — метод, позволяющий выпускать новые версии продукта, не управляя процессом вручную. Это помогает избежать человеческих ошибок, оптимизировать и ускорить процесс деплоя.

Пример "плохого" CI/CD:
`stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - npm install
    - echo "Building the project..."
    - npm run build
  only:
    - master

test_job:
  stage: test
  script:
    - npm test
    - echo "Running tests..."

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - scp ./build/* user@production-server:/var/www/html
  only:
    - master`

    Разберем ошибки:

    **Нет кэширования зависимостей**
    Установка зависимостей `npm install` происходит на каждом запуске. Если повторять одни и те же действия несколько раз, процесс сборки сильно замедляется.

    **Деплой сразу на прод**
    Деплой на стейдж не проводится, то есть нет должного тестирования. Есть огромный риск развернуть нерабочую версию с багами.

    **Только ветка master**
    Запуск ограничивается только веткой master, и тестирование фич на других ветках не выполняется.

    **Прямой SCP для деплоя**
    SCP потенциально не обеспечивает должной безопасности и, например, с ним не получится управлять откатами или версионированием.

    **Нет управления ошибками**
    Статусы выполнения команд не проверяются, и чтобы узнать, где произошла ошибка, остается только гадать на кофейной гуще (или переписывать все полностью).


  Исправим вышеописанные ошибки и получим пример "хорошего" CI/CD:

  `# Good CI/CD file
stages:
  - build
  - test
  - deploy_staging
  - deploy_production

cache:
  paths:
    - node_modules/

build_job:
  stage: build
  script:
    - npm ci
    - echo "Building the project..."
    - npm run build
  only:
    - merge_requests
    - master

test_job:
  stage: test
  script:
    - npm test
  allow_failure: false
  only:
    - merge_requests
    - master

deploy_staging_job:
  stage: deploy_staging
  script:
    - echo "Deploying to staging..."
    - rsync -avz ./build/ user@staging-server:/var/www/staging
  only:
    - merge_requests

deploy_production_job:
  stage: deploy_production
  script:
    - echo "Deploying to production..."
    - rsync -avz ./build/ user@production-server:/var/www/html
  only:
    - master
  when: manual
  environment:
    name: production
    url: https://production-server`
