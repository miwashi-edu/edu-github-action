# edu-github-action

## Sending mail

> Denna kräver att ni kan SMTP inställningar.
> Det finns andra mail klienter än den som  används här i https://github.com/marketplace?type=actions


```yaml
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

    - name: Send Success Email
      if: success()  # This step will run only if the previous steps were successful
      uses: dawidd6/action-send-mail@v3
      with:
        server_address: smtp.example.com
        server_port: 465
        username: ${{ secrets.MAIL_USERNAME }}
        password: ${{ secrets.MAIL_PASSWORD }}
        subject: Tests were successful
        to: someone@somewhere
        from: Your Repository <noreply@example.com>
        body: The component tests ran successfully.
```

