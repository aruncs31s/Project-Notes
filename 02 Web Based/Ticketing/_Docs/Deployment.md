# Production Deployment Checklist

## Pre-Deployment

### Configuration
- [ ] Copy `.env.example` to `.env`
- [ ] Set `ENVIRONMENT=production`
- [ ] Generate strong `JWT_SECRET` (64+ characters)
- [ ] Configure `ALLOWED_ORIGINS` with actual domain(s)
- [ ] Set appropriate `RATE_LIMIT_PER_MIN` (recommended: 60)
- [ ] Configure `DATABASE_URL` if using external database
- [ ] Set `SERVER_HOST=0.0.0.0` for container deployment

### Security
- [ ] Review all environment variables
- [ ] Ensure `.env` is in `.gitignore`
- [ ] Enable HTTPS/TLS
- [ ] Configure firewall rules
- [ ] Set up SSL certificates
- [ ] Review CORS settings
- [ ] Test CSRF protection
- [ ] Verify rate limiting works

### Database
- [ ] Set up production database
- [ ] Configure connection pooling
- [ ] Set up database backups
- [ ] Test database migrations
- [ ] Create database indexes
- [ ] Set up database monitoring

### Testing
- [ ] Run all unit tests
- [ ] Run integration tests
- [ ] Test health endpoints
- [ ] Test metrics endpoint
- [ ] Load testing
- [ ] Security testing
- [ ] Test rate limiting
- [ ] Test CSRF protection

## Deployment

### Build
- [ ] Run `go mod tidy`
- [ ] Build binary: `go build -o server cmd/main.go`
- [ ] Test binary locally
- [ ] Create Docker image (if using containers)
- [ ] Tag image with version

### Infrastructure
- [ ] Set up load balancer
- [ ] Configure health check probes
- [ ] Set up auto-scaling (if needed)
- [ ] Configure logging aggregation
- [ ] Set up monitoring alerts
- [ ] Configure backup strategy

### Monitoring
- [ ] Set up health check monitoring
- [ ] Configure metrics collection
- [ ] Set up alerting (CPU, memory, errors)
- [ ] Configure log aggregation
- [ ] Set up uptime monitoring
- [ ] Create monitoring dashboard

## Post-Deployment

### Verification
- [ ] Verify application is running
- [ ] Test health endpoint: `/health`
- [ ] Test readiness: `/health/ready`
- [ ] Test liveness: `/health/live`
- [ ] Check metrics: `/metrics`
- [ ] Test authentication flow
- [ ] Verify rate limiting
- [ ] Check CORS headers
- [ ] Test error handling

### Monitoring
- [ ] Check application logs
- [ ] Verify metrics are being collected
- [ ] Test alerting system
- [ ] Monitor resource usage
- [ ] Check database connections
- [ ] Verify backup jobs

### Documentation
- [ ] Update deployment documentation
- [ ] Document environment variables
- [ ] Create runbook for common issues
- [ ] Document rollback procedure
- [ ] Update API documentation

## Environment Variables Checklist

### Required
```bash
JWT_SECRET=<64-char-random-string>
```

### Recommended for Production
```bash
ENVIRONMENT=production
SERVER_HOST=0.0.0.0
SERVER_PORT=8080
ALLOWED_ORIGINS=https://yourdomain.com
RATE_LIMIT_PER_MIN=60
DATABASE_URL=<connection-string>
```

## Health Check Endpoints

### For Load Balancers
```
Health Check: /health
Expected: 200 OK
Interval: 30s
Timeout: 5s
```

### For Kubernetes
```yaml
livenessProbe:
  httpGet:
    path: /health/live
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /health/ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

## Monitoring Alerts

### Critical Alerts
- [ ] Application down (health check fails)
- [ ] Database connection lost
- [ ] High error rate (>5%)
- [ ] Memory usage >90%
- [ ] CPU usage >90%

### Warning Alerts
- [ ] Response time >1s
- [ ] Error rate >1%
- [ ] Memory usage >70%
- [ ] CPU usage >70%
- [ ] Disk usage >80%

## Security Hardening

### Application Level
- [x] Rate limiting enabled
- [x] CSRF protection enabled (production)
- [x] Input sanitization enabled
- [x] Error handling configured
- [x] Session management configured
- [ ] HTTPS enforced
- [ ] Security headers configured

### Infrastructure Level
- [ ] Firewall configured
- [ ] DDoS protection enabled
- [ ] WAF configured (if applicable)
- [ ] VPC/Network isolation
- [ ] Secrets management
- [ ] Access control (IAM)

## Rollback Plan

### If Deployment Fails
1. Check application logs
2. Check health endpoints
3. Verify environment variables
4. Check database connectivity
5. Rollback to previous version if needed

### Rollback Steps
```bash
# Stop current version
systemctl stop ticketing-system

# Deploy previous version
cp /backup/server-v1.0 /opt/ticketing-system/server

# Start service
systemctl start ticketing-system

# Verify health
curl http://localhost:8080/health
```

## Performance Optimization

### Before Production
- [ ] Enable Gin release mode
- [ ] Configure connection pooling
- [ ] Set up caching (if needed)
- [ ] Optimize database queries
- [ ] Enable compression
- [ ] Configure CDN (if applicable)

### Monitoring Performance
- [ ] Track response times
- [ ] Monitor database query times
- [ ] Check memory usage patterns
- [ ] Monitor goroutine count
- [ ] Track error rates

## Backup Strategy

### Database Backups
- [ ] Daily full backups
- [ ] Hourly incremental backups
- [ ] Test restore procedure
- [ ] Off-site backup storage
- [ ] Backup retention policy

### Application Backups
- [ ] Configuration files backed up
- [ ] Binary versions archived
- [ ] Deployment scripts versioned
- [ ] Documentation backed up

## Disaster Recovery

### Recovery Time Objective (RTO)
- Target: < 1 hour

### Recovery Point Objective (RPO)
- Target: < 1 hour

### DR Procedures
- [ ] Document failover procedure
- [ ] Test DR plan quarterly
- [ ] Maintain DR environment
- [ ] Document recovery steps
- [ ] Train team on DR procedures

## Compliance

### Data Protection
- [ ] GDPR compliance (if applicable)
- [ ] Data encryption at rest
- [ ] Data encryption in transit
- [ ] Access logging enabled
- [ ] Data retention policy

### Audit
- [ ] Enable audit logging
- [ ] Log all authentication attempts
- [ ] Log all data modifications
- [ ] Regular security audits
- [ ] Compliance reporting

## Support

### On-Call
- [ ] Set up on-call rotation
- [ ] Configure alerting channels
- [ ] Create incident response plan
- [ ] Document escalation procedures
- [ ] Maintain contact list

### Documentation
- [ ] API documentation
- [ ] Deployment guide
- [ ] Troubleshooting guide
- [ ] Architecture documentation
- [ ] Runbook for common issues

## Sign-Off

### Development Team
- [ ] Code reviewed
- [ ] Tests passing
- [ ] Documentation updated
- [ ] Security review completed

### Operations Team
- [ ] Infrastructure ready
- [ ] Monitoring configured
- [ ] Backups configured
- [ ] DR plan tested

### Security Team
- [ ] Security scan completed
- [ ] Vulnerabilities addressed
- [ ] Compliance verified
- [ ] Penetration test passed

### Management
- [ ] Deployment approved
- [ ] Rollback plan approved
- [ ] Budget approved
- [ ] Timeline approved

---

**Deployment Date**: _______________
**Deployed By**: _______________
**Version**: _______________
**Sign-Off**: _______________

