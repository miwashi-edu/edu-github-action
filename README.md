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

## Tutorials

[Discord](https://www.youtube.com/watch?v=-KDQqWNK3Tw)  
[Hosting](https://www.youtube.com/watch?v=Bnd4IO3f2hU&t)  
[Functions](https://davelms.medium.com/deploy-firebase-functions-using-github-actions-7dbafbd4df77)  
[Test](https://www.youtube.com/watch?v=WW6ZUw9IExA)  
[Test](https://www.youtube.com/watch?v=mda8mH4dRuo)  
[Release](https://www.youtube.com/watch?v=sVl6De94evo)

