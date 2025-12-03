# 7.8 Team Collaboration

Làm việc nhóm hiệu quả với Postman Workspaces, sharing collections, và version control.

## Mục Tiêu

- ✅ Team Workspaces
- ✅ Sharing collections
- ✅ Version control
- ✅ Collaboration workflows

## 1. Postman Workspaces

### Types of Workspaces

**Personal Workspace:**
- Private collections
- Individual testing
- Experimental work
- Personal credentials

**Team Workspace:**
- Shared collections
- Collaboration
- No sensitive data
- Unified environment templates

**Public Workspace:**
- Open-source projects
- Public APIs
- Community sharing

> **📸 HÌNH ẢNH:** Workspace Types
> - File: `workspace-types.png`
> - Nội dung: Screenshot showing 3 workspace types (Personal, Team, Public) với descriptions và access levels

<!-- IMAGE_PLACEHOLDER: workspace-types.png -->

## 2. Creating Team Workspace

### Setup Steps

1. **Create Workspace**
   - Click Workspaces dropdown
   - **Create Workspace**
   - Name: "API Testing Team"
   - Visibility: Team
   - Add team members

2. **Invite Team Members**
   - Click workspace → **Invite**
   - Enter email addresses
   - Set roles: Viewer, Editor, Admin

3. **Share Collections**
   - Move collections to team workspace
   - Set permissions

> **📸 HÌNH ẢNH:** Team Workspace Interface
> - File: `team-workspace-interface.png`
> - Nội dung: Screenshot of team workspace showing shared collections, team members list, và invite button

<!-- IMAGE_PLACEHOLDER: team-workspace-interface.png -->

## 3. Sharing Collections

### Share to Workspace

**Method 1: Move to Workspace**
```
Collection → ... → Move
→ Select Team Workspace
```

**Method 2: Share Link**
```
Collection → Share
→ Get Link
→ Copy and send to team
```

### Permission Levels

**Viewer:**
- ✅ View collections
- ✅ Run requests
- ❌ Cannot edit

**Editor:**
- ✅ View collections
- ✅ Run requests
- ✅ Edit requests
- ✅ Create folders

**Admin:**
- ✅ All Editor permissions
- ✅ Delete collections
- ✅ Manage permissions
- ✅ Workspace settings

## 4. Version Control với Git

### Why Git for Collections?

- ✅ Track changes
- ✅ Rollback capability
- ✅ Collaboration
- ✅ Code review
- ✅ CI/CD integration

### Setup Git Repository

**Structure:**
```
api-tests/
├── .gitignore
├── README.md
├── collections/
│   ├── user-api.postman_collection.json
│   └── product-api.postman_collection.json
├── environments/
│   ├── dev-template.json
│   ├── staging-template.json
│   └── README.md
└── newman/
    ├── run-tests.sh
    └── reports/
```

**.gitignore:**
```gitignore
# Environments với credentials
*environment.json
!*-template.json

# Reports
newman/reports/
*.html

# Logs
*.log

# Sensitive
.env
credentials.json
```

### Workflow

**1. Export Collection:**
```
Collection → Export → Collection v2.1
→ Save to collections/
```

**2. Commit Changes:**
```bash
git add collections/user-api.postman_collection.json
git commit -m "Add user registration endpoint"
git push origin main
```

**3. Pull Latest:**
```bash
git pull origin main
# Import updated collections into Postman
```

## 5. Pull Request Workflow

### Feature Branch Strategy

```bash
# Create feature branch
git checkout -b feature/add-payment-api

# Export collection
# ... (make changes in Postman)

# Export and commit
git add collections/payment-api.postman_collection.json
git commit -m "Add payment API endpoints"
git push origin feature/add-payment-api

# Create PR
gh pr create --title "Add Payment API" --body "Adds endpoints for payment processing"
```

### Review Process

**PR Description Template:**
```markdown
## Changes
- Added 5 new endpoints for payment processing
- Created payment validation tests
- Added error handling tests

## Testing
- [x] All tests pass locally
- [x] Newman run successful
- [x] Tested in Dev environment

## Checklist
- [x] Collection exported
- [x] No credentials committed
- [x] Documentation updated
- [x] Tests added
```

**Review Checklist:**
- [ ] No hardcoded credentials
- [ ] Tests comprehensive
- [ ] Naming conventions followed
- [ ] Documentation clear
- [ ] No sensitive data

## 6. Collaborative Editing

### Real-time Collaboration

**Postman Features:**
- ✅ Multiple users editing
- ✅ See who's online
- ✅ Live updates
- ✅ Conflict resolution

**Best Practices:**
- Communicate before editing
- Use folders to divide work
- Commit frequently
- Pull before editing

### Avoiding Conflicts

**❌ BAD:**
```
Person A: Edits "Login" request
Person B: Edits "Login" request at same time
→ Conflict!
```

**✅ GOOD:**
```
Person A: Works on "Authentication" folder
Person B: Works on "Users" folder
→ No conflict
```

## 7. Documentation for Teams

### Collection README

