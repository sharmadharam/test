# Engineering Architecture Proposal
**Certano**  
Testing Enhancements and Development Workflow

| Date | Change | By |
|------|--------|-----|
| February 6, 2026 | Initial Draft | Engineering Team |

---

**Confidential and Proprietary**

---

## Table of Contents
- [Introduction](#introduction)
- [Context](#context)
- [High-Level Architecture](#high-level-architecture)
- [Detailed Components](#detailed-components)
- [Technologies & Tools](#technologies--tools)
- [Patterns & Practices](#patterns--practices)
- [Dependency Management](#dependency-management)
- [Deployment Strategy](#deployment-strategy)
- [Scalability & Performance](#scalability--performance)
- [Security](#security)
- [Monitoring & Logging](#monitoring--logging)
- [Documentation and Knowledge Sharing](#documentation-and-knowledge-sharing)
- [Conclusion](#conclusion)

---

## Introduction

In this project, we will be introducing automated tests to an existing system and enhancing the development workflow with modern CI/CD practices. There are UI enhancements that may occur in parallel but will not be covered in this document.

---

## Context

This document outlines the primary components of an efficient automated testing workflow, a continuous integration (CI) system, continuous deployment (CD) pipeline, and associated testing frameworks.

A **CI/CD pipeline** is a set of tools that automates the process of building, testing, and deploying software. It is an essential part of a modern software development pipeline, as it helps to ensure that the software is always in a releasable state.

A **testing framework** is a set of tools and processes that are used to validate the functionality and correctness of the software. It typically includes a set of test cases, which define the expected behavior of the software under a variety of conditions. These tests can be any combination of unit, integration, and acceptance/end-to-end tests.

When a CI/CD system is used in conjunction with a testing framework, it can automatically run the test cases every time new code is committed to the repository. This provides a number of benefits, including but not limited to:
- Early detection of bugs and issues
- Increased development feedback cycle
- Increased confidence that new code has a lower risk of introducing new regressions
- Automated deployments with confidence

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              Developer                                          │
│                          (Local Development)                                    │
└──────────────────────────────┬──────────────────────────────────────────────────┘
                               │
                               │ git push & create PR
                               ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         GitHub Repository                                       │
│                        (Source Control - Git)                                   │
└──────────────────────────────┬──────────────────────────────────────────────────┘
                               │
                               │ PR created (webhook trigger)
                               ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         GitHub Actions                                          │
│                   (CI - Automated Testing on PRs)                               │
│                                                                                 │
│  ┌────────────────┐  ┌────────────────┐  ┌──────────────────────┐               │
│  │     Build      │→ │   Unit Tests   │→ │  Integration Tests   │               │
│  │   Frontend     │  │    Backend     │  │     End-to-End       │               │
│  │   & Backend    │  │    Frontend    │  │       Tests          │               │
│  └────────────────┘  └────────────────┘  └──────────────────────┘               │
└──────────────────────────────┬──────────────────────────────────────────────────┘
                               │
                               │ PR merge (after approval & tests pass)
                               ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              Jenkins                                            │
│                      (CD - One-Click Deployments)                               │
│                                                                                 │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐  ┌──────────────┐   │
│  │      Demo      │→ │    Full QA     │→ │    Staging     │→ │  Production  │   │
│  │   Deployment   │  │    Testing     │  │   Deployment   │  │  Deployment  │   │
│  └────────────────┘  └────────────────┘  └────────────────┘  └──────────────┘   │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Detailed Components

### Continuous Integration with GitHub Actions

A GitHub Actions CI workflow for validating compilation, unit tests, integration tests, and end-to-end tests should begin with a **build step**, ensuring that both the React frontend and Rails backend can be successfully compiled and all dependencies are properly installed.

This step should be followed by a **unit testing step**, where individual units of code are tested in isolation:
- **Backend**: Rails models, controllers, and services tested with RSpec
- **Frontend**: React components tested with Jest and React Testing Library

Once the unit tests have passed, the workflow should proceed to an **integration testing step**, where the interaction between different parts of the system is validated, followed by **end-to-end tests** using tools like Cypress or Playwright or Cucumber to ensure the application works correctly from the user's perspective. Cucumber can also provide behavior-driven testing with Gherkin scenarios that serve as living documentation.

By following this order of validation, development teams can catch and address issues early in the development process, reducing the risk of bugs and other issues making it to production, and ultimately delivering high-quality software that meets the needs of users.

### Continuous Deployment with Jenkins

Jenkins provides a robust one-click deployment mechanism with the following features:
- **Release tag-based deployments** ensuring consistency across all environments
- **Parameterized builds** allowing selection of specific release tags to deploy
- **Environment-specific deployments** (demo, staging, production)
- **Rollback capabilities** for quick recovery to previous release tags
- **Deployment notifications** via Slack or email
- **Post-deployment smoke tests** to verify successful deployment

---

## Technologies & Tools

### Primary Software Stack

#### Frontend: React¹ with Material-UI²

The frontend is built using **React**, a popular JavaScript library for building user interfaces, combined with **Material-UI (MUI)**, a comprehensive React component library that implements Google's Material Design.

**Key Features:**
- Component-based architecture for reusability
- Modern, responsive UI components out of the box
- Strong TypeScript support for type safety
- Large ecosystem and active community

#### Backend: Ruby on Rails³

The backend utilizes **Ruby on Rails**, a mature and battle-tested web application framework known for convention over configuration and rapid development capabilities.

**Key Features:**
- RESTful API design patterns
- Active Record ORM for database interactions
- Rich ecosystem of gems (libraries)
- Built-in security features

### Version Control: Git⁴

**Git** is used as the distributed version control system, providing:
- Branching and merging capabilities
- Complete history tracking
- Collaborative development support
- Integration with GitHub for code reviews

**GitHub** serves as the central repository host, offering:
- Pull request workflow
- Code review tools
- Issue tracking
- GitHub Actions integration

### Testing Frameworks

#### Backend Testing: RSpec⁵

**RSpec** is the primary testing framework for the Rails backend, providing:
- Behavior-driven development (BDD) syntax
- Extensive matchers for assertions
- Request specs for API testing
- Database cleaner integration
- Factory patterns with FactoryBot

#### Frontend Testing: Jest & React Testing Library⁶

**Jest** combined with **React Testing Library** provides comprehensive testing for React components:
- Unit tests for individual components
- Integration tests for component interactions
- Snapshot testing for UI consistency
- Mock functions for isolated testing

#### End-to-End Testing: Cypress, Playwright & Cucumber⁷

**Cypress** or **Playwright** are modern end-to-end testing frameworks that test the application from the user's perspective:
- Real browser testing
- Automatic waiting and retries
- Time-travel debugging (Cypress)
- Cross-browser support
- Screenshot and video capture on failure

**Cucumber** provides behavior-driven development (BDD) capabilities for end-to-end testing:
- Gherkin syntax for human-readable test scenarios
- Living documentation that stakeholders can understand
- Reusable step definitions
- Integration with Cypress or Playwright for browser automation
- Collaboration between technical and non-technical team members

### Continuous Integration: GitHub Actions⁸

**GitHub Actions** provides native CI/CD capabilities directly integrated with GitHub:
- YAML-based workflow configuration
- Matrix builds for testing multiple versions
- Caching for faster builds
- Secret management for credentials
- Status checks on pull requests

### Continuous Deployment: Jenkins⁸

**Jenkins** provides a flexible and powerful deployment automation platform:
- Pipeline as code (Jenkinsfile)
- One-click deployments via parameterized builds
- Environment promotion (dev → staging → production)
- Post-deployment verification
- Rollback capabilities

### Additional Tools

#### Code Quality: ESLint & Rubocop⁹
- **ESLint** for JavaScript/React code quality and consistency
- **Rubocop** for Ruby code style and best practices
- Automated checks in GitHub Actions

#### API Documentation: Swagger/OpenAPI¹⁰
- Interactive API documentation
- Request/response examples
- Try-it-out functionality

#### Database: PostgreSQL or MySQL¹¹
**PostgreSQL:**
- Reliable, ACID-compliant database
- JSON support for flexible data structures
- Full-text search capabilities
- Advanced indexing (GiST, GIN)

**MySQL:**
- Proven reliability and performance
- Wide ecosystem and community support
- Strong replication capabilities
- Excellent for read-heavy workloads

Both databases are well-supported by Ruby on Rails and provide robust solutions for production environments.

---

## Patterns & Practices

### Agile Methodology

The development lifecycle is recommended to follow **Agile methodologies** over the waterfall development approach. We have found that Agile handles the unknowns of software development extremely well and allows us to pivot quickly and effectively. It allows us to swap out a component or change a technology during active development without being tied to a rigid waterfall approach.

### Test-Driven Development (TDD)

Ideally, all code is driven by **Test-Driven Development**.

Test-driven development (TDD) is a disciplined approach to software development that involves writing automated tests before writing the corresponding code. This discipline helps ensure that the code is correct, maintainable, and testable. By focusing on small, incremental changes and continuously testing the code, TDD helps developers catch and fix bugs early in the development process, reducing technical debt and improving overall code quality.

### Version Control with Git

To manage dependencies effectively, we use **Git** to track changes to the codebase and ensure that all dependencies are properly documented and versioned.

### AI-Assisted Development

Modern development practices increasingly leverage **AI-powered tools** to accelerate development, improve code quality, and enhance developer productivity.

**Frontend Development:**
- AI pair programming tools assist with React component creation and Material-UI integration
- Automated code completion and refactoring suggestions
- Real-time bug detection and fix recommendations
- Test generation for React components and hooks
- Documentation generation for components and APIs

**Backend Development:**
- AI assistance for Ruby on Rails controller and model generation
- Automated API endpoint creation and validation
- Database query optimization suggestions
- Security vulnerability detection and remediation
- Test case generation for RSpec unit and integration tests

**GitHub Copilot for Pull Request Reviews:**
- **GitHub Copilot** is integrated into the pull request review process
- Provides automated code review suggestions and identifies potential issues
- Analyzes code changes for bugs, security vulnerabilities, and best practice violations
- Suggests improvements for code quality, performance, and maintainability
- Team members are encouraged to leverage Copilot reviews alongside human peer reviews
- Copilot feedback complements manual code review but does not replace human judgment

**Best Practices:**
- AI suggestions are reviewed and validated by developers before integration
- Code generated by AI tools undergoes the same CI/CD pipeline and testing requirements
- Developers maintain ownership and understanding of all code
- AI tools complement, rather than replace, human expertise and code review processes
- Team members are encouraged to use AI assistants for repetitive tasks, allowing focus on complex problem-solving

---

## Dependency Management

### Frontend (npm/yarn)

**package.json** manages all frontend dependencies with proper version constraints and lock files to ensure consistent installations across environments.

### Backend (Bundler)

**Gemfile** manages all Rails dependencies with semantic versioning to maintain stability and compatibility.

### Version Control with Git

To manage dependencies effectively, we use **Git** to track changes to the codebase and ensure that all dependencies are properly documented and versioned.

---

## Deployment Strategy

### Pull Request Workflow

1. **Developer Creates PR**
   - Branch from `develop`
   - Implement feature/fix
   - Push to GitHub

2. **GitHub Actions CI Runs**
   - Build frontend and backend
   - Run all tests (unit, integration, E2E)
   - Check code quality (ESLint, Rubocop)
   - Verify test coverage

3. **Code Review**
   - Team members review code
   - Automated checks must pass
   - Discussion and feedback

4. **Merge to Develop**
   - Squash commits
   - Delete feature branch
   - Triggers develop branch tests

5. **Create Release Tag**
   - After all features are merged and tested
   - Create a semantic version tag from GitHub (e.g., v1.2.3)
   - Tag serves as the deployment artifact for all environments

### Jenkins Deployment Pipeline

After a release tag is created in GitHub, all deployments are manual and on-demand via Jenkins one-click deployments. The release tag ensures consistent deployments across all environments and provides clear traceability of what code is running in each environment.

**Demo Deployment:**
- One-click deployment using the selected release tag
- Initial smoke tests to verify basic functionality
- Available for internal team review

**Full QA Testing:**
- Comprehensive manual and automated testing in Demo environment
- Test all user workflows and edge cases
- Performance and security testing
- Sign-off required from QA team before proceeding

**Staging Deployment:**
- One-click deployment using the same release tag
- Production-like environment for final validation
- Final smoke testing by QA team
- Requires manual approval to proceed to production

**Production Deployment:**
- One-click deployment using the verified release tag
- Requires manual approval from authorized personnel
- Blue-green deployment for zero downtime
- Automatic rollback on smoke test failure

### Rollback Strategy

**Quick Rollback:**
- Redeploy using a previous release tag from the container registry
- One-click rollback to any previously deployed release version
- Database migration rollback procedures documented
- Release tags provide clear audit trail of deployed versions

---

## Scalability & Performance

### Frontend Optimization

Frontend performance can be enhanced through code splitting, lazy loading, and caching strategies. Performance monitoring tools should be integrated into the CI pipeline to ensure consistent performance standards.

### Backend Optimization

Backend performance relies on proper database indexing, caching layers, background processing for asynchronous tasks, and API optimization techniques such as pagination and rate limiting.

### Horizontal Scaling

The application should be designed to support horizontal scaling through load balancing, session management, and database replication strategies.

---

## Security

Secure software development involves considering security throughout the software development lifecycle, from design and implementation to deployment and maintenance.

### Application Security

**Authentication & Authorization:**
- JWT-based authentication for API
- Role-based access control (RBAC)
- Password hashing with bcrypt
- Session management best practices

**API Security:**
- CORS configuration
- Rate limiting per user/IP
- Input validation and sanitization
- SQL injection prevention (parameterized queries)
- XSS protection

**Data Protection:**
- Encryption at rest (database)
- Encryption in transit (HTTPS/TLS)
- Secrets management (environment variables, AWS Secrets Manager)

### Infrastructure Security

**Container Security:**
- Base images from trusted sources
- Regular security scanning (Trivy, Snyk)
- Minimal base images (Alpine Linux)
- Non-root user execution

**Network Security:**
- VPC isolation
- Security groups/firewall rules
- Private subnets for databases
- WAF (Web Application Firewall)

### CI/CD Security

**GitHub Security:**
- Branch protection rules
- Required status checks
- Signed commits
- Dependabot security alerts

**Jenkins Security:**
- Access control and authentication
- Secrets in credential store
- Audit logging
- Plugin security updates

---

## Monitoring & Logging

### Application Monitoring

**Frontend Monitoring:**
- **Grafana**: Visualization and monitoring dashboards

**Backend Monitoring:**
- **New Relic or DataDog**: Application performance monitoring

### Infrastructure Monitoring

**Server Monitoring:**
- CPU, memory, disk usage
- Network metrics
- Container health
- Service availability

**Database Monitoring:**
- Connection pool utilization
- Query performance
- Disk space

### Log Management

**Centralized Logging:**
- **ELK Stack** (Elasticsearch, Logstash, Kibana) or **Datadog Logs**
- Structured logging (JSON format)
- Log levels (DEBUG, INFO, WARN, ERROR)
- Thread ID logging for tracing (as implemented)

**Log Format Example:**
```
I, [2026-02-06T14:30:45.123456 #12345] [TID-70123456789]  INFO -- :  User login successful: user_id=123
```

**Log Retention:**
- Application logs: 90 days
- Audit logs: 1 year
- Security logs: 2 years

### Jenkins Monitoring

**Deployment Monitoring:**
- Deployment frequency
- Lead time for changes
- Mean time to recovery (MTTR)
- Deployment success rate

**Alerting:**
- Slack or Email notifications for:
  - Deployment started/completed
  - Deployment failures
  - Test failures in staging

---

## Documentation and Knowledge Sharing

Performing a software development knowledge transfer and documentation is an essential step in ensuring the continuity and maintainability of a software system.

### Code Documentation

**Frontend Documentation:**
- **README files**: Setup instructions, architecture decisions
- **Inline comments**: Complex logic explanation

**Backend Documentation:**
- **API documentation**: Swagger/OpenAPI specs
- **Database schema**: ERD diagrams
- **Inline comments**: Complex business logic

### Architecture Documentation

**Architecture Decision Records (ADRs):**
- Document significant technical decisions
- Include context, decision, and consequences
- Version controlled with code

**System Documentation:**
- High-level architecture diagrams
- Data flow diagrams
- Infrastructure diagrams
- Integration points

### Process Documentation

**Development Guides:**
- Setup and installation instructions
- Development environment configuration
- Coding standards and conventions
- Git workflow and branching strategy

**Operational Runbooks:**
- Deployment procedures
- Rollback procedures
- Incident response procedures
- Common troubleshooting steps

### Knowledge Sharing

**Regular Knowledge Sharing Sessions:**
- Weekly tech talks (30 minutes)
- Architecture review meetings
- Code walkthroughs for complex features
- Pair programming sessions


### Post-Project Knowledge Transfer

Multiple, short Question and Answer sessions for a few weeks post-active-project development. These sessions can be tailored to the client's target audience (client's internal development team, operators, or users of the system, etc.). These sessions can also include:

- **Recorded demos** of key features and workflows
- **Video tutorials** for common tasks
- **Interactive workshops** for hands-on learning
- **Office hours** for ad-hoc questions

---

## Conclusion

In an Agile development environment, requirements and priorities may change frequently, and the development team must be flexible and adaptable to these changes. Therefore, the software development best practices, architecture, and design recommendations included in this document may evolve as the project progresses.

### Key Takeaways

**Technology Stack:**
- **Frontend**: React with Material-UI for modern, responsive UI
- **Backend**: Ruby on Rails for robust, maintainable API
- **CI**: GitHub Actions for automated testing on every PR
- **CD**: Jenkins for one-click, controlled deployments

**Quality Assurance:**
- Comprehensive test coverage (unit, integration, E2E)
- Automated code quality checks (ESLint, Rubocop)
- Security scanning in CI pipeline
- Performance monitoring and optimization

**Development Workflow:**
- Git-based version control with feature branches
- Pull request workflow with peer reviews
- Test-driven development practices
- Agile methodology with 2-week sprints

**Deployment Strategy:**
- Manual one-click deployments to demo via Jenkins
- Manual one-click deployments to staging via Jenkins
- Manual one-click deployments to production via Jenkins
- Blue-green deployments for zero downtime
- Quick rollback capabilities

**Observability:**
- Centralized logging with thread ID tracking
- Application and infrastructure monitoring
- Automated alerting for critical issues
- Performance tracking and optimization

---

**Confidential and Proprietary**

---

## Footnotes

1. React: https://react.dev/
2. Material-UI (MUI): https://mui.com/
3. Ruby on Rails: https://rubyonrails.org/
4. Git: https://git-scm.com/
5. RSpec: https://rspec.info/
6. Jest & React Testing Library: https://jestjs.io/ & https://testing-library.com/react
7. Cypress: https://www.cypress.io/ | Playwright: https://playwright.dev/ | Cucumber: https://cucumber.io/
8. GitHub Actions: https://github.com/features/actions
9. Jenkins: https://www.jenkins.io/
10. ESLint: https://eslint.org/ | Rubocop: https://rubocop.org/
11. Swagger/OpenAPI: https://swagger.io/
12. PostgreSQL: https://www.postgresql.org/ | MySQL: https://www.mysql.com/
