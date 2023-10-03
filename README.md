# edu-github-action

> I denna del vill vi använda github och däför är det bra om du börjar med att skapa ett repo på github, sedan clonar det till ditt workspace.

```bash
cd ~
cd ws
git clone github-action
cd github-action
npm init -y
mkdir __tests__
curl -L https://gist.github.com/miwashiab/7ea5be9d7a645f197440f5746fd340bc/raw/unit-test.js -o ./__tests__/unit-test.js
curl -L  https://gist.github.com/miwashiab/642d5f51c7c93e6793356ad666f6be03/raw/server.js -o server.js
curl -L https://gist.github.com/miwashiab/3378fc2e4ab5d2691fa5978822721796/raw/.gitignore -o .gitignore

npm pkg set main="server.js"
npm pkg set description="Simple application to test commit hooks"
npm pkg set scripts.start="node server.js" 
npm pkg set scripts.dev="nodemon server.js"
npm pkg set scripts.test="jest"
npm install nodemon -D
npm install jest -D
npm install jest-runner-groups -D
npm install express
git add .
git commit -m "Initial Commit"
git push

mkdir -p ./.github/workflows
curl -L  https://gist.github.com/miwashiab/48373261199b6f4a9b661d8f5b231c44/raw/hello-world.yml -o ./.github/workflows/hello-world.yml

git add .
git commit -m "My first push with an action"
git push
```

## .github/hello-world.yml
```yml
name: hello-world
on: push
jobs:
  any-job:
    runs-on: ubuntu-latest
    steps:
      - name: any-step
        run: echo "Hello World!"
```
## .github/ci-cd-pipeline.yml

```bash
cd ~
cd ws
git clone edu-github-action
cd edu-github-action
# brew install gh / choco install gh
gh secret set DATABASE_URL
gh secret set HEROKU_API_KEY
gh secret set HEROKU_APP_NAME
gh secret set HEROKU_EMAIL
```


## .github/ci-cd-pipeline.yml
> Stulen, inte generaliserad ännu.
```yml
name: CI CD Pipeline
on: 
  push:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]
        mongodb-version: ['4.2', '4.4', '5.0', '6.0']
    
    steps:
      - name: Git checkout
        uses: actions/checkout@v3
      
      - name: Use Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - name: Start MongoDB
        uses: supercharge/mongodb-github-action@1.8.0
        with:
          mongodb-version: ${{ matrix.mongodb-version }}
          mongodb-db: ${{secrets.DATABASE_URL}}          
 

      - name: Install dependencies
        run: npm ci
      
      - name: Compile project
        run: npm run build --if-present
      
      - name: Run Tests
        run: npm test
        env:
          DATABASE_URL: ${{secrets.DATABASE_URL}}
          CI: true


  deploy:
    needs: [test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: akhileshns/heroku-deploy@v3.12.12 
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: ${{secrets.HEROKU_APP_NAME}} 
          heroku_email: ${{secrets.HEROKU_EMAIL}}
```

