# Requirements Document: Comic-3D-Tourer

## Overview

### Problem Statement
Traditional 3D virtual tour creation requires significant time, expertise, and cost. Businesses need expert 3D designers to manually model, texture, and set up scenes in Unity/Unreal/Blender, followed by heavy preprocessing and rendering. This results in:
- High time-to-market due to scene complexity
- Expensive labor costs requiring specialized 3D artists
- Difficulty capturing fine details like signage, textures, and real-world artifacts
- Complex update processes requiring full rework

### Solution
Comic-3D-Tourer is a 3D touring tool that enables commercial shops, heritage sites, and real estate companies to publish interactive virtual tours through an automated video-to-tour pipeline. Using Gaussian Splatting and Neural Radiance Fields, the system creates photoreal "digital twins" that deliver clearer, more accurate views than traditional 3D pipelines.

### Target Audiences
1. **Commercial Shops**: Retail stores, restaurants, showrooms seeking enhanced online engagement
2. **Heritage Sites**: Museums, historical landmarks, cultural institutions requiring preservation and showcase capabilities
3. **Real Estate Companies**: Property managers, brokers, and developers needing efficient property showcasing

## Unique Selling Propositions (USP)

- **Video → Virtual Tour Pipeline**: Upload video only; everything automated end-to-end
- **No 3D Artists Required**: Zero manual modeling, texturing, or scene setup
- **Photoreal Digital Twin Quality**: Captures signage, textures, and real-world artifacts with high fidelity
- **Faster Turnaround + Lower Cost**: Significantly reduced time-to-live compared to traditional workflows
- **Website-Ready Output**: Shareable link + iframe/SDK embed for seamless integration
- **Optimized for Real-Time Viewing**: Smooth performance on both mobile and desktop devices
- **Easy Updates**: Re-capture new video to refresh tours without starting from scratch

### Audience Benefits
- **Shops**: Better customer engagement + remote browsing capabilities
- **Heritage**: Digital preservation + high-fidelity showcase for global audiences
- **Real Estate**: Faster property showcasing + fewer physical visits + improved buyer confidence

## Goals and Non-Goals

### Goals
- Automate the entire pipeline from video upload to published virtual tour
- Deliver photoreal quality using Gaussian Splatting and NeRF technologies
- Provide real-time interactive viewing experience on web and mobile
- Enable easy embedding and sharing of tours
- Support tour updates through simple video re-upload
- Maintain cost-effective infrastructure with predictable pricing
- Ensure secure access control for private tours

### Non-Goals
- Manual 3D modeling or editing tools
- Real-time video streaming during capture
- VR headset native applications (web-based only)
- Social media features or community building
- Offline mobile applications

## Personas and Actors

### Tour Owner
Business owner or content manager who creates and manages virtual tours for their establishment.

### Tour Visitor
End user who views and interacts with published virtual tours.

### System Administrator
Technical operator who monitors system health, manages infrastructure, and handles operational tasks.

### API Consumer
Developer integrating Comic-3D-Tourer via SDK or API for custom applications.

## Core User Journeys

### Journey 1: Tour Owner Creates Virtual Tour
1. Owner signs up and authenticates via Cognito
2. Owner creates a new project and uploads walkthrough video
3. System automatically processes video (frame sampling, pose estimation, reconstruction)
4. Owner receives notification when tour is ready
5. Owner previews tour and adjusts settings (privacy, branding)
6. Owner publishes tour and receives shareable link + embed code
7. Owner monitors analytics (views, engagement, device types)

### Journey 2: Tour Visitor Views Virtual Tour
1. Visitor accesses tour via shared link or embedded iframe
2. Tour loads progressively with optimized streaming
3. Visitor navigates interactively using mouse/touch controls
4. Viewer adapts to device capabilities (mobile/desktop)
5. Analytics events captured for owner insights

### Journey 3: Admin Operations and Monitoring
1. Admin monitors job queue and processing status
2. Admin reviews system metrics (GPU utilization, costs, errors)
3. Admin investigates failed jobs and triggers retries
4. Admin manages user accounts and permissions
5. Admin reviews cost dashboards and optimizes resources

