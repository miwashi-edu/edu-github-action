# edu-github-action

> The first script is for setting up a backend server with Node and Express
> It can be used to add actions to run unit tests or integration tests.
> 

```bash
cd ~
cd ws
git clone github-action
cd github-action
npm init -y
mkdir __tests__
curl -L https://gist.github.com/miwashi/7ea5be9d7a645f197440f5746fd340bc/raw/unit-test.js -o ./__tests__/unit-test.js
curl -L  https://gist.github.com/miwashi/642d5f51c7c93e6793356ad666f6be03/raw/server.js -o server.js

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
curl -L  https://gist.github.com/miwashi/48373261199b6f4a9b661d8f5b231c44/raw/hello-world.yml -o ./.github/workflows/hello-world.yml

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

## On Push of package.json

> We guard package.json and check when it is updated. If updated our action fires.

```yml
on:
  push:
    branches:
      - main
    paths:
      - 'package.json'
```

## Get a virtual machine and start a job

```yml
jobs:
  check-version-and-publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Important for the next step to have the complete git history
```

## Step 1: Install nodejs in our virtual machine.

```yml
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          registry-url: 'https://registry.npmjs.org/'
          # Enables Yarn/npm to read from .npmrc file
          # scope: '@your-scope' # Replace with your package's scope if it's scoped
```

## Step 2: Use EndBug/version-check@v2 action

> This action checks the version of package.json to see if it has changed.

```yml
      - name: Check for version change
        id: version_changed
        uses: EndBug/version-check@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          diff-search: true # Search for changes in the last commit
```

## Step 3:  Run npm install

> Just like if we would to it ourself, to do **npm run build** we need first to run **npm install**

```yml
      - name: Install dependencies
        run: npm install
```

## Step 4:  Build it

> Run **npm run build** to get the component into ./dist

```yml
      - name: Build component (if needed)
        run: npm run build
```

## Step 4:  Publish it

> We need to go to npmjs.com and generate a TOKEN, then add that token as NPM_TOKEN to github secrets.

```yml
      - name: Publish to NPM
        if: steps.version_changed.outputs.changed == 'true'
        run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

## Step 5: Notify Team:

> Here you add a step that notifies via mail, discord, slack or other.
> Example is slack and uses a webhook stored in github secrets as SLACK_WEBHOOK_URL_COMPONENTS.
> This: SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL_COMPONENTS }} allows me to have many webhook urls and use same script just
> change the secret assigned to SLACK_WEBHOOK_URL.

```yml
      - name: Notify Slack
        if: steps.version_changed.outputs.changed == 'true'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL_COMPONENTS }}
        run: |
          VERSION=$(jq -r '.version' package.json)
          LIBRARY_NAME=$(jq -r '.name' package.json)
          MESSAGE="Version $VERSION of $LIBRARY_NAME now published to npmjs."
          curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"${MESSAGE}\"}" $SLACK_WEBHOOK_URL
```





