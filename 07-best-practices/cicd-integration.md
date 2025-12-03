# 7.9 CI/CD Integration

Tích hợp API testing vào CI/CD pipeline đảm bảo quality gates và automated regression testing.

## Mục Tiêu

- ✅ Newman trong CI/CD
- ✅ GitHub Actions setup
- ✅ GitLab CI/CD
- ✅ Quality gates
- ✅ Automated reporting

## 1. Why CI/CD for API Tests?

### Benefits

**Manual Testing:**
- ❌ Time-consuming
- ❌ Human error
- ❌ Inconsistent
- ❌ Not scalable

**Automated CI/CD:**
- ✅ Runs on every commit
- ✅ Fast feedback
- ✅ Consistent results
- ✅ Prevents regressions
- ✅ Quality gates

### Use Cases

- Run tests on every PR
- Smoke tests before deployment
- Scheduled regression tests
- Performance monitoring
- Contract testing

> **📸 HÌNH ẢNH:** CI/CD Pipeline Flow
> - File: `cicd-pipeline-flow.png`
> - Nội dung: Flow diagram: Code Push → CI Trigger → Newman Tests → Pass/Fail → Deploy/Block với icons

<!-- IMAGE_PLACEHOLDER: cicd-pipeline-flow.png -->

## 2. GitHub Actions

### Basic Workflow

**.github/workflows/api-tests.yml:**
```yaml
name: API Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install Newman
        run: |
          npm install -g newman
          npm install -g newman-reporter-htmlextra
      
      - name: Run API Tests
        run: |
          newman run collections/api-tests.json \
            -e environments/ci-environment.json \
            -r cli,htmlextra \
            --reporter-htmlextra-export reports/test-report.html
      
      - name: Upload Test Report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: test-report
          path: reports/test-report.html
```

### With Environment Variables

```yaml
- name: Run API Tests
  env:
    API_KEY: ${{ secrets.API_KEY }}
    BASE_URL: ${{ secrets.BASE_URL }}
  run: |
    newman run collections/api-tests.json \
      --env-var "apiKey=$API_KEY" \
      --env-var "baseUrl=$BASE_URL" \
      -r htmlextra \
      --reporter-htmlextra-export reports/test-report.html
```

**Setup Secrets:**
1. GitHub Repo → Settings → Secrets and variables → Actions
2. New repository secret
3. Name: `API_KEY`, Value: `your-api-key`

### Scheduled Tests

```yaml
on:
  schedule:
    - cron: '0 */6 * * *'  # Every 6 hours
  push:
    branches: [ main ]
```

### Multiple Environments

```yaml
jobs:
  test-dev:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Test Dev Environment
        run: newman run collection.json -e dev.json

  test-staging:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Test Staging
        run: newman run collection.json -e staging.json
```

> **📸 HÌNH ẢNH:** GitHub Actions Workflow Run
> - File: `github-actions-run.png`
> - Nội dung: Screenshot GitHub Actions tab showing workflow run với steps (checkout, setup, newman, upload) và status indicators

<!-- IMAGE_PLACEHOLDER: github-actions-run.png -->

## 3. GitLab CI/CD

**.gitlab-ci.yml:**
```yaml
stages:
  - test
  - deploy

api-tests:
  stage: test
  image: node:18
  before_script:
    - npm install -g newman newman-reporter-htmlextra
  script:
    - newman run collections/api-tests.json 
        -e environments/$CI_ENVIRONMENT_NAME.json
        -r htmlextra
        --reporter-htmlextra-export reports/test-report.html
  artifacts:
    when: always
    paths:
      - reports/
    expire_in: 1 week
  only:
    - merge_requests
    - main
```

### Environment-specific Tests

```yaml
test-dev:
  stage: test
  script:
    - newman run collection.json -e dev.json
  only:
    - develop

test-staging:
  stage: test
  script:
    - newman run collection.json -e staging.json
  only:
    - main

test-prod:
  stage: test
  script:
    - newman run smoke-tests.json -e prod.json
  only:
    - tags
```

### With Variables

```yaml
api-tests:
  stage: test
  variables:
    API_KEY: $CI_API_KEY
    BASE_URL: $CI_BASE_URL
  script:
    - newman run collection.json 
        --env-var "apiKey=$API_KEY"
        --env-var "baseUrl=$BASE_URL"
```

**Setup Variables:**
GitLab → Settings → CI/CD → Variables → Add Variable

## 4. Jenkins Pipeline