## Feature Requirements

### FR1: Upload and Ingest
- **FR1.1**: Support video upload via web interface (drag-drop or file picker)
- **FR1.2**: Accept common video formats (MP4, MOV, AVI) up to 10GB
- **FR1.3**: Validate video quality (resolution ≥1080p, frame rate ≥24fps)
- **FR1.4**: Display upload progress with percentage and estimated time
- **FR1.5**: Store raw video securely in S3 with encryption at rest
- **FR1.6**: Generate unique project ID and job ID for tracking

### FR2: Processing Pipeline
- **FR2.1**: Automatically sample best frames from uploaded video
- **FR2.2**: Remove blurry, redundant, or low-quality frames
- **FR2.3**: Estimate camera motion and poses using COLMAP or similar
- **FR2.4**: Reconstruct scene using Gaussian Splatting or NeRF
- **FR2.5**: Optimize for real-time performance (compression, LOD, tiling)
- **FR2.6**: Generate tour manifest with metadata and asset references
- **FR2.7**: Provide real-time job status updates via WebSocket
- **FR2.8**: Support job pause, resume, and retry on failure
- **FR2.9**: Automatically scale GPU workers based on queue depth

### FR3: Publishing and Distribution
- **FR3.1**: Generate unique shareable URL for each tour
- **FR3.2**: Provide iframe embed code with customizable dimensions
- **FR3.3**: Offer SDK for custom integrations
- **FR3.4**: Support public and private tour visibility settings
- **FR3.5**: Enable password protection for private tours
- **FR3.6**: Deliver tours via CloudFront CDN for global performance
- **FR3.7**: Generate thumbnail and preview images for sharing

### FR4: Interactive Viewer
- **FR4.1**: Render tours using Three.js and WebGL
- **FR4.2**: Support mouse navigation (click-drag to rotate, scroll to zoom)
- **FR4.3**: Support touch navigation on mobile devices
- **FR4.4**: Implement progressive loading with low-res preview
- **FR4.5**: Adapt rendering quality based on device capabilities
- **FR4.6**: Display loading indicators and error states
- **FR4.7**: Support fullscreen mode
- **FR4.8**: Provide navigation controls (reset view, auto-rotate)

### FR5: Analytics and Insights
- **FR5.1**: Track tour views and unique visitors
- **FR5.2**: Capture device types, browsers, and geographic locations
- **FR5.3**: Measure engagement metrics (time spent, interactions)
- **FR5.4**: Store analytics events in S3 for querying via Athena
- **FR5.5**: Display analytics dashboards in QuickSight
- **FR5.6**: Provide exportable reports (CSV, PDF)

### FR6: Tour Updates
- **FR6.1**: Allow owners to upload new video for existing tour
- **FR6.2**: Maintain version history of tours
- **FR6.3**: Support rollback to previous tour versions
- **FR6.4**: Preserve analytics data across updates
- **FR6.5**: Notify visitors of tour updates (optional)

### FR7: Admin Operations
- **FR7.1**: Dashboard showing active jobs, queue depth, and system health
- **FR7.2**: User management (view, suspend, delete accounts)
- **FR7.3**: Project management (view, archive, delete projects)
- **FR7.4**: Job management (retry, cancel, view logs)
- **FR7.5**: Cost monitoring and budget alerts
- **FR7.6**: System configuration management

## Non-Functional Requirements

### NFR1: Performance
- **NFR1.1**: Video upload should support resumable uploads for files >1GB
- **NFR1.2**: Tour viewer should load initial preview within 3 seconds on 4G connection
- **NFR1.3**: Interactive navigation should maintain ≥30fps on desktop, ≥24fps on mobile
- **NFR1.4**: Processing pipeline should complete within 24 hours for 10-minute video
- **NFR1.5**: API response time should be <200ms for 95th percentile
- **NFR1.6**: CDN cache hit ratio should exceed 85%

