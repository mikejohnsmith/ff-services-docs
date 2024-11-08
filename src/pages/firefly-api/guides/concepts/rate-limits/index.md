---
title: Rate Limits - Adobe Firefly API
description: This guide explains rate limiting for the Adobe Firefly API.
keywords:
  - Adobe Firefly Services
  - Rate limits
  - Firefly API
  - Developer documentation
  - Rate limiting concepts
  - API usage limits
  - Throttling
  - Rate limiting policies
  - Quotas
  - Request limits
  - API rate limiting strategies
  - Rate limit enforcement
  - Rate limit management
  - Usage quotas
  - Rate limit headers
  - Rate limit monitoring
  - Scalability considerations
  - Performance optimization
  - Rate limiting configurations
  - Rate limit exceptions
  - Client access
  - Concurrency
  - Monitors usage
  - API Usage
  - API performance
contributors:
  - https://github.com/amandahuarng
  - https://github.com/nimithajalal
  - https://github.com/hollyschinsky
  - https://github.com/bishoysefin
hideBreadcrumbNav: true
---

# Rate Limits

Adobe Firefly API places limits on the volume, frequency, and concurrency of API calls. This guide provides an overview of these limits, how to check them, why they are necessary, and what to do if you encounter issues.

## Summary of Rate Limits

Our API imposes the following rate limits **per organization**:

* **4** requests **per minute (RPM)**
* **9,000** requests **per day (RPD)**

It's important to note that rate limits are shared across **all users within your organization**. This means that all users within an organization share the same rate limits.

## Checking Rate Limits with Requests

If you exceed the rate limits, you'll receive an **HTTP 429 Too Many Requests** error. We recommend using the `retry-after` header to determine the number of seconds you should wait before trying again.

## Why do we have rate limits?

Rate limits are standard practice for APIs, and they serve several important purposes:

* **Preventing abuse**: Protects the API from being overwhelmed by excessive requests.
* **Ensuring fair usage**: Guarantees equal access to all users and organizations.
* **Managing server load**: Manages server load for consistent performance.
* **Protecting against downtime**: Reduces the risk of service interruptions.
* **Controlling costs**: Helps manage resource consumption and associated expenses.

## What to Do If You Run Into Issues

If you encounter rate limit issues:

1. **Review Your Usage:** reduce unnecessary request.
2. **Implement Retry Logic:** Use the `retry-after` header to wait before retrying.
3. **Contact Us for Assistance:** We appreciate that these limits may not be ideal for certain use cases. Please contact your account manager so that we can partner with you on setting the optimal thresholds for your account.