**Jenkinsfile:**
```groovy
pipeline {
    agent any
    
    environment {
        API_KEY = credentials('api-key')
        BASE_URL = credentials('base-url')
    }
    
    stages {
        stage('Setup') {
            steps {
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }
        
        stage('API Tests') {
            steps {
                sh '''
                    newman run collections/api-tests.json \
                        -e environments/ci.json \
                        --env-var "apiKey=$API_KEY" \
                        --env-var "baseUrl=$BASE_URL" \
                        -r htmlextra \
                        --reporter-htmlextra-export reports/test-report.html
                '''
            }
        }
    }
    
    post {
        always {
            publishHTML([
                reportDir: 'reports',
                reportFiles: 'test-report.html',
                reportName: 'API Test Report'
            ])
        }
        failure {
            mail to: 'team@company.com',
                 subject: "API Tests Failed: ${env.JOB_NAME}",
                 body: "Check ${env.BUILD_URL}"
        }
    }
}
```

## 5. Quality Gates

### Fail Build on Test Failure

Newman exits with code 1 if tests fail, which fails the CI build.

**Example:**
```yaml
- name: Run Tests
  run: newman run collection.json
  # Build fails if newman exits with error
```

### Custom Thresholds

**newman-options.json:**
```json
{
  "bail": true,
  "reporters": ["cli", "json"],
  "reporter": {
    "json": {
      "export": "results.json"
    }
  }
}
```

**Parse Results:**
```javascript
const results = require('./results.json');
const failedTests = results.run.stats.tests.failed;
const avgResponseTime = results.run.timings.responseAverage;

if (failedTests > 0) {
    console.error(`❌ ${failedTests} tests failed`);
    process.exit(1);
}

if (avgResponseTime > 2000) {
    console.error(`❌ Avg response time too high: ${avgResponseTime}ms`);
    process.exit(1);
}

console.log('✅ All quality gates passed');
```

### Code Coverage Gate

```yaml
- name: Check Test Coverage
  run: |
    TOTAL_ENDPOINTS=50
    TESTED_ENDPOINTS=$(grep -c "pm.test" collections/*.json)
    COVERAGE=$((TESTED_ENDPOINTS * 100 / TOTAL_ENDPOINTS))
    
    echo "Test coverage: $COVERAGE%"
    
    if [ $COVERAGE -lt 80 ]; then
      echo "❌ Coverage below 80%"
      exit 1
    fi
```

## 6. Pre-deployment Smoke Tests

### Deployment Pipeline

```yaml
name: Deploy with Smoke Tests

on:
  push:
    branches: [ main ]

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Staging
        run: ./deploy-staging.sh
      
      - name: Wait for Deployment
        run: sleep 30
      
      - name: Smoke Tests
        run: |
          newman run smoke-tests.json \
            -e staging.json \
            --bail
      
      - name: Deploy to Production
        if: success()
        run: ./deploy-production.sh
```

### Smoke Test Collection

**Critical endpoints only:**
```javascript
// Smoke Tests Collection
- GET /health (API is up)
- POST /auth/login (Auth works)
- GET /users/me (Token validation works)
- GET /products (Core feature works)
```

## 7. Monitoring and Alerts

### Postman Monitors

**Setup:**
1. Postman → Monitors
2. Create Monitor
3. Select collection and environment
4. Schedule: Every 5 minutes
5. Notifications: Email/Slack on failure

### Slack Notifications

**GitHub Actions:**
```yaml
- name: Notify Slack on Failure
  if: failure()
  uses: slackapi/slack-github-action@v1
  with:
    payload: |
      {
        "text": "❌ API Tests Failed",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "API tests failed in <${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}|workflow run>"
            }
          }
        ]
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

### Email Notifications

```yaml
- name: Send Email on Failure
  if: failure()
  uses: dawidd6/action-send-mail@v3
  with:
    server_address: smtp.gmail.com
    server_port: 465
    username: ${{ secrets.EMAIL_USERNAME }}
    password: ${{ secrets.EMAIL_PASSWORD }}
    subject: API Tests Failed - ${{ github.sha }}
    to: team@company.com
    from: ci@company.com
    body: |
      API tests failed in commit ${{ github.sha }}
      
      View details: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
```

## 8. Advanced Workflows

### Matrix Testing

**Test multiple environments:**
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        environment: [dev, staging, production]
    steps:
      - uses: actions/checkout@v3
      - name: Test ${{ matrix.environment }}
        run: newman run collection.json -e ${{ matrix.environment }}.json
```

