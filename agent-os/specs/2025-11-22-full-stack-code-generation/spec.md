# Specification: Full-Stack Code Generation

## Goal

Generate production-ready Next.js 14 App Router applications with Supabase backend integration, TypeScript implementation, and Tailwind CSS styling from PRD and design specifications to accelerate development in the Develop phase.

## User Stories

- As a venture creator, I want a complete full-stack application generated from my designs so that I can skip manual coding
- As a developer, I want TypeScript code following best practices so that the codebase is maintainable and type-safe
- As a designer, I want pixel-perfect implementation of my designs using Tailwind CSS so that the final product matches my vision

## Specific Requirements

**Next.js 14 App Router Application**
- Generate Next.js 14 application using App Router (not Pages Router)
- Implement server components and client components appropriately
- Configure routing based on PRD user flows
- Set up layouts and page templates per design specifications

**Supabase Backend Integration**
- Integrate Supabase client for database queries and authentication
- Generate API routes for CRUD operations on all entities
- Implement Row-Level Security (RLS) policies for multi-tenant data isolation
- Configure Supabase Auth with OAuth providers (Google, GitHub, Microsoft)

**TypeScript Implementation**
- Generate fully typed TypeScript code (no `any` types)
- Create type definitions for all data models and API responses
- Use Zod for runtime validation where needed
- Configure strict TypeScript compiler settings

**Tailwind CSS Styling**
- Implement responsive designs using Tailwind CSS utility classes
- Generate custom Tailwind config with brand colors and typography
- Use Tailwind components for consistency (buttons, forms, cards)
- Ensure WCAG AA accessibility compliance for color contrast

**Code Quality Standards**
- Follow Next.js and React best practices (component composition, hooks)
- Implement error boundaries and loading states
- Use consistent naming conventions (camelCase for variables, PascalCase for components)
- Generate ESLint and Prettier configurations

## Visual Design

No visual assets provided. Design system should follow:
- Component library matching generated design system
- Responsive layouts for mobile, tablet, desktop
- Accessibility features (ARIA labels, keyboard navigation)
- Loading skeletons and error states

## Existing Code to Leverage

**API Router Patterns**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/api/src/app/routers/`
- Apply similar API structure for consistency
- Reuse error handling and validation patterns

**Database Models**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Generate Prisma models following similar structure
- Apply multi-tenant isolation patterns (org_id, RLS)

**LangGraph Agent for Code Generation**
- Create Code Generation Agent using StateGraph orchestration
- Generate code iteratively (models → API → components → pages)
- Validate generated code syntax before output

## Out of Scope

- Custom framework support beyond Next.js (no Remix, SvelteKit, etc.)
- Backend languages other than TypeScript (no Python, Go, etc.)
- Database support beyond PostgreSQL via Supabase
- Real-time features using WebSockets (deferred)
- Advanced animations and micro-interactions
- Progressive Web App (PWA) configuration
- Internationalization (i18n) setup
- Advanced state management (Redux, Zustand) - use React Context only
- GraphQL API generation (REST only)
- Server-side rendering optimization beyond Next.js defaults
