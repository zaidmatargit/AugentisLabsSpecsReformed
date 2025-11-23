# Specification: Performance Optimization Agent

## Goal

Automate performance analysis and optimization recommendations through Lighthouse score analysis, bundle size optimization, and caching strategy implementation to achieve <2s LCP and <300ms API latency targets.

## User Stories

- As a developer, I want automated performance analysis so that I can identify bottlenecks without manual profiling
- As a user, I want fast page loads (<2s LCP) so that I have a smooth experience
- As a tech lead, I want Lighthouse score optimization so that I can meet Core Web Vitals targets

## Specific Requirements

**Automated Performance Analysis**
- Run Lighthouse CI audits on all pages automatically
- Measure Core Web Vitals (LCP, FID, CLS) and track trends
- Identify performance bottlenecks (render-blocking resources, large images, slow APIs)
- Generate performance reports with actionable recommendations

**Lighthouse Score Optimization**
- Target Lighthouse performance score ≥90
- Optimize for accessibility score ≥90 (WCAG AA compliance)
- Achieve best practices score ≥90
- Improve SEO score ≥90

**Bundle Size Optimization**
- Analyze JavaScript bundle sizes and identify large dependencies
- Recommend code splitting strategies for route-based chunking
- Identify unused dependencies and dead code
- Alert on bundle size increases >10% in pull requests

**Caching Strategy Implementation**
- Configure HTTP caching headers for static assets (1 year cache)
- Implement Next.js static page caching and ISR where appropriate
- Set up CDN caching via Vercel Edge Network
- Cache API responses with appropriate TTLs

**Performance Monitoring**
- Track performance metrics over time and identify regressions
- Alert on performance degradation (LCP >2s, API P95 >300ms)
- Compare performance across environments (staging vs. production)
- Generate performance improvement recommendations

## Visual Design

No visual assets provided. Design system should follow:
- Performance dashboard with Lighthouse scores and Core Web Vitals
- Bundle size trend chart over time
- Performance waterfall charts showing resource loading
- Optimization recommendation cards with priority and impact estimates

## Existing Code to Leverage

**Monitoring Integration**
- Reference monitoring & observability setup for performance tracking
- Use similar alerting patterns for performance degradation
- Integrate with Vercel Analytics for real user monitoring

**Code Quality Gates Integration**
- Reference code quality gates for CI/CD integration
- Add performance checks to quality gate validation
- Block deploys if performance regresses significantly

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create Performance Optimization Agent using StateGraph
- Use structured output for optimization recommendations
- Prioritize recommendations by impact and effort

## Out of Scope

- Real-time performance optimization during runtime (build-time only)
- Custom performance profiling tools (using Lighthouse and Vercel Analytics)
- Database query optimization beyond N+1 detection (manual tuning)
- Advanced caching strategies (Redis, service workers) beyond HTTP caching
- Image optimization beyond Next.js Image component
- Server-side rendering (SSR) optimization beyond Next.js defaults
- Progressive Web App (PWA) setup for offline performance
- Performance budgets and enforcement in CI/CD
- Third-party script optimization and lazy loading
- Geographic performance testing from multiple regions