### NFR2: Reliability
- **NFR2.1**: System uptime should be ≥99.5% (excluding planned maintenance)
- **NFR2.2**: Failed jobs should automatically retry up to 3 times with exponential backoff
- **NFR2.3**: Data should be replicated across multiple availability zones
- **NFR2.4**: Graceful degradation when GPU capacity is exhausted
- **NFR2.5**: Zero data loss for uploaded videos and processed tours

### NFR3: Scalability
- **NFR3.1**: Support concurrent processing of 100+ jobs
- **NFR3.2**: Auto-scale GPU workers based on queue depth (scale up when >10 jobs queued)
- **NFR3.3**: Handle 10,000+ concurrent tour viewers
- **NFR3.4**: Support 1,000+ active projects per customer account
- **NFR3.5**: Database should handle 1M+ projects without performance degradation

### NFR4: Security and Privacy
- **NFR4.1**: All data encrypted at rest using KMS
- **NFR4.2**: All data encrypted in transit using TLS 1.3
- **NFR4.3**: Authentication via AWS Cognito with MFA support
- **NFR4.4**: Role-based access control (RBAC) for all resources
- **NFR4.5**: Private tours accessible only via signed URLs with expiration
- **NFR4.6**: WAF protection against common attacks (SQL injection, XSS, DDoS)
- **NFR4.7**: Audit logging for all administrative actions
- **NFR4.8**: Compliance with GDPR and CCPA for user data

### NFR5: Observability
- **NFR5.1**: Centralized logging via CloudWatch with structured JSON format
- **NFR5.2**: Distributed tracing via X-Ray for all API requests
- **NFR5.3**: Custom metrics for business KPIs (tours created, processing time, viewer engagement)
- **NFR5.4**: Alerting for critical failures (job failures, API errors, cost overruns)
- **NFR5.5**: SLO monitoring with automated incident creation

### NFR6: Cost Awareness
- **NFR6.1**: Use Spot instances for GPU workers where possible (target 70% cost reduction)
- **NFR6.2**: Implement S3 lifecycle policies (move to Glacier after 90 days)
- **NFR6.3**: Cache frequently accessed tours in ElastiCache
- **NFR6.4**: Implement LOD and tiling to reduce CDN egress costs
- **NFR6.5**: Provide cost estimates before processing starts
- **NFR6.6**: Alert when monthly costs exceed budget thresholds

## Technology Stack

### Frontend and Viewer
- Next.js / React (dashboard and admin interface)
- Three.js + WebGL (tour viewer and embed)
- WebSockets (live job status updates)

### Backend and APIs
- Python FastAPI (core ML APIs)
- Golang API (high performance and scalability for core system infrastructure)
- AWS Lambda (lightweight tasks: thumbnails, webhooks)

### Storage and Delivery
- Amazon S3 (videos, frames, tour artifacts, manifests)
- Amazon CloudFront (CDN for streaming tours)

### Processing and ML Compute
- Amazon EKS or ECS (services and workers)
- AWS Batch (GPU job scheduling)
- EC2 GPU instances (G5/P4/P5)
- AWS Step Functions (orchestrate: preprocess → train → optimize → publish)

### Caching
- Amazon ElastiCache for Redis:
  - Cache tour metadata/manifests, permission checks, signed URL results
  - Store job progress and real-time status
  - Rate limiting and session caching (if not handled by WAF)

### Data and Analytics
- Amazon RDS (PostgreSQL) for users, projects, jobs
- Amazon Athena + S3 for analytics queries
- Amazon QuickSight for dashboards
- Optional: DynamoDB or OpenSearch for specific use cases

### Security, Monitoring, and DevOps
- Amazon Cognito (authentication)
- IAM + KMS + Secrets Manager (security, encryption, secrets)
- AWS WAF + Shield (protection)
- CloudWatch + X-Ray (monitoring and tracing)
- ECR + CI/CD (GitHub Actions or CodePipeline) + IaC (Terraform or CDK)

## Acceptance Criteria

### AC1: Upload and Ingest
- User can upload video via web interface with progress indicator
- System validates video format and quality before accepting
- Raw video stored in S3 with encryption and unique project ID assigned
- User receives confirmation and estimated processing time

