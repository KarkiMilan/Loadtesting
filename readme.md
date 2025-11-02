# Run Apache JMeter Tests with GitHub Actions

This repository demonstrates how to automate Apache JMeter performance tests using GitHub Actions — no manual setup or DevOps dependency required.

---

## Project Structure

.github/
└── workflows/
└── jmeter.yml # GitHub Actions workflow file

tests/
└── test.jmx # JMeter test plan


---

## How It Works

Every time you push to the `main` branch or create a pull request, GitHub Actions automatically:

1. Checks out your repository  
2. Sets up Java (required for JMeter)  
3. Downloads Apache JMeter  
4. Runs your JMeter test in non-GUI mode  
5. Generates an HTML performance report  
6. Uploads the report as a downloadable artifact  

---

## Workflow Overview

File: `.github/workflows/jmeter.yml`

```yaml
name: Run JMeter Tests

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  jmeter-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK (required for JMeter)
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Download Apache JMeter
        run: |
          wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.tgz
          tar -xvzf apache-jmeter-5.6.3.tgz
          mv apache-jmeter-5.6.3 jmeter

      - name: Run JMeter test
        run: |
          ./jmeter/bin/jmeter -n -t tests/test.jmx -l results.jtl -e -o results-html

      - name: Upload JMeter results
        uses: actions/upload-artifact@v4
        with:
          name: jmeter-report
          path: results-html