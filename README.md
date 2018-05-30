# Deploying to Surge with CircleCI

Updated Surge instructions for CircleCI 2.0. [Previous CircleCI found here.](https://surge.sh/help/integrating-with-circleci)


## Sample `.circleci/config.yml`file

```yml
# Javascript Node CircleCI 2.0 configuration file
#
# Check https://circleci.com/docs/2.0/language-javascript/ for more details
#
version: 2
jobs:
  # Sample build task
  build:
    docker:
      - image: circleci/node:7.10
    steps:
      - checkout
      # Download and cache dependencies
      - restore_cache:
          keys:
          - v1-dependencies-{{ checksum "package.json" }}
          # fallback to using the latest cache if no exact match is found
          - v1-dependencies-

      - run: yarn install
      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}
      # run placeholder test
      - run: yarn test
  # Deploy with Surge task
  deploy:
    docker:
      - image: circleci/node:7.10
    steps:
      - checkout
      - run: npm install surge
      - run: ./node_modules/surge/lib/cli.js --project ./ --domain inconclusive-transport.surge.sh
workflows:
  version: 2
  build-and-deploy:
    jobs:
      - build
      - deploy:
          requires:
              - build
```

## Setting up CircleCI environment configs
In the CircleCi admin screen of your project, go to Settings > Build Settings > Environment Variables.

|Name|Value|
|---|---|
|SURGE_LOGIN|YourSurgeEmailAccount@gmail.com|
|SURGE_TOKEN|Use `surge token` locally with surge cli to generate|
