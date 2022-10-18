# GitHub-Actions ( CI/CD )
- Deployment Methods
  - If you push to the "deploy" branch, It is deployed to the heroku server.
  
- Workflow List
  - Check
  
  ```
  name: Check

  on:
    push:
      branches: [ "main", "master", "develop", "deploy" ]

  jobs:
    build:
      runs-on: ubuntu-latest

      strategy:
        matrix:
          node-version: [14.x, 15.x, 16.x]

      steps:
        - uses: actions/checkout@v3
        
        - name: Use Node.js ${{ matrix.node-version }}
          uses: actions/setup-node@v3
          with:
            node-version: ${{ matrix.node-version }}
            
        - run: npm install
        
        - run: npm run build
  ```
  
  - Deploy
  
  ```
  name: Deploy

  on:
    workflow_run:
      workflows: [ "Check" ]
      types: [completed]
      branches: [ "deploy" ]

  jobs:
    deploy:
      runs-on: ubuntu-latest

      steps:
        - uses: actions/checkout@v3

        - uses: akhileshns/heroku-deploy@v3.12.12
          with:
            heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
            heroku_app_name: ${{ secrets.HEROKU_APP_NAME }}
            heroku_email: ${{ secrets.HEROKU_EMAIL }}
            usedocker: true
  ```
