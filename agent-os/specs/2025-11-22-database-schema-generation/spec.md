# Specification: Database Schema Generation

## Goal

Automatically generate optimized PostgreSQL database schemas from PRD data models, including relationship mapping, index optimization, and migration scripts to support scalable multi-tenant data architecture.

## User Stories

- As a backend developer, I want database schemas auto-generated from requirements so that I can avoid manual schema design
- As a DBA, I want optimized indexes so that queries perform efficiently at scale
- As an architect, I want relationship mapping so that data integrity is enforced at the database level

## Specific Requirements

**Automated Database Schema Creation**
- Generate Prisma schema from PRD entity models and relationships
- Define all tables, columns, data types, and constraints
- Apply naming conventions (snake_case for columns, plural table names)
- Include audit fields (created_at, updated_at, created_by) on all entities

**Relationship Mapping**
- Define foreign key relationships between entities
- Configure cascade delete/update behaviors appropriately
- Create junction tables for many-to-many relationships
- Enforce referential integrity with database constraints

**Index Optimization**
- Generate indexes for foreign keys automatically
- Create composite indexes for common query patterns
- Add unique indexes for natural keys and business constraints
- Include partial indexes for tenant isolation queries

**Multi-Tenant Schema Design**
- Add org_id column to all tenant-scoped tables
- Generate Row-Level Security (RLS) policies for tenant isolation
- Create indexes on tenant identifiers for query performance
- Validate tenant context in all data access patterns

**Migration Scripts**
- Generate initial Prisma migration for schema creation
- Include seed data scripts for reference data
- Create database setup scripts for local development
- Document schema dependencies and initialization order

## Visual Design

No visual assets provided. Design system should follow:
- Entity-relationship diagram (ERD) visualization
- Schema browser showing tables, columns, and relationships
- Index coverage analysis showing indexed vs. non-indexed queries
- Migration preview showing SQL DDL statements

## Existing Code to Leverage

**Database Models**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Use existing SQLAlchemy models as reference for Prisma schema
- Apply same multi-tenant patterns (org_id, RLS)
- Maintain consistent naming and structure

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create Schema Generation Agent using StateGraph
- Use structured output for Prisma schema generation
- Validate schema against best practices

## Out of Scope

- NoSQL database support (PostgreSQL only)
- Sharding and horizontal scaling strategies (single database)
- Database replication configuration (infrastructure concern)
- Advanced performance tuning (query optimization, partitioning)
- Data archiving and retention policies
- Full-text search schema design (basic text search only)
- Temporal tables and historical data tracking
- Database encryption configuration beyond Supabase defaults
- Custom database functions and stored procedures
- Multi-database schema synchronization
