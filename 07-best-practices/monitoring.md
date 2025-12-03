# 7.10 Monitoring và Alerting

Monitoring liên tục đảm bảo API luôn hoạt động tốt, phát hiện issues sớm, và maintain uptime.

## Mục Tiêu

- ✅ Postman Monitors
- ✅ Scheduled runs
- ✅ Alerting strategies
- ✅ Health checks
- ✅ Uptime monitoring

## 1. Postman Monitors

### What are Monitors?

**Monitors** = Automated scheduled collection runs

**Benefits:**
- ✅ Continuous testing
- ✅ Uptime monitoring
- ✅ Performance tracking
- ✅ Alerts on failures
- ✅ No infrastructure needed

> **📸 HÌNH ẢNH:** Postman Monitor Dashboard
> - File: `monitor-dashboard.png`
> - Nội dung: Screenshot Postman Monitors tab showing multiple monitors với status (passing/failing), run history, và performance graphs

<!-- IMAGE_PLACEHOLDER: monitor-dashboard.png -->

## 2. Creating a Monitor

### Setup Steps

1. **Select Collection**
   - Collection → ... → Monitor Collection

2. **Configure Monitor**
   - Name: "Production API Health Check"
   - Environment: Production
   - Schedule: Every 5 minutes
   - Region: Select closest to API

3. **Notifications**
   - Email on failure
   - Slack webhook
   - After 2 consecutive failures

> **📸 HÌNH ẢNH:** Monitor Creation Form
> - File: `monitor-creation-form.png`
> - Nội dung: Screenshot of monitor setup form với fields: name, schedule dropdown, region selector, notification settings

<!-- IMAGE_PLACEHOLDER: monitor-creation-form.png -->

## 3. Monitoring Strategies

### 1. Health Check Monitor

**Collection: Health Checks**
```javascript
// Request: GET /health
pm.test("API is up", () => {
    pm.response.to.have.status(200);
});

pm.test("Response time acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

pm.test("Health status is OK", () => {
    const response = pm.response.json();
    pm.expect(response.status).to.equal("healthy");
});
```

**Schedule:** Every 5 minutes

### 2. Critical Path Monitor

**Test critical user workflows:**
```javascript
// 1. User Login
// 2. Get User Profile
// 3. Update Profile
// 4. Logout
```

**Schedule:** Every 15 minutes

### 3. Performance Monitor

**Track response times:**
```javascript
pm.test("Login performance", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Log for trends
console.log(`Response time: ${pm.response.responseTime}ms`);
```

**Schedule:** Every hour

### 4. Integration Monitor

**Test third-party integrations:**
```javascript
// Payment gateway
// Email service
// SMS provider
// Cloud storage
```

**Schedule:** Every 30 minutes

## 4. Schedule Frequency

### Recommendations

| Type | Frequency | Reason |
|------|-----------|--------|
| Health Check | 5 minutes | Fast detection |
| Critical Paths | 15 minutes | Balance cost/coverage |
| Full Regression | 1 hour | Comprehensive |
| Performance | 30 minutes | Trend tracking |
| Non-critical | 6 hours | Reduce noise |

### Consider Costs

Postman free tier: Limited monitor runs
- Optimize frequency
- Focus on critical endpoints
- Use regional targeting

## 5. Alerting

### Email Notifications

**Configure:**
- Monitor → Settings → Notifications
- Email: team@company.com
- Trigger: After 2 consecutive failures
- Include: Test results and error details

### Slack Integration

**Setup:**
1. Postman → Integrations → Slack
2. Connect workspace
3. Configure notifications

**Example Notification:**
```
🔴 Monitor Failed: Production API Health
Collection: Health Checks
Failed at: 2024-01-15 10:30 UTC
Failures:
  - GET /health: Status was 503 (expected 200)
  - Response time: 15234ms (expected < 1000ms)
View: https://go.postman.co/...
```

### Webhook Notifications

