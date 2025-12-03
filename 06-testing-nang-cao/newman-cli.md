# 6.6 Newman CLI

Newman là command-line tool để chạy Postman collections, cho phép automation, CI/CD integration, và scheduled testing.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Cài đặt Newman
- ✅ Chạy collections từ command line
- ✅ Sử dụng environments và data files
- ✅ Generate reports (HTML, JSON, CLI)
- ✅ Integrate với CI/CD pipelines

## 1. Newman Là Gì?

**Newman** = Postman for Command Line

### Tại Sao Cần Newman?

**Postman GUI:**
- ✅ Great cho manual testing
- ❌ Không automation-friendly
- ❌ Không chạy trong CI/CD

**Newman CLI:**
- ✅ Chạy từ terminal
- ✅ Automation scripts
- ✅ CI/CD integration
- ✅ Scheduled tests (cron jobs)
- ✅ Headless execution

## 2. Installation

### Yêu Cầu

- Node.js (v12 hoặc cao hơn)

Check version:
```bash
node --version
# v18.0.0 hoặc tương tự
```

### Install Newman

```bash
npm install -g newman
```

> **📸 HÌNH ẢNH:** Newman Installation
> - File: `newman-installation-terminal.png`
> - Nội dung: Terminal screenshot showing npm install -g newman command và output success message

<!-- IMAGE_PLACEHOLDER: newman-installation-terminal.png -->

### Verify Installation

```bash
newman --version
# 5.3.2 hoặc tương tự
```

### Install Reporters (Optional)

```bash
# HTML reporter
npm install -g newman-reporter-html

# HTMLExtra reporter (better)
npm install -g newman-reporter-htmlextra
```

## 3. Export Collection và Environment

### Export Collection

**Từ Postman:**
1. Right-click collection
2. **Export**
3. Chọn **Collection v2.1**
4. Save file: `my-collection.json`

### Export Environment

1. Click Environments
2. Click "..." trên environment
3. **Export**
4. Save file: `my-environment.json`

## 4. Basic Commands

### Run Collection

```bash
newman run my-collection.json
```

> **📸 HÌNH ẢNH:** Newman Run Output
> - File: `newman-run-output.png`
> - Nội dung: Terminal showing newman run results với summary: iterations, requests, tests passed/failed, response times

<!-- IMAGE_PLACEHOLDER: newman-run-output.png -->

**Output:**
```
Newman

API Tests

→ GET Users
  GET https://jsonplaceholder.typicode.com/users [200 OK, 5.2KB, 234ms]
  ✓ Status code is 200
  ✓ Response has users

→ GET User by ID
  GET https://jsonplaceholder.typicode.com/users/1 [200 OK, 890B, 156ms]
  ✓ Status code is 200
  ✓ User has name

┌─────────────────────────┬──────────────────┬──────────────────┐
│                         │         executed │           failed │
├─────────────────────────┼──────────────────┼──────────────────┤
│              iterations │                1 │                0 │
├─────────────────────────┼──────────────────┼──────────────────┤
│                requests │                2 │                0 │
├─────────────────────────┼──────────────────┼──────────────────┤
│            test-scripts │                4 │                0 │
├─────────────────────────┼──────────────────┼──────────────────┤
│      prerequest-scripts │                2 │                0 │
├─────────────────────────┼──────────────────┼──────────────────┤
│              assertions │                4 │                0 │
├─────────────────────────┴──────────────────┴──────────────────┤
│ total run duration: 1.2s                                      │
├───────────────────────────────────────────────────────────────┤
│ total data received: 6.08KB (approx)                          │
├───────────────────────────────────────────────────────────────┤
│ average response time: 195ms [min: 156ms, max: 234ms]        │
└───────────────────────────────────────────────────────────────┘
```

### Run với Environment

```bash
newman run my-collection.json -e my-environment.json
```

### Run với Data File

```bash
newman run my-collection.json -d test-data.csv
```

### Multiple Iterations

```bash
newman run my-collection.json -n 10
# Chạy collection 10 lần
```

## 5. Command Options

### Essential Options

```bash
newman run <collection> [options]
```

