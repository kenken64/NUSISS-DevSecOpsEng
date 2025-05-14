# S-DOEA - Workshop 8 - Linting, DAST , SAST

## Pre-requisites

- Workshop 6 Github Repo

## Linting

1. Under the .github/workflows directory create a lint file with the below's content. Filename : lint.yml

```
name: "linting-tool-scan"

on:
  push:
    branches: [githubcicd]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]

    steps:
      - uses: actions/checkout@v2

      - name: Install Dependencies
        if: steps.cache-nodemodules.outputs.cache-hit != 'true'
        run: |
          npm ci --force

      - name: Installing JSHint
        run: |
          sudo npm install -g jshint

      - name: Change script permission
        run: |
          chmod +x scripts/jshint-script.sh

      - name: Run scan with JSHint
        run: scripts/jshint-script.sh

      - name: Archive production artifacts
        uses: actions/upload-artifact@v4
        with:
          name: linting tool report
          path: |
            ./JSHint-report

```

2. Create a new scripts directory on the root directory of your project folder

3. Under the scripts folder create a jshint script file with the below's content. Filename : jshint-script.sh

```
#!/bin/bash

jshint --exclude="node_modules/" --reporter=unix . > JSHint-report

echo $? > /dev/null
```

## SAST

1. Under the .github/workflows directory create the sast scan yml file with the below's content. Filename: sast-scan.yml

```
name: "sast-scan"

on:
  push:
    branches: [githubcicd]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]

    steps:
      - uses: actions/checkout@v2

      - name: Install Dependencies
        if: steps.cache-nodemodules.outputs.cache-hit != 'true'
        run: |
          npm ci --force

      - name: OWASP Dependency Check
        run: |
          wget https://github.com/dependency-check/DependencyCheck/releases/download/v12.1.1/dependency-check-12.1.1-release.zip
          unzip dependency-check-12.1.1-release.zip

      - name: Run scan with ODC
        run: |
          dependency-check/bin/dependency-check.sh --project "bitcoin" --nvdApiKey ${{ secrets.WORKSHOP6_NVD_API_KEY }} --out . --scan .

      - name: Archive production artifacts
        uses: actions/upload-artifact@v4
        with:
          name: sast report
          path: |
            ./dependency-check-report.html


```

2. Apply NVD API Key from the following website https://nvd.nist.gov/developers/request-an-api-key. The api key will be sent to your email address. Setup the key as github action secret (WORKSHOP6_NVD_API_KEY).

<br>
<img style="width:800px;height:500px; float: center;" src="./screens/sast2.png"/>
<br>

## DAST

1. Under the .github/workflows directory create the dast scan yml file with the below's content. Filename: zap-scan.yml

```
name: "owasp-scan"

on:
  push:
    branches: [githubcicd]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]

    steps:
      - uses: actions/checkout@v2

      - name: Change script permission
        run: |
          chmod +x scripts/zap-script.sh

      - name: ZAP scan
        run: scripts/zap-script.sh

      - name: Archive production artifacts
        uses: actions/upload-artifact@v4
        with:
          name: zap report
          path: |
            ./zap_baseline_report.html


```

2. Under the scripts directory create the dast script file with the below's content. Filename: zap-script.sh

```
#!/bin/bash

docker pull zaproxy/zap-stable
docker run -i zaproxy/zap-stable zap-baseline.py -t "https://kenken64.github.io/bitcoin-order-app/" -l PASS > zap_baseline_report.html

echo $? > /dev/null
```

## Final

1. Check in all the codes to the githubcicd branch

```
git add .
git commit -m "lint, dast,sast"
git push origin githubcicd
```

## Submission

1. Lint report
   <img src="./screens/Screenshot from 2022-09-16 04-09-44.png" >

2. Sast report

<img src="./screens/Screenshot from 2022-09-16 04-08-08.png" >

3. Dast report

<img src="./screens/Screenshot from 2022-09-16 04-05-43.png" >