**Custom webhook:**
```javascript
// Collection Tests
if (pm.response.code !== 200) {
    const webhook = pm.environment.get("alertWebhook");
    
    pm.sendRequest({
        url: webhook,
        method: 'POST',
        header: {
            'Content-Type': 'application/json'
        },
        body: {
            mode: 'raw',
            raw: JSON.stringify({
                alert: 'API Down',
                endpoint: pm.request.url.toString(),
                status: pm.response.code,
                time: new Date().toISOString()
            })
        }
    });
}
```

### PagerDuty Integration

**For critical alerts:**
```javascript
if (pm.response.code === 503 || pm.response.responseTime > 10000) {
    // Trigger PagerDuty incident
    pm.sendRequest({
        url: 'https://events.pagerduty.com/v2/enqueue',
        method: 'POST',
        header: {
            'Content-Type': 'application/json'
        },
        body: {
            mode: 'raw',
            raw: JSON.stringify({
                routing_key: pm.environment.get('pagerdutyKey'),
                event_action: 'trigger',
                payload: {
                    summary: 'API Critical Failure',
                    severity: 'critical',
                    source: pm.request.url.toString()
                }
            })
        }
    });
}
```

## 6. Monitor Analytics

### View Results

**Postman Monitor Dashboard:**
- Pass rate over time
- Response time trends
- Failure patterns
- Regional differences

### Export Data

**Via API:**
```bash
curl -X GET https://api.getpostman.com/monitors/{monitorId}/runs \
  -H "X-Api-Key: $POSTMAN_API_KEY"
```

### Analyze Trends

```javascript
// Download results
// Parse JSON
// Calculate:
- Average uptime
- P95/P99 response times
- Failure frequency
- Recovery time
```

## 7. Multi-region Monitoring

### Why Multi-region?

- ✅ Detect regional outages
- ✅ Monitor CDN performance
- ✅ Verify global availability
- ✅ Test geo-routing

### Setup

**Create monitors in different regions:**
- Monitor 1: US East
- Monitor 2: EU West
- Monitor 3: Asia Pacific

**Compare results:**
```javascript
// Collection Pre-request
pm.environment.set("region", "us-east");

// Tests
pm.test(`API accessible from ${pm.environment.get("region")}`, () => {
    pm.response.to.have.status(200);
});
```

## 8. Custom Health Checks

### Comprehensive Health Endpoint

**Backend implementation:**
```javascript
// GET /health
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "services": {
    "database": "healthy",
    "cache": "healthy",
    "queue": "healthy"
  },
  "metrics": {
    "uptime": 864000,
    "activeConnections": 42
  }
}
```

### Monitor Tests

```javascript
pm.test("All services healthy", () => {
    const response = pm.response.json();
    pm.expect(response.status).to.equal("healthy");
    
    // Check each service
    const services = response.services;
    pm.expect(services.database).to.equal("healthy");
    pm.expect(services.cache).to.equal("healthy");
    pm.expect(services.queue).to.equal("healthy");
});

pm.test("Uptime acceptable", () => {
    const response = pm.response.json();
    const uptime = response.metrics.uptime;
    pm.expect(uptime).to.be.above(3600);  // > 1 hour
});
```

## 9. Synthetic Monitoring

### User Journey Monitoring

**Test complete workflows:**

**Collection: E-commerce User Journey**
```
1. Browse Products (GET /products)
2. Add to Cart (POST /cart)
3. View Cart (GET /cart)
4. Checkout (POST /orders)
5. Confirm Order (GET /orders/:id)
```

**Monitor every 30 minutes**

### Benefits

- ✅ Real user experience simulation
- ✅ End-to-end validation
- ✅ Detect integration issues
- ✅ Performance of workflows

## 10. Incident Response

### Runbook

**When monitor fails:**

1. **Check Dashboard**
   - Is it still failing?
   - Which region?
   - Single endpoint or multiple?

2. **Verify Issue**
   - Run manual test
   - Check server logs
   - Verify dependencies

3. **Escalate if Needed**
   - Page on-call engineer
   - Post in #incidents
   - Update status page

