# NgrxShoppingList

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 13.2.6.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.

*************************************************************
To install ngRx store package : npm i @ngrx/store

## Adding a local repository to GitHub using Git
https://docs.github.com/en/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-locally-hosted-code-to-github

$ git remote add origin  <REMOTE_URL> 
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
$ git push origin main
# Pushes the changes in your local repository up to the remote repository you specified as the origin
 

************************* Implementing CI - Creating Github Actions
Install Npm package
ng add angular-cli-ghpages

Name of Workflow
name: Angular GitHub CI # 👈

Trigger on Main Branch
Let’s trigger the job whenever main branch got new push.
on:
  push:
    branches:
      - main # 👈

Node Versions
Let’s run on multiple node versions on ubuntu-latest.
jobs:
  ci:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [10.x, 12.x]
        # 👆
Note we are using node10 and node12/ Therefore, at a time there will be parallelly 2 jobs will run one for 10.x and one for 12.x

Checkout source code
Let’s checkout the code first.
steps:
  - uses:
      actions/checkout@v2
      # 👆

Setup Node Environment
- name: Use Node.js $
- uses: actions/setup-node@v1
  with:
    node-version:
      $
      # 👆

Use GitHub Cache
Let’s use GitHub Cache to save node_modules. Learn more about Caching GitHub Workflow dependencies here.
- name: Cache node modules
  id: cache-nodemodules
  uses: actions/cache@v2
  env:
    cache-name: cache-node-modules
  with:
    # caching node_modules
    path: node_modules # 👈 path for node_modules folder
    key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
    # 👆 name of the chache key includes package-lock.json
    restore-keys: |
      ${{ runner.os }}-build-${{ env.cache-name }}-
      ${{ runner.os }}-build-
      ${{ runner.os }}-

Install Dependencies
Next we must install node packages conditionally. Learn more here…
- name: Install Dependencies
  if:
    steps.cache-nodemodules.outputs.cache-hit != 'true'
    # 👆 if cache hits the skip this step.
  run: npm ci

Building Project
Let’s run build in production mode to compile our project. We need to pass -- --prod so that ng build --prod will be executed.
- name: Build
  run: npm run build -- --prod

Linting Project
Let’s run linting.
- name: Lint
  run: npm run lint

Testing Project
Let’s run test in production mode. We need to make sure while running Test:
✔️ we are using chrome headless browser "browsers": "ChromeHeadless" and
✔️ Generating code coverage "codeCoverage": true,
✔️ Ignoring Source Map "sourceMap": false
👁️ Make sure not in watch mode ` “watch”: false`
All of the above settings can be done in angular.json file.

Navigate to angular.json file identify project name.

Go to test object. projects.sample-app.architect.test
Add below code configuration for production.
"configurations": {
  "production": {
    "sourceMap": false,
    "codeCoverage": true,
    "browsers": "ChromeHeadless",
    "watch": false
  }
},
It will look like this:

       "test": {
          "builder": "@angular-devkit/build-angular:karma",
          "configurations": { 👈
            "production": {
              "sourceMap": false,
              "codeCoverage": true,
              "browsers": "ChromeHeadless",
              "watch": false
            }
          },👈
          "options": {
            "main": "src/test.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.spec.json",
            "karmaConfig": "karma.conf.js",
            "assets": [
              "src/favicon.ico",
              "src/assets"
            ],
            "styles": [
              "src/styles.scss"
            ],
            "scripts": []
          }
        },
Lets add below script in the main.yml file. Which will use above production test configuration while running in build machine.

- name: Test
  run: npm run test -- --prod

