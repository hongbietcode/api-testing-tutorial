# 7.7 Performance Testing

Performance testing đảm bảo API đáp ứng trong thời gian chấp nhận được và xử lý tải hiệu quả.

## Mục Tiêu

- ✅ Response time validation
- ✅ Load testing basics
- ✅ Performance benchmarks
- ✅ Bottleneck identification

## 1. Response Time Testing

### Basic Response Time Check

```javascript
pm.test("Response time is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(2000);  // < 2 seconds
});
```

### Tiered Response Times

```javascript
// Fast endpoints (< 500ms)
pm.test("GET list is fast", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Medium endpoints (< 2s)
pm.test("POST create is medium", () => {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

// Slow endpoints (< 5s)
pm.test("Complex query is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(5000);
});
```

### Track Response Times

```javascript
// Pre-request Script
pm.globals.set("requestStartTime", Date.now());

// Tests
const responseTime = pm.response.responseTime;
console.log(`Response time: ${responseTime}ms`);

// Store for analysis
const times = JSON.parse(pm.globals.get("responseTimes") || "[]");
times.push({
    request: pm.info.requestName,
    time: responseTime,
    timestamp: new Date().toISOString()
});
pm.globals.set("responseTimes", JSON.stringify(times));
```

> **📸 HÌNH ẢNH:** Response Time Chart
> - File: `response-time-chart.png`
> - Nội dung: Line graph showing response times across multiple requests, với threshold lines at 500ms, 2s, 5s

<!-- IMAGE_PLACEHOLDER: response-time-chart.png -->

## 2. Performance Benchmarks

### Define SLA (Service Level Agreement)

```javascript
// Collection Pre-request Script
const SLA = {
    fast: 500,      // Read operations
    medium: 2000,   // Write operations
    slow: 5000      // Complex queries
};
pm.globals.set("SLA", JSON.stringify(SLA));
```

### Test Against SLA

```javascript
const SLA = JSON.parse(pm.globals.get("SLA"));

pm.test(`Response time meets SLA (< ${SLA.fast}ms)`, () => {
    pm.expect(pm.response.responseTime).to.be.below(SLA.fast);
});
```

### Categorized Tests

```javascript
// GET requests - Fast SLA
if (pm.request.method === "GET") {
    pm.test("GET response is fast", () => {
        pm.expect(pm.response.responseTime).to.be.below(500);
    });
}

// POST/PUT requests - Medium SLA
if (pm.request.method === "POST" || pm.request.method === "PUT") {
    pm.test("Write operation is medium speed", () => {
        pm.expect(pm.response.responseTime).to.be.below(2000);
    });
}

// Complex queries - Slow SLA
if (pm.request.url.toString().includes("/search") || 
    pm.request.url.toString().includes("/report")) {
    pm.test("Complex query is acceptable", () => {
        pm.expect(pm.response.responseTime).to.be.below(5000);
    });
}
```

## 3. Load Testing với Collection Runner

### Setup Data File

**users.csv:**
```csv
userId,action
1,view
2,edit
3,delete
4,view
5,edit
```

### Run Multiple Iterations

**Collection Runner:**
1. Select collection
2. Choose data file: `users.csv`
3. Iterations: 100
4. Delay: 100ms
5. Run

### Monitor Performance

```javascript
// Tests tab
const iterationCount = pm.info.iteration;
const responseTime = pm.response.responseTime;

console.log(`Iteration ${iterationCount}: ${responseTime}ms`);

// Alert if degrading
if (responseTime > 3000) {
    console.warn(`⚠️ Slow response in iteration ${iterationCount}`);
}
```

> **📸 HÌNH ẢNH:** Collection Runner Load Test
> - File: `collection-runner-load-test.png`
> - Nội dung: Screenshot Collection Runner với 100 iterations, showing progress bar, average response time, và pass/fail summary

<!-- IMAGE_PLACEHOLDER: collection-runner-load-test.png -->

## 4. Newman Load Testing

### Basic Load Test

```bash
# Run 100 iterations
newman run api-tests.json -n 100 --delay-request 100
```

### Parallel Execution

```bash
# Run 3 instances in parallel
newman run api-tests.json -n 50 &
newman run api-tests.json -n 50 &
newman run api-tests.json -n 50 &
wait
```

### Load Test Script