### Parallel Execution

```yaml
jobs:
  test-auth:
    runs-on: ubuntu-latest
    steps:
      - run: newman run auth-tests.json
  
  test-users:
    runs-on: ubuntu-latest
    steps:
      - run: newman run user-tests.json
  
  test-products:
    runs-on: ubuntu-latest
    steps:
      - run: newman run product-tests.json
```

### Conditional Execution

```yaml
- name: Run Full Test Suite
  if: github.event_name == 'push' && github.ref == 'refs/heads/main'
  run: newman run full-tests.json

- name: Run Smoke Tests Only
  if: github.event_name == 'pull_request'
  run: newman run smoke-tests.json
```

## 9. Reporting

### HTML Reports as Artifacts

```yaml
- name: Upload HTML Report
  uses: actions/upload-artifact@v3
  if: always()
  with:
    name: api-test-report-${{ github.run_number }}
    path: reports/*.html
    retention-days: 30
```

### Publish to GitHub Pages

```yaml
- name: Deploy Report to GitHub Pages
  if: always()
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./reports
    destination_dir: reports/${{ github.run_number }}
```

Access at: `https://username.github.io/repo/reports/123/test-report.html`

### Test Summary in PR

```yaml
- name: Comment PR with Results
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v6
  with:
    script: |
      const fs = require('fs');
      const results = JSON.parse(fs.readFileSync('results.json'));
      const summary = `
      ## API Test Results
      - ✅ Passed: ${results.run.stats.tests.passed}
      - ❌ Failed: ${results.run.stats.tests.failed}
      - ⏱️ Avg Response: ${results.run.timings.responseAverage}ms
      `;
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: summary
      });
```

> **📸 HÌNH ẢNH:** CI/CD Test Report
> - File: `cicd-test-report.png`
> - Nội dung: Screenshot of HTML test report deployed as artifact, showing test summary, pass/fail rates, response times với graphs

<!-- IMAGE_PLACEHOLDER: cicd-test-report.png -->

## 10. Best Practices

### ✅ DO

- Run tests on every PR
- Use environment-specific configs
- Store secrets securely
- Generate HTML reports
- Send notifications on failure
- Run smoke tests before deploy
- Use quality gates
- Cache dependencies
- Run tests in parallel when possible
- Monitor test execution time

### ❌ DON'T

- Hardcode credentials in workflows
- Skip tests for "urgent" deployments
- Ignore flaky tests
- Run full suite on every commit (use smoke tests)
- Deploy if tests fail
- Commit sensitive data
- Use production for CI tests
- Ignore slow tests

## 11. Complete Example

**.github/workflows/complete-ci.yml:**
```yaml
name: Complete CI/CD Pipeline

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]

env:
  NODE_VERSION: '18'

jobs:
  smoke-tests:
    name: Smoke Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install Newman
        run: npm install -g newman newman-reporter-htmlextra
      
      - name: Run Smoke Tests
        env:
          API_KEY: ${{ secrets.API_KEY }}
        run: |
          newman run collections/smoke-tests.json \
            -e environments/ci.json \
            --env-var "apiKey=$API_KEY" \
            --bail \
            -r cli

  full-tests:
    name: Full Test Suite
    needs: smoke-tests
    if: github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install Newman
        run: npm install -g newman newman-reporter-htmlextra
      
      - name: Run All Tests
        env:
          API_KEY: ${{ secrets.API_KEY }}
        run: |
          newman run collections/all-tests.json \
            -e environments/ci.json \
            --env-var "apiKey=$API_KEY" \
            -r htmlextra,json \
            --reporter-htmlextra-export reports/test-report.html \
            --reporter-json-export reports/results.json
      
      - name: Upload Report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: test-report
          path: reports/
      
      - name: Check Quality Gates
        run: node scripts/check-quality-gates.js reports/results.json
      
      - name: Notify on Failure
        if: failure()
        uses: slackapi/slack-github-action@v1
        with:
          payload: '{"text": "❌ API Tests Failed"}'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

## Tổng Kết

- ✅ Newman trong CI/CD pipelines
- ✅ GitHub Actions, GitLab CI, Jenkins
- ✅ Quality gates và thresholds
- ✅ Automated reporting
- ✅ Notifications and monitoring
- ✅ Pre-deployment smoke tests
- ✅ Secure credential management

---

[⬅️ Collaboration](./collaboration.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Monitoring ➡️](./monitoring.md)