| Option | Short | Mô tả | Ví dụ |
|--------|-------|-------|-------|
| `--environment` | `-e` | Environment file | `-e env.json` |
| `--globals` | `-g` | Global variables | `-g globals.json` |
| `--data` | `-d` | Data file (CSV/JSON) | `-d users.csv` |
| `--iteration-count` | `-n` | Số iterations | `-n 5` |
| `--iteration-data` | | Specific iteration | `--iteration-data "1-3"` |
| `--folder` | | Run specific folder | `--folder "Login Tests"` |
| `--delay-request` | | Delay giữa requests (ms) | `--delay-request 500` |
| `--timeout-request` | | Request timeout (ms) | `--timeout-request 30000` |
| `--reporters` | `-r` | Report formats | `-r cli,html` |
| `--reporter-html-export` | | HTML report path | `--reporter-html-export report.html` |
| `--bail` | | Stop on first failure | `--bail` |
| `--color` | | Enable colors | `--color on` |
| `--suppress-exit-code` | | Always exit 0 | `--suppress-exit-code` |

### Example Commands

**Run với tất cả options:**
```bash
newman run api-tests.json \
  -e staging.json \
  -d test-users.csv \
  -n 3 \
  --delay-request 200 \
  --timeout-request 10000 \
  -r cli,html \
  --reporter-html-export report.html \
  --color on
```

**Run specific folder:**
```bash
newman run collection.json --folder "Smoke Tests"
```

**Bail on failure:**
```bash
newman run collection.json --bail
# Dừng ngay khi có test failed
```

## 6. Reporters

### CLI Reporter (Default)

```bash
newman run collection.json -r cli
```

Output trực tiếp trong terminal.

### JSON Reporter

```bash
newman run collection.json -r json --reporter-json-export output.json
```

Generates JSON file với detailed results.

### HTML Reporter

```bash
newman run collection.json \
  -r html \
  --reporter-html-export report.html
```

> **📸 HÌNH ẢNH:** HTML Report
> - File: `newman-html-report.png`
> - Nội dung: Screenshot of HTML report showing test summary, requests, response times, passed/failed tests với charts

<!-- IMAGE_PLACEHOLDER: newman-html-report.png -->

**HTML report bao gồm:**
- Test summary
- Request/response details
- Response times graph
- Failed tests highlights
- Environment variables

### HTMLExtra Reporter (Recommended)

```bash
newman run collection.json \
  -r htmlextra \
  --reporter-htmlextra-export report.html
```

**Features:**
- Better UI/UX
- Charts và graphs
- Dark mode
- Detailed test results
- Response previews

### Multiple Reporters

```bash
newman run collection.json \
  -r cli,json,htmlextra \
  --reporter-json-export results.json \
  --reporter-htmlextra-export report.html
```

Generates CLI output + JSON + HTML!

## 7. CI/CD Integration

### GitHub Actions

**.github/workflows/api-tests.yml:**
```yaml
name: API Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 */6 * * *'  # Every 6 hours

jobs:
  api-tests:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

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
        newman run postman/collection.json \
          -e postman/environment.json \
          -r cli,htmlextra \
          --reporter-htmlextra-export reports/api-test-report.html

    - name: Upload Report
      uses: actions/upload-artifact@v3
      if: always()
      with:
        name: api-test-report
        path: reports/api-test-report.html
```

> **📸 HÌNH ẢNH:** GitHub Actions Workflow
> - File: `github-actions-newman.png`
> - Nội dung: Screenshot GitHub Actions workflow running, showing newman command execution và test results

<!-- IMAGE_PLACEHOLDER: github-actions-newman.png -->

### GitLab CI

**.gitlab-ci.yml:**
```yaml
api-tests:
  image: node:18
  stage: test
  script:
    - npm install -g newman newman-reporter-htmlextra
    - newman run collection.json -e environment.json -r htmlextra --reporter-htmlextra-export report.html
  artifacts:
    when: always
    paths:
      - report.html
    expire_in: 1 week
```

### Jenkins