4. **Document**
   - What failed?
   - Root cause?
   - Resolution steps?
   - Prevention?

### Alert Fatigue Prevention

**❌ BAD:**
- Alert on every failure
- Too many monitors
- Overly sensitive thresholds
- No context in alerts

**✅ GOOD:**
- Alert after N consecutive failures
- Focus on critical paths
- Reasonable thresholds
- Rich alert context
- Gradual escalation

## 11. Monitor Best Practices

### ✅ DO

- Monitor critical endpoints
- Use appropriate frequencies
- Set reasonable thresholds
- Test from multiple regions
- Alert relevant people
- Include context in alerts
- Track trends over time
- Document runbooks
- Review and adjust monitors
- Use health check endpoints

### ❌ DON'T

- Monitor everything
- Run too frequently (cost)
- Set overly strict thresholds
- Ignore repeated failures
- Alert entire team for minor issues
- Skip documentation
- Forget to test monitors
- Neglect monitor maintenance
- Monitor production only

## 12. Complete Monitor Example

**Collection: Production Health Monitor**

```javascript
// Request 1: API Health
pm.test("API is up", () => {
    pm.response.to.have.status(200);
});

pm.test("Response time good", () => {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

// Request 2: Database Connection
pm.test("Database accessible", () => {
    const response = pm.response.json();
    pm.expect(response.database).to.equal("connected");
});

// Request 3: Authentication
pm.test("Auth service working", () => {
    pm.response.to.have.status(200);
    const token = pm.response.json().token;
    pm.expect(token).to.be.a('string');
    pm.environment.set("healthCheckToken", token);
});

// Request 4: Critical Endpoint
pm.test("Users endpoint accessible", () => {
    pm.response.to.have.status(200);
});

// Request 5: Performance Check
pm.test("Average response time acceptable", () => {
    const times = pm.globals.get("responseTimes") || [];
    const avg = times.reduce((a, b) => a + b, 0) / times.length;
    pm.expect(avg).to.be.below(500);
});

// Alert on issues
if (pm.response.code !== 200) {
    console.error(`🔴 ALERT: ${pm.info.requestName} failed!`);
    // Trigger webhook/PagerDuty here
}
```

**Monitor Configuration:**
- Name: Production Health Monitor
- Schedule: Every 5 minutes
- Regions: US East, EU West, Asia Pacific
- Alerts: 
  - Email after 2 failures
  - Slack immediately
  - PagerDuty on critical

> **📸 HÌNH ẢNH:** Monitor Run Results
> - File: `monitor-run-results.png`
> - Nội dung: Screenshot showing monitor execution results với timeline graph, pass/fail status for each request, response times

<!-- IMAGE_PLACEHOLDER: monitor-run-results.png -->

## 13. Cost Optimization

### Free Tier Limits

Postman free plan:
- Limited monitor runs/month
- Optimize usage

### Strategies

**1. Prioritize Critical Monitors**
```
✅ Health checks: 5 min
✅ Critical paths: 15 min
⚠️  Full regression: 1 hour
❌ Non-critical: Manual or CI/CD
```

**2. Use Regional Targeting**
- Choose closest region
- Avoid unnecessary multi-region

**3. Smart Scheduling**
```javascript
// Pre-request: Skip non-business hours
const hour = new Date().getHours();
if (hour < 6 || hour > 22) {
    console.log("Outside business hours, skipping");
    postman.setNextRequest(null);  // Skip
}
```

**4. Combine Tests**
- Single monitor with multiple requests
- Better than separate monitors

## Tổng Kết

- ✅ Postman Monitors for continuous testing
- ✅ Health checks every 5-15 minutes
- ✅ Multi-region monitoring
- ✅ Alerting via Email/Slack/PagerDuty
- ✅ Synthetic user journey monitoring
- ✅ Analytics and trend tracking
- ✅ Incident response runbooks
- ✅ Cost optimization strategies

---

[⬅️ CI/CD Integration](./cicd-integration.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Chương 8 ➡️](../08-du-an-thuc-te/README.md)