**load-test.sh:**
```bash
#!/bin/bash

echo "Starting load test..."

# Warm up
echo "Warm-up phase..."
newman run api-tests.json -n 10 --delay-request 500 > /dev/null

# Main load test
echo "Load test phase (100 requests)..."
newman run api-tests.json \
  -n 100 \
  --delay-request 100 \
  -r cli,json \
  --reporter-json-export load-test-results.json

echo "Load test complete!"

# Analyze results
node analyze-performance.js load-test-results.json
```

### Analyze Results

**analyze-performance.js:**
```javascript
const fs = require('fs');
const results = JSON.parse(fs.readFileSync(process.argv[2]));

const times = results.run.executions.map(e => e.response.responseTime);
const avg = times.reduce((a, b) => a + b, 0) / times.length;
const max = Math.max(...times);
const min = Math.min(...times);

console.log(`
Performance Summary:
- Total Requests: ${times.length}
- Average: ${avg.toFixed(2)}ms
- Min: ${min}ms
- Max: ${max}ms
- P95: ${percentile(times, 95)}ms
- P99: ${percentile(times, 99)}ms
`);

function percentile(arr, p) {
    const sorted = arr.sort((a, b) => a - b);
    const index = Math.ceil(sorted.length * (p / 100)) - 1;
    return sorted[index];
}
```

## 5. Concurrent Users Simulation

### Pre-request Script

```javascript
// Simulate concurrent users
const users = ["user1", "user2", "user3", "user4", "user5"];
const randomUser = users[Math.floor(Math.random() * users.length)];
pm.environment.set("currentUser", randomUser);
```

### Monitoring

```javascript
// Tests
const concurrentUsers = pm.globals.get("activeUsers") || 0;
pm.globals.set("activeUsers", parseInt(concurrentUsers) + 1);

console.log(`Concurrent users: ${pm.globals.get("activeUsers")}`);

// Cleanup after test
pm.globals.set("activeUsers", parseInt(pm.globals.get("activeUsers")) - 1);
```

## 6. Database Performance

### Monitor Query Times

```javascript
pm.test("Database query is optimized", () => {
    const response = pm.response.json();
    
    // API might return query execution time
    if (response.meta && response.meta.queryTime) {
        pm.expect(response.meta.queryTime).to.be.below(100);  // < 100ms
    }
});
```

### Pagination Performance

```javascript
pm.test("Pagination doesn't slow down", () => {
    const url = pm.request.url.toString();
    
    // Large offset shouldn't be significantly slower
    if (url.includes("offset=1000")) {
        pm.expect(pm.response.responseTime).to.be.below(1000);
    }
});
```

## 7. Caching Tests

### Test Cache Headers

```javascript
pm.test("Response is cacheable", () => {
    pm.response.to.have.header("Cache-Control");
    const cacheControl = pm.response.headers.get("Cache-Control");
    pm.expect(cacheControl).to.include("max-age");
});
```

### Verify Cache Hit

```javascript
// First request - Cache miss
pm.test("First request populates cache", () => {
    pm.response.to.have.status(200);
});

// Second request - Should be faster (cache hit)
pm.test("Cached response is faster", () => {
    const firstRequestTime = pm.environment.get("firstRequestTime");
    const currentTime = pm.response.responseTime;
    
    if (firstRequestTime) {
        pm.expect(currentTime).to.be.below(firstRequestTime);
        console.log(`Cache speedup: ${firstRequestTime - currentTime}ms`);
    } else {
        pm.environment.set("firstRequestTime", currentTime);
    }
});
```

## 8. Stress Testing

### Gradual Load Increase

```javascript
// Pre-request Script
const iteration = pm.info.iteration;
const delayMs = Math.max(1000 - (iteration * 10), 0);

setTimeout(() => {
    // Request will run after delay
}, delayMs);
```

### Identify Breaking Point

```javascript
// Tests
const responseTime = pm.response.responseTime;
const statusCode = pm.response.code;

if (responseTime > 10000 || statusCode === 503) {
    console.error(`⛔ System breaking at iteration ${pm.info.iteration}`);
    pm.environment.set("breakingPoint", pm.info.iteration);
}
```

## 9. Performance Monitoring Dashboard

### Track Metrics

```javascript
// Collection Tests (runs after all requests)
const allTimes = JSON.parse(pm.globals.get("responseTimes") || "[]");

const stats = {
    total: allTimes.length,
    average: avg(allTimes.map(t => t.time)),
    min: Math.min(...allTimes.map(t => t.time)),
    max: Math.max(...allTimes.map(t => t.time)),
    p95: percentile(allTimes.map(t => t.time), 95),
    p99: percentile(allTimes.map(t => t.time), 99)
};

console.log("Performance Summary:", stats);

// Helper functions
function avg(arr) {
    return arr.reduce((a, b) => a + b, 0) / arr.length;
}

function percentile(arr, p) {
    const sorted = arr.sort((a, b) => a - b);
    const index = Math.ceil(sorted.length * (p / 100)) - 1;
    return sorted[index];
}
```