**Jenkinsfile:**
```groovy
pipeline {
    agent any

    stages {
        stage('Install Newman') {
            steps {
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }

        stage('Run API Tests') {
            steps {
                sh '''
                    newman run collection.json \
                      -e environment.json \
                      -r htmlextra \
                      --reporter-htmlextra-export report.html
                '''
            }
        }
    }

    post {
        always {
            publishHTML([
                reportDir: '.',
                reportFiles: 'report.html',
                reportName: 'API Test Report'
            ])
        }
    }
}
```

## 8. Environment Variables trong Newman

### Set qua Command Line

```bash
newman run collection.json \
  --env-var "apiKey=abc123" \
  --env-var "baseUrl=https://api.example.com"
```

### Set từ OS Environment

```bash
export API_KEY=abc123
export BASE_URL=https://api.example.com

newman run collection.json
```

Access trong scripts:
```javascript
const apiKey = pm.environment.get("apiKey") || process.env.API_KEY;
```

## 9. Exit Codes

Newman returns exit codes:

| Code | Meaning |
|------|---------|
| `0` | All tests passed |
| `1` | Test failures or errors |

**Use trong scripts:**
```bash
newman run collection.json
if [ $? -eq 0 ]; then
    echo "✅ All tests passed!"
else
    echo "❌ Tests failed!"
    exit 1
fi
```

**CI/CD:** Exit code 1 = build failure

### Suppress Exit Code

```bash
newman run collection.json --suppress-exit-code
# Always exit 0, even if tests fail
```

Useful khi bạn không muốn fail build.

## 10. Practical Examples

### Example 1: Scheduled Daily Tests

**Cron job:**
```bash
# /etc/crontab
0 2 * * * cd /path/to/tests && newman run api-tests.json -e prod.json -r htmlextra --reporter-htmlextra-export daily-report.html
```

Chạy mỗi ngày lúc 2AM.

### Example 2: Smoke Tests Before Deployment

**deploy.sh:**
```bash
#!/bin/bash

echo "Running smoke tests..."

newman run smoke-tests.json -e staging.json --bail

if [ $? -ne 0 ]; then
    echo "❌ Smoke tests failed! Aborting deployment."
    exit 1
fi

echo "✅ Smoke tests passed! Deploying..."
./deploy-to-production.sh
```

### Example 3: Load Testing

```bash
# 100 iterations với 1 second delay
newman run load-test.json -n 100 --delay-request 1000
```

### Example 4: Data-Driven Testing

**users.csv:**
```csv
email,password
user1@test.com,pass1
user2@test.com,pass2
user3@test.com,pass3
```

```bash
newman run login-test.json -d users.csv
# Chạy 3 lần, mỗi user một lần
```

## 11. Best Practices

### ✅ DO

- Version control collections và environments
- Use environment variables cho sensitive data
- Generate HTML reports cho visibility
- Set reasonable timeouts
- Use --bail trong CI/CD để fail fast
- Clean up test data sau run
- Monitor exit codes

### ❌ DON'T

- Hardcode credentials trong collections
- Run load tests against production
- Ignore failed tests
- Skip environment file khi cần
- Use very short timeouts (flaky tests)
- Commit sensitive environment files

## 12. Troubleshooting

### Issue: "newman: command not found"

```bash
# Check global npm path
npm config get prefix

# Add to PATH
export PATH=$PATH:/usr/local/bin
```

### Issue: Tests Fail Locally but Pass in Postman

- Check environment variables are set
- Verify data file path is correct
- Check for timing issues (add delays)

### Issue: Slow Execution

- Reduce iterations
- Increase timeout
- Run specific folder instead of entire collection

## 13. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Newman chạy Postman collections từ CLI
- ✅ Install và basic commands
- ✅ Run với environments, data files, iterations
- ✅ Generate reports (CLI, JSON, HTML)
- ✅ CI/CD integration (GitHub Actions, GitLab, Jenkins)
- ✅ Exit codes và error handling
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.7 JSON Schema Validation](./json-schema-validation.md)

---

[⬅️ Mock Servers](./mock-servers.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: JSON Schema ➡️](./json-schema-validation.md)