```markdown
# User API Tests

## Overview
Comprehensive test suite for User Management API.

## Team
- **Owner**: john@company.com
- **Maintainer**: jane@company.com
- **Contributors**: See CONTRIBUTORS.md

## Setup
1. Import collection from `collections/`
2. Copy environment template:
   ```bash
   cp environments/dev-template.json environments/dev-local.json
   ```
3. Fill in your credentials in `dev-local.json`
4. Import environment into Postman

## Running Tests
- **All tests**: Collection Runner
- **Smoke tests**: Run "00-Smoke" folder
- **CI/CD**: `npm run test:newman`

## Contributing
See CONTRIBUTING.md

## Questions?
Contact API Testing Team on Slack: #api-testing
```

### CONTRIBUTING.md

```markdown
# Contributing Guidelines

## Before You Start
1. Join team workspace
2. Set up local environment
3. Read documentation

## Making Changes
1. Create feature branch
2. Make changes in Postman
3. Export collection
4. Write descriptive commit message
5. Push and create PR

## Naming Conventions
- **Requests**: `METHOD Description` (e.g., `GET User by ID`)
- **Folders**: `NN-Category` (e.g., `01-Authentication`)
- **Variables**: camelCase (e.g., `authToken`)

## Testing
All changes must include:
- Status code tests
- Response validation
- Error case handling

## Code Review
- At least 1 approval required
- All tests must pass
- Newman CI check must succeed
```

## 8. Communication

### Slack Integration

**Setup:**
1. Postman → Integrations
2. Add Slack
3. Configure notifications

**Notifications:**
- Collection updates
- Monitor failures
- Test results

**Example:**
```
🔔 Postman Notification
Collection "User API" updated by @john
- Added: POST Create User
- Modified: GET User List
View: https://workspace.postman.com/...
```

### Comments

**In Postman:**
- Click request → Comments tab
- Add comment: "@jane Can you review this validation?"
- Resolve when done

## 9. Roles and Responsibilities

### API Testing Team Structure

**API Test Lead:**
- Workspace admin
- Review all changes
- Maintain standards
- Onboard new members

**QA Engineers:**
- Write test cases
- Execute tests
- Report bugs
- Maintain collections

**Developers:**
- Update collections when API changes
- Fix failing tests
- Collaborate on test design

### Responsibilities Matrix

| Task | Lead | QA | Dev |
|------|------|----|----|
| Define test strategy | ✅ | ✓ | |
| Write tests | ✓ | ✅ | |
| Update on API changes | | ✓ | ✅ |
| Review PRs | ✅ | ✓ | |
| CI/CD setup | ✅ | | ✓ |
| Monitor health | ✅ | ✓ | |

## 10. Onboarding New Team Members

### Onboarding Checklist

**Week 1:**
- [ ] Add to team workspace
- [ ] Grant repository access
- [ ] Share documentation
- [ ] Assign mentor

**Week 2:**
- [ ] Import collections
- [ ] Set up environment
- [ ] Run first test suite
- [ ] Make first contribution

**Week 3:**
- [ ] Add new test case
- [ ] Submit PR
- [ ] Participate in review

### Onboarding Guide

```markdown
# Welcome to API Testing Team!

## Getting Started

### 1. Access
- Postman workspace: [Link]
- GitHub repo: [Link]
- Slack channel: #api-testing

### 2. Tools
- Install Postman
- Install Node.js (for Newman)
- Clone repository

### 3. Setup
```bash
git clone https://github.com/company/api-tests
cd api-tests
npm install
cp environments/dev-template.json environments/dev-local.json
# Edit dev-local.json with your credentials
```

### 4. First Run
- Import collections from `collections/`
- Import your `dev-local.json` environment
- Run "00-Smoke" folder
- All tests should pass ✅

### 5. Your First Task
Create a test for: GET /api/health
- Expected: 200 OK
- Response: `{ "status": "healthy" }`

### Need Help?
- Mentor: @john
- Docs: docs/
- Ask in #api-testing
```

## 11. Best Practices

### ✅ DO

- Use team workspaces for sharing
- Version control with Git
- Document everything
- Communicate changes
- Review before merging
- Use feature branches
- Tag releases
- Maintain changelog
- Onboard properly
- Set clear responsibilities

### ❌ DON'T

- Work in isolation
- Skip documentation
- Commit credentials
- Push directly to main
- Ignore conflicts
- Skip reviews
- Make breaking changes without notice
- Forget to pull latest
- Leave comments unresolved

## 12. Collaboration Tools

### Recommended Stack

**Version Control:**
- Git + GitHub/GitLab

**Communication:**
- Slack/Teams integration
- PR comments
- Postman comments

**CI/CD:**
- GitHub Actions
- GitLab CI
- Jenkins

**Documentation:**
- Postman docs
- Markdown files
- Wiki

**Project Management:**
- Jira integration
- GitHub Projects
- Trello

## Tổng Kết

- ✅ Team workspaces for collaboration
- ✅ Version control với Git
- ✅ PR workflow
- ✅ Clear roles and responsibilities
- ✅ Documentation for onboarding
- ✅ Communication channels
- ✅ Review process

---

[⬅️ Performance](./performance.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: CI/CD ➡️](./cicd-integration.md)