### AC2: Processing Pipeline
- System automatically samples frames and removes blurry/redundant ones
- Camera poses estimated and scene reconstructed using GS/NeRF
- Tour optimized for real-time viewing (compression, LOD, tiling applied)
- Job status updates delivered in real-time via WebSocket
- Failed jobs automatically retry up to 3 times

### AC3: Publishing
- Published tour accessible via unique shareable URL
- Iframe embed code generated with customizable dimensions
- Private tours require authentication or password
- Tours delivered via CloudFront with <3s initial load time

### AC4: Viewer Experience
- Tour renders smoothly at ≥30fps on desktop, ≥24fps on mobile
- Navigation works via mouse (desktop) and touch (mobile)
- Progressive loading shows low-res preview before full quality
- Viewer adapts to device capabilities automatically

### AC5: Analytics
- System tracks views, unique visitors, device types, and engagement
- Analytics events stored in S3 and queryable via Athena
- QuickSight dashboards display key metrics
- Reports exportable in CSV and PDF formats

### AC6: Updates
- Owner can upload new video to update existing tour
- Version history maintained with rollback capability
- Analytics data preserved across updates

### AC7: Security
- All data encrypted at rest (KMS) and in transit (TLS 1.3)
- Authentication via Cognito with MFA support
- Private tours accessible only via signed URLs with expiration
- WAF protects against common attacks

## Assumptions and Cost Notes

### GPU Compute
- Example instance: g3.4xlarge (122GB RAM, 16 vCPU, $1.12/hour)
- Monthly cost (24/7 operation): 24h × 30d = $806.40/month
- Spot instances can reduce costs by ~70%

### Caching
- Example instance: cache.m2.2xlarge (33.8GB RAM, 4 vCPU, $0.604/hour)
- Monthly cost (24/7 operation): 24h × 30d = $434.88/month

### Storage (S3 Standard)
- Cost: ~$0.023/GB-month
- For 1TB (1024GB): $23.55/month

### Delivery (CloudFront CDN)
- Data transfer out: ~$0.085/GB (NA/EU) or ~$0.14/GB (Asia)
- For 1TB: $87.04 (NA/EU) or $143.36 (Asia)

### Cost Optimization Strategies
- Use Spot instances for GPU workers (target 70% savings)
- Implement aggressive caching to reduce CDN egress
- Apply S3 lifecycle policies (move to Glacier after 90 days)
- Use LOD and tiling to minimize data transfer
- Monitor and alert on cost anomalies

## Risks and Mitigations

### Risk 1: GPU Capacity Exhaustion
- **Impact**: Processing delays, poor user experience
- **Mitigation**: Auto-scaling with queue-based triggers; fallback to CPU processing for low-priority jobs; communicate estimated wait times

### Risk 2: High CDN Costs
- **Impact**: Budget overruns, reduced profitability
- **Mitigation**: Aggressive caching strategy; LOD/tiling to reduce payload size; monitor egress and alert on anomalies

### Risk 3: Poor Reconstruction Quality
- **Impact**: Unsatisfactory tours, customer churn
- **Mitigation**: Video quality validation at upload; provide recording guidelines; manual review option for premium customers

### Risk 4: Security Breach
- **Impact**: Data loss, reputational damage, legal liability
- **Mitigation**: Defense-in-depth (WAF, encryption, IAM, audit logs); regular security audits; incident response plan

### Risk 5: Vendor Lock-in (AWS)
- **Impact**: Limited flexibility, potential cost increases
- **Mitigation**: Abstract cloud services behind interfaces; document migration paths; monitor AWS pricing changes

## Open Questions and Future Scope

### Open Questions
- What is the maximum acceptable processing time for users?
- Should we support live collaboration features (multiple owners)?
- What level of customization should be available for viewer UI?
- Should we offer white-label solutions for enterprise customers?

### Future Scope
- Mobile app for video capture with guided recording
- AI-powered scene annotations and hotspots
- Multi-language support for viewer interface
- Integration with CRM and marketing platforms
- Advanced analytics (heatmaps, attention tracking)
- Support for outdoor and large-scale environments
- Real-time collaborative editing of tours
