# Troubleshooting AI Agents Operations Workflows

This document lists common issues and troubleshooting steps for AI agent-based IT operations workflows.

## 1. Orchestrator Is Not Responding

### Symptom

The API or workflow endpoint does not respond.

### Possible Causes

* Orchestrator service is down
* Wrong port
* Reverse proxy issue
* Container is stopped
* Firewall is blocking access
* Environment variables are missing

### Recommended Checks

```bash
docker ps
docker logs orchestrator
curl http://localhost:3000/api/health
```

Check listening ports:

```bash
ss -tulpn
```

---

## 2. Webhook Is Not Receiving Updates

### Symptom

Workflow runs, but the external system does not receive status updates.

### Possible Causes

* Incorrect webhook URL
* External system unavailable
* Authentication failure
* Invalid payload format
* Timeout
* Firewall or proxy issue

### Recommended Checks

* Validate webhook URL
* Test with a sample payload
* Check orchestrator logs
* Check external system run history
* Confirm HTTP response code
* Confirm payload schema

---

## 3. Workflow Stuck in Running Status

### Symptom

The workflow starts but never reaches success or failed status.

### Possible Causes

* Agent timeout
* Missing callback
* Failed status update
* Queue issue
* Background worker stopped
* External dependency unavailable

### Recommended Checks

* Check agent logs
* Check orchestrator logs
* Review workflow ID
* Validate status update logic
* Confirm queue or worker status
* Add timeout handling

---

## 4. Agent Returns an Unexpected Result

### Symptom

The workflow completes but the result is incorrect or incomplete.

### Possible Causes

* Wrong task type
* Incomplete input payload
* Missing context
* Incorrect routing
* Agent prompt or logic issue
* External API returned unexpected data

### Recommended Checks

* Review original request payload
* Validate task type mapping
* Check agent response
* Review routing rules
* Add validation before final response

---

## 5. API Gateway Returns 502 Bad Gateway

### Symptom

The gateway is reachable, but the backend service is not.

### Possible Causes

* Backend service is down
* Wrong upstream URL
* DNS issue
* Network issue
* Container is unhealthy
* Reverse proxy misconfiguration

### Recommended Checks

```bash
curl http://localhost:3000/api/health
docker ps
docker logs gateway
docker logs orchestrator
```

If using Nginx:

```bash
sudo nginx -t
sudo systemctl status nginx
sudo tail -f /var/log/nginx/error.log
```

---

## 6. Environment Variables Are Missing

### Symptom

The service starts but fails when calling integrations or storage services.

### Possible Causes

* Missing `.env` file
* Wrong variable names
* Secret not loaded
* Incorrect deployment configuration

### Recommended Checks

* Compare `.env` with `.env.example`
* Validate required variables
* Restart service after changes
* Check startup logs

---

## 7. Notification Was Sent Twice

### Symptom

The same notification appears multiple times.

### Possible Causes

* Retry logic without idempotency
* Duplicate webhook calls
* Workflow was triggered twice
* External platform retried the request

### Recommended Checks

* Use workflow ID to detect duplicates
* Add idempotency keys
* Review retry configuration
* Check request logs

## General Troubleshooting Flow

```text
Check service health
        ↓
Check logs
        ↓
Validate request payload
        ↓
Confirm routing
        ↓
Check agent result
        ↓
Check status update
        ↓
Check webhook delivery
```

## Security Notes

Do not publish real logs, production payloads, webhook URLs, API keys, tokens, internal domains, private IP addresses, or customer data.
