# edu-github-action

## Component Tests

```yml
name: Run Component Tests

on:
  push:

jobs:
  test:
    name: Run npm run test:component
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'

    - name: Install Dependencies
      run: npm ci

    - name: Run Component Tests
      run: npm run test:component
```