## 10. Performance Regression Tests

### Baseline Performance

```javascript
// Set baseline
const baselineTime = 500;  // ms
pm.environment.set("baselineResponseTime", baselineTime);
```

### Compare Against Baseline

```javascript
pm.test("No performance regression", () => {
    const baseline = pm.environment.get("baselineResponseTime");
    const current = pm.response.responseTime;
    
    // Allow 20% variation
    const threshold = baseline * 1.2;
    
    if (current > threshold) {
        console.warn(`⚠️ Performance regression detected!`);
        console.warn(`Baseline: ${baseline}ms, Current: ${current}ms`);
    }
    
    pm.expect(current).to.be.below(threshold);
});
```

### Track Over Time

```javascript
const history = JSON.parse(pm.globals.get("performanceHistory") || "[]");
history.push({
    date: new Date().toISOString(),
    endpoint: pm.info.requestName,
    responseTime: pm.response.responseTime
});
pm.globals.set("performanceHistory", JSON.stringify(history));
```

## 11. Best Practices

### ✅ DO

- Set realistic SLAs for different operations
- Test response times regularly
- Monitor performance trends
- Test with realistic data volumes
- Use Newman for automated load tests
- Identify bottlenecks early
- Test caching effectiveness
- Track P95/P99 percentiles
- Run warm-up iterations before load tests
- Test under production-like conditions

### ❌ DON'T

- Only test happy path performance
- Ignore slow outliers
- Test against production (use staging)
- Skip baseline measurements
- Ignore database query times
- Test with unrealistic data
- Run load tests without monitoring
- Ignore cache performance
- Test only average response times

## 12. Performance Testing Checklist

### Before Testing

- [ ] Define SLAs for each endpoint type
- [ ] Set up monitoring/logging
- [ ] Create realistic test data
- [ ] Warm up the system
- [ ] Establish performance baseline

### During Testing

- [ ] Monitor response times
- [ ] Track error rates
- [ ] Watch for degradation
- [ ] Note breaking points
- [ ] Log anomalies

### After Testing

- [ ] Analyze results
- [ ] Compare to baseline
- [ ] Identify bottlenecks
- [ ] Document findings
- [ ] Create action items

## 13. Sample Performance Test Collection

```javascript
// Collection Pre-request
pm.globals.set("SLA_FAST", 500);
pm.globals.set("SLA_MEDIUM", 2000);
pm.globals.set("SLA_SLOW", 5000);

// Request: GET /users (Fast)
pm.test("GET users is fast", () => {
    const sla = pm.globals.get("SLA_FAST");
    pm.expect(pm.response.responseTime).to.be.below(sla);
});

// Request: POST /users (Medium)
pm.test("POST user is medium speed", () => {
    const sla = pm.globals.get("SLA_MEDIUM");
    pm.expect(pm.response.responseTime).to.be.below(sla);
});

// Request: GET /reports/analytics (Slow)
pm.test("Complex report is acceptable", () => {
    const sla = pm.globals.get("SLA_SLOW");
    pm.expect(pm.response.responseTime).to.be.below(sla);
});

// Collection Tests
const allTimes = JSON.parse(pm.globals.get("responseTimes") || "[]");
console.log(`Total requests: ${allTimes.length}`);
console.log(`Average: ${avg(allTimes)}ms`);
console.log(`P95: ${p95(allTimes)}ms`);
```

> **📸 HÌNH ẢNH:** Performance Test Results
> - File: `performance-test-results.png`
> - Nội dung: Dashboard showing performance metrics: average response time, P95, P99, với color-coded SLA thresholds (green < 500ms, yellow < 2s, red > 2s)

<!-- IMAGE_PLACEHOLDER: performance-test-results.png -->

## Tổng Kết

- ✅ Response time validation
- ✅ Load testing với Newman
- ✅ Performance benchmarks (SLA)
- ✅ Concurrent user simulation
- ✅ Cache testing
- ✅ Stress testing
- ✅ Regression detection
- ✅ P95/P99 tracking

---

[⬅️ Security](./security-best-practices.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Collaboration ➡️](./collaboration.md)
