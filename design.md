# Design Document: AI-Powered Developer Profile Analyzer

## Document Information

**Version:** 1.0  
**Last Updated:** February 15, 2026  
**Status:** Design Phase  
**Owner:** Engineering Team

---

## 1. System Architecture Overview

The AI-Powered Developer Profile Analyzer is designed as a modern, cloud-native web application following a microservices-inspired architecture. The system processes multiple input sources (GitHub repositories, PDF resumes, and role information) through specialized processing engines, leveraging AI for intelligent analysis, and produces comprehensive developer profile reports.

### Architecture Principles

- **Separation of Concerns**: Each component has a single, well-defined responsibility
- **Scalability**: Horizontal scaling capability for compute-intensive operations
- **Modularity**: Loosely coupled components that can be developed and deployed independently
- **Resilience**: Graceful degradation and comprehensive error handling
- **Security-First**: Authentication, authorization, and data encryption at all layers
- **API-Driven**: RESTful API design enabling future integrations and extensions
- **Adaptive Learning**: Personalized learning experiences based on skill gaps and progress

### System Characteristics

- **Type**: Web-based SaaS application with adaptive learning capabilities
- **Deployment**: Cloud-hosted (AWS/GCP/Azure)
- **Architecture Style**: Layered architecture with service-oriented components
- **Communication**: Synchronous REST APIs with asynchronous job processing
- **Data Storage**: Relational database with object storage for files
- **Learning Engine**: AI-powered adaptive learning path generation and tracking

---

## 2. High-Level Architecture Diagram


```
┌─────────────────────────────────────────────────────────────────────────┐
│                              CLIENT LAYER                                │
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │                    Web Application (React)                        │  │
│  │  - Input Forms  - Dashboard  - Report Viewer  - User Profile    │  │
│  │  - Learning Path Viewer  - Progress Tracker  - Skill Dashboard  │  │
│  └──────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ HTTPS/REST
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          API GATEWAY / LOAD BALANCER                     │
│                    (Authentication, Rate Limiting, Routing)              │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┬───────────────┐
                    ▼               ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          APPLICATION LAYER                               │
│                                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐ │
│  │   Auth      │  │   Analysis   │  │   Report    │  │    User      │ │
│  │  Service    │  │   Service    │  │   Service   │  │   Service    │ │
│  └─────────────┘  └──────────────┘  └─────────────┘  └──────────────┘ │
│                                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐ │
│  │  Learning   │  │  Skill Gap   │  │  Progress   │  │   Learning   │ │
│  │    Path     │  │   Analysis   │  │  Tracking   │  │   Content    │ │
│  │  Service    │  │   Service    │  │   Service   │  │   Service    │ │
│  └─────────────┘  └──────────────┘  └─────────────┘  └──────────────┘ │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┬───────────────┐
                    ▼               ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          PROCESSING LAYER                                │
│                                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐ │
│  │   GitHub    │  │    Resume    │  │    Code     │  │      AI      │ │
│  │ Integration │  │    Parser    │  │  Analysis   │  │  Evaluation  │ │
│  │   Engine    │  │    Engine    │  │   Engine    │  │    Engine    │ │
│  └─────────────┘  └──────────────┘  └─────────────┘  └──────────────┘ │
│                                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐ │
│  │  Skill Gap  │  │   Learning   │  │  Adaptive   │  │   Progress   │ │
│  │  Analysis   │  │     Path     │  │  Learning   │  │   Tracking   │ │
│  │   Engine    │  │  Generator   │  │   Engine    │  │    System    │ │
│  └─────────────┘  └──────────────┘  └─────────────┘  └──────────────┘ │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            DATA LAYER                                    │
│                                                                          │
│  ┌─────────────────────┐         ┌─────────────────────────────────┐  │
│  │   PostgreSQL DB     │         │    Object Storage (S3)          │  │
│  │  - Users            │         │  - Resume PDFs                  │  │
│  │  - Analyses         │         │  - Generated Reports            │  │
│  │  - Reports          │         │  - Cached Repository Data       │  │
│  │  - Skills           │         └─────────────────────────────────┘  │
│  └─────────────────────┘                                               │
│                                                                          │
│  ┌─────────────────────┐         ┌─────────────────────────────────┐  │
│  │   Redis Cache       │         │    Job Queue (Redis/RabbitMQ)   │  │
│  │  - Session Data     │         │  - Analysis Jobs                │  │
│  │  - API Responses    │         │  - Report Generation Jobs       │  │
│  └─────────────────────┘         └─────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        EXTERNAL SERVICES                                 │
│                                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐ │
│  │   GitHub    │  │    OpenAI    │  │   Email     │  │  Monitoring  │ │
│  │     API     │  │     API      │  │   Service   │  │   (DataDog)  │ │
│  └─────────────┘  └──────────────┘  └─────────────┘  └──────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
```

### Architecture Flow Description

1. **Client Layer**: React-based web application providing user interface for analysis and learning
2. **API Gateway**: Entry point handling authentication, rate limiting, and routing
3. **Application Layer**: Core business logic services including new learning services
4. **Processing Layer**: Specialized engines including adaptive learning components
5. **Data Layer**: Persistent storage, caching, and job queue management
6. **External Services**: Third-party integrations for extended functionality

**Adaptive Learning Module Integration**:

The Adaptive Learning Module is integrated throughout the architecture:
- **Client Layer**: New UI components for learning paths, progress tracking, and module viewing
- **Application Layer**: Four new services (Learning Path, Skill Gap Analysis, Progress Tracking, Learning Content)
- **Processing Layer**: Four new engines (Skill Gap Analysis, Learning Path Generator, Adaptive Learning, Progress Tracking)
- **Data Layer**: 13 new database tables for comprehensive learning data management

---

## 3. Component Breakdown

### 3.1 Frontend UI (Web Application)

**Technology**: React.js with TypeScript

**Responsibilities**:
- User authentication and session management
- Multi-step form for data collection (GitHub URL, resume upload, role info)
- Real-time progress indicators during analysis
- Interactive dashboard for viewing results
- Report visualization with charts and graphs
- Export functionality for reports
- Responsive design for desktop and tablet

**Key Modules**:
- Authentication Module (Login, Register, OAuth)
- Input Collection Module (Forms, File Upload, Validation)
- Dashboard Module (Analysis History, Quick Stats)
- Report Viewer Module (Interactive Results, Visualizations)
- Profile Management Module (User Settings, Preferences)

**State Management**: Redux or Context API for global state
**UI Framework**: Material-UI or Tailwind CSS
**Charts**: Chart.js or Recharts for visualizations


### 3.2 Backend API

**Technology**: Node.js with Express or Python with FastAPI

**Responsibilities**:
- RESTful API endpoints for all operations
- Request validation and sanitization
- Business logic orchestration
- Authentication and authorization
- Rate limiting and throttling
- Error handling and logging
- Job queue management

**API Endpoints**:

```
Authentication:
POST   /api/v1/auth/register          - User registration
POST   /api/v1/auth/login             - User login
POST   /api/v1/auth/logout            - User logout
POST   /api/v1/auth/refresh           - Refresh access token
GET    /api/v1/auth/github            - GitHub OAuth initiation
GET    /api/v1/auth/github/callback   - GitHub OAuth callback

Analysis:
POST   /api/v1/analysis               - Create new analysis
GET    /api/v1/analysis/:id           - Get analysis by ID
GET    /api/v1/analysis               - List user's analyses
DELETE /api/v1/analysis/:id           - Delete analysis
GET    /api/v1/analysis/:id/status    - Get analysis status

Reports:
GET    /api/v1/reports/:id            - Get report data
GET    /api/v1/reports/:id/download   - Download report PDF
POST   /api/v1/reports/:id/share      - Generate shareable link

User:
GET    /api/v1/users/me               - Get current user profile
PUT    /api/v1/users/me               - Update user profile
DELETE /api/v1/users/me               - Delete user account

Learning Paths:
GET    /api/v1/learning/paths                    - List user's learning paths
POST   /api/v1/learning/paths                    - Create new learning path
GET    /api/v1/learning/paths/:pathId            - Get learning path details
PUT    /api/v1/learning/paths/:pathId            - Update learning path
DELETE /api/v1/learning/paths/:pathId            - Delete learning path
POST   /api/v1/learning/paths/:pathId/start      - Start a learning path
POST   /api/v1/learning/paths/:pathId/pause      - Pause a learning path
POST   /api/v1/learning/paths/:pathId/complete   - Mark path as completed

Skill Gaps:
GET    /api/v1/learning/skill-gaps/:analysisId   - Get skill gaps for analysis
POST   /api/v1/learning/skill-gaps/:analysisId/analyze - Trigger gap analysis

Learning Modules:
GET    /api/v1/learning/paths/:pathId/modules    - List modules in path
GET    /api/v1/learning/modules/:moduleId        - Get module details
POST   /api/v1/learning/modules/:moduleId/start  - Start a module
POST   /api/v1/learning/modules/:moduleId/complete - Complete a module
GET    /api/v1/learning/modules/:moduleId/resources - Get module resources

Learning Activities:
POST   /api/v1/learning/activities               - Record learning activity
GET    /api/v1/learning/activities               - Get user's activities
GET    /api/v1/learning/activities/recent        - Get recent activities

Progress:
GET    /api/v1/learning/progress/:pathId         - Get progress for path
GET    /api/v1/learning/progress/summary         - Get overall progress summary
GET    /api/v1/learning/progress/stats           - Get detailed statistics

Assessments:
GET    /api/v1/learning/assessments/:assessmentId - Get assessment details
POST   /api/v1/learning/assessments/:assessmentId/start - Start assessment
POST   /api/v1/learning/assessments/:assessmentId/submit - Submit assessment
GET    /api/v1/learning/assessments/:assessmentId/results - Get assessment results

Achievements:
GET    /api/v1/learning/achievements             - List all achievements
GET    /api/v1/learning/achievements/user        - Get user's achievements
GET    /api/v1/learning/achievements/:achievementId - Get achievement details

Adaptive Learning:
GET    /api/v1/learning/adaptive/:pathId         - Get adaptive settings
PUT    /api/v1/learning/adaptive/:pathId         - Update adaptive settings
POST   /api/v1/learning/adaptive/:pathId/adjust  - Trigger adaptive adjustment

Recommendations:
GET    /api/v1/learning/recommendations          - Get personalized recommendations
GET    /api/v1/learning/next-steps               - Get suggested next steps

Health:
GET    /api/v1/health                 - Health check endpoint
GET    /api/v1/metrics                - System metrics (admin only)
```

**Request/Response Examples for Learning Module**:

```javascript
// POST /api/v1/learning/paths
// Create a new learning path from analysis
{
  "analysisId": "uuid",
  "targetRole": "Senior Full-Stack Developer",
  "learningStyle": "mixed",
  "availableHoursPerWeek": 10,
  "budgetUsd": 200
}

// Response
{
  "pathId": "uuid",
  "title": "Full-Stack JavaScript Developer Path",
  "estimatedDurationWeeks": 24,
  "totalModules": 27,
  "phases": 4,
  "status": "active"
}

// POST /api/v1/learning/activities
// Record a learning activity
{
  "pathId": "uuid",
  "moduleId": "uuid",
  "resourceId": "uuid",
  "activityType": "resource_completed",
  "durationSeconds": 3600,
  "metadata": {
    "score": 85,
    "notes": "Completed React hooks tutorial"
  }
}

// Response
{
  "activityId": "uuid",
  "progressUpdated": true,
  "milestoneReached": false,
  "achievementsUnlocked": ["first_module_complete"],
  "currentStreak": 5
}

// GET /api/v1/learning/progress/:pathId
// Get progress for a learning path
// Response
{
  "pathId": "uuid",
  "completionPercentage": 45,
  "modulesCompleted": 12,
  "modulesTotal": 27,
  "hoursInvested": 85,
  "estimatedHoursRemaining": 105,
  "currentPhase": 2,
  "averageScore": 78,
  "currentStreak": 14,
  "proficiencyGains": {
    "react": {
      "startLevel": "beginner",
      "currentLevel": "intermediate",
      "progress": 60
    }
  }
}

// POST /api/v1/learning/adaptive/:pathId/adjust
// Trigger adaptive adjustment
// Response
{
  "adjustmentsMade": true,
  "changes": {
    "difficulty": "increased",
    "reason": "High performance on recent assessments",
    "contentChanges": [
      "Added advanced React patterns module",
      "Removed beginner JavaScript review"
    ]
  },
  "recommendations": [
    "Consider skipping to advanced content",
    "Try more challenging projects"
  ]
}
```

**Middleware Stack**:
- CORS handling
- Request logging
- Authentication verification (JWT)
- Rate limiting
- Request validation
- Error handling

### 3.3 GitHub API Integration Engine

**Responsibilities**:
- Fetch repository metadata and statistics
- Clone or download repository contents
- Extract file structure and organization
- Identify programming languages and file types
- Collect commit history and contribution patterns
- Handle API rate limiting
- Cache repository data

**Key Functions**:
- `fetchRepositoryMetadata()` - Get repo info, stars, forks, etc.
- `cloneRepository()` - Download repository contents
- `analyzeFileStructure()` - Parse directory structure
- `detectLanguages()` - Identify programming languages
- `extractCommitHistory()` - Get commit patterns
- `handleRateLimit()` - Manage GitHub API rate limits

**GitHub API Endpoints Used**:
- GET /repos/{owner}/{repo}
- GET /repos/{owner}/{repo}/languages
- GET /repos/{owner}/{repo}/contents
- GET /repos/{owner}/{repo}/commits
- GET /repos/{owner}/{repo}/contributors

**Error Handling**:
- Invalid repository URL
- Private repository without access
- Repository too large
- API rate limit exceeded
- Network timeouts

### 3.4 Resume Parser Engine

**Technology**: pdf-parse (Node.js) or PyPDF2 (Python)

**Responsibilities**:
- Extract text content from PDF files
- Parse structured information (education, experience, skills)
- Identify contact information
- Extract dates and durations
- Detect certifications and achievements
- Handle various resume formats

**Key Functions**:
- `extractTextFromPDF()` - Convert PDF to text
- `parseEducation()` - Extract education details
- `parseWorkExperience()` - Extract job history
- `extractSkills()` - Identify technical skills
- `parseCertifications()` - Find certifications
- `calculateExperience()` - Compute total years of experience

**Parsing Strategy**:
- Regular expressions for pattern matching
- NLP for entity recognition (optional enhancement)
- Section detection (Education, Experience, Skills)
- Date parsing and normalization
- Skill keyword extraction

**Supported Resume Formats**:
- Standard chronological resumes
- Functional resumes
- Combination resumes
- Single and multi-column layouts

### 3.5 Code Analysis Engine

**Responsibilities**:
- Analyze code quality metrics
- Detect code smells and anti-patterns
- Evaluate code complexity
- Assess documentation quality
- Identify architectural patterns
- Measure test coverage
- Detect technology stack

**Key Modules**:

#### Code Quality Analyzer
- Cyclomatic complexity calculation
- Code duplication detection
- Naming convention analysis
- Code organization assessment
- Comment density evaluation

#### Technology Stack Detector
- Language identification
- Framework detection (React, Django, Spring, etc.)
- Library and dependency analysis
- Build tool identification
- Database technology detection

#### Architecture Pattern Recognizer
- MVC pattern detection
- Microservices architecture identification
- Monolithic structure analysis
- API design evaluation
- Design pattern usage (Singleton, Factory, Observer, etc.)

**Tools and Libraries**:
- ESLint/Pylint for code quality
- SonarQube metrics
- Language-specific AST parsers
- Dependency analyzers (npm, pip, maven)

**Metrics Collected**:
- Lines of code (LOC)
- Cyclomatic complexity
- Maintainability index
- Code duplication percentage
- Comment ratio
- Test coverage percentage
- Number of dependencies
- Code organization score


### 3.6 AI Evaluation Engine

**Technology**: OpenAI API (GPT-4) or similar LLM

**Responsibilities**:
- Synthesize insights from all data sources
- Evaluate skill alignment between code and resume
- Generate proficiency level assessments
- Create personalized recommendations
- Identify skill gaps and learning opportunities
- Produce natural language summaries

**Key Functions**:

#### Skill Alignment Evaluator
```javascript
evaluateSkillAlignment(codeSkills, resumeSkills, roleInfo) {
  // Compare claimed skills vs demonstrated skills
  // Identify discrepancies
  // Assess skill authenticity
  // Return alignment score and insights
}
```

#### Proficiency Assessor
```javascript
assessProficiency(codeMetrics, experienceYears, projectComplexity) {
  // Analyze code quality indicators
  // Consider experience duration
  // Evaluate project sophistication
  // Return proficiency level (beginner/intermediate/advanced)
}
```

#### Recommendation Generator
```javascript
generateRecommendations(skillGaps, careerGoals, currentLevel) {
  // Identify priority learning areas
  // Suggest specific technologies
  // Recommend courses and resources
  // Prioritize by impact and feasibility
}
```

**AI Prompts Structure**:
- System prompt with role definition
- Context injection (code metrics, resume data, role info)
- Structured output format (JSON)
- Temperature and token limits
- Retry logic for API failures

**Sample AI Prompt**:
```
You are an expert technical recruiter and software engineering mentor.
Analyze the following developer profile:

Code Analysis: {code_metrics}
Resume Data: {resume_data}
Current Role: {role_info}

Provide:
1. Skill alignment score (0-100)
2. Proficiency levels for each technology
3. Top 5 improvement recommendations
4. Career development insights

Format response as JSON.
```

### 3.7 Report Generator

**Technology**: PDFKit (Node.js) or ReportLab (Python)

**Responsibilities**:
- Compile analysis results into structured report
- Generate PDF documents with professional formatting
- Create visualizations and charts
- Produce JSON export for programmatic access
- Generate shareable report links
- Apply branding and styling

**Report Structure**:

```
1. Executive Summary
   - Overall score
   - Key strengths
   - Primary recommendations

2. Code Quality Analysis
   - Quality metrics
   - Code organization
   - Best practices adherence

3. Technology Stack
   - Languages and frameworks
   - Tools and libraries
   - Technology proficiency matrix

4. Architecture Assessment
   - Patterns identified
   - Design quality
   - Scalability considerations

5. Resume Analysis
   - Experience summary
   - Education and certifications
   - Career progression

6. Skill Alignment
   - Code vs resume comparison
   - Skill authenticity assessment
   - Proficiency levels

7. Recommendations
   - Learning priorities
   - Skill development path
   - Resource suggestions

8. Appendix
   - Detailed metrics
   - Methodology
   - Glossary
```

**Output Formats**:
- PDF (primary format)
- JSON (for API consumers)
- HTML (for web preview)

**Visualization Components**:
- Skill radar chart
- Technology distribution pie chart
- Code quality bar chart
- Experience timeline
- Proficiency matrix heatmap

### 3.8 Adaptive Learning Module

The Adaptive Learning Module is a comprehensive system that identifies skill gaps, generates personalized learning paths, adapts content difficulty based on user experience, and tracks progress over time. This module transforms the platform from a one-time analysis tool into a continuous learning companion.

#### 3.8.1 Skill Gap Analysis Engine

**Responsibilities**:
- Identify missing skills based on career goals and market demand
- Quantify skill gaps with severity scores
- Prioritize gaps based on impact and feasibility
- Compare user skills against industry benchmarks
- Detect outdated skills requiring updates
- Identify complementary skills for career advancement

**Key Functions**:

```javascript
identifySkillGaps(userSkills, targetRole, industryBenchmarks) {
  // Compare current skills with target role requirements
  // Analyze market demand for missing skills
  // Calculate gap severity (critical, high, medium, low)
  // Return prioritized list of skill gaps
}

calculateGapSeverity(skill, targetRole, marketDemand) {
  // Factors: role requirement, market demand, learning difficulty
  // Return severity score (0-100)
}

detectOutdatedSkills(userSkills, technologyTrends) {
  // Identify deprecated or declining technologies
  // Suggest modern alternatives
  // Return list of skills needing updates
}

findComplementarySkills(currentSkills, careerPath) {
  // Identify skills that enhance existing capabilities
  // Suggest skill combinations for specialization
  // Return recommended complementary skills
}
```

**Gap Analysis Process**:

```
1. COLLECT INPUT DATA
   ├─ User's current skills (from code analysis + resume)
   ├─ Target role or career goal
   ├─ Years of experience
   └─ Industry/domain preference

2. FETCH BENCHMARK DATA
   ├─ Role requirements from job market data
   ├─ Industry skill standards
   ├─ Technology trend data
   └─ Salary correlation data

3. PERFORM GAP ANALYSIS
   ├─ Missing skills identification
   ├─ Proficiency level gaps
   ├─ Outdated technology detection
   └─ Complementary skill opportunities

4. PRIORITIZE GAPS
   ├─ Calculate impact score (career advancement potential)
   ├─ Assess learning difficulty
   ├─ Consider time investment required
   └─ Factor in market demand

5. GENERATE GAP REPORT
   ├─ Critical gaps (immediate attention)
   ├─ High-priority gaps (short-term goals)
   ├─ Medium-priority gaps (medium-term goals)
   └─ Low-priority gaps (long-term goals)
```

**Gap Severity Calculation**:

```javascript
severityScore = (
  roleRequirement * 0.4 +      // How essential for target role
  marketDemand * 0.3 +          // Current market demand
  careerImpact * 0.2 +          // Impact on career progression
  urgency * 0.1                 // Time sensitivity
) * 100
```

**Data Sources**:
- Internal skill database
- Job market APIs (LinkedIn, Indeed, Glassdoor)
- Technology trend data (Stack Overflow, GitHub)
- Industry reports and surveys

#### 3.8.2 Learning Path Generator

**Responsibilities**:
- Create personalized learning roadmaps
- Sequence learning topics logically
- Estimate time requirements for each topic
- Recommend specific resources (courses, books, projects)
- Generate milestone-based learning plans
- Adapt paths based on learning style preferences

**Key Functions**:

```javascript
generateLearningPath(skillGaps, userProfile, preferences) {
  // Create structured learning roadmap
  // Sequence topics with prerequisites
  // Assign time estimates
  // Recommend resources
  // Return complete learning path
}

sequenceTopics(topics, prerequisites) {
  // Build dependency graph
  // Perform topological sort
  // Group parallel learning opportunities
  // Return ordered topic sequence
}

recommendResources(topic, learningStyle, budget) {
  // Match resources to learning preferences
  // Consider budget constraints
  // Prioritize by quality and relevance
  // Return ranked resource list
}

estimateLearningTime(topic, currentLevel, targetLevel) {
  // Calculate hours needed based on difficulty
  // Adjust for user's learning pace
  // Return time estimate in hours
}
```

**Learning Path Structure**:

```javascript
{
  "pathId": "uuid",
  "userId": "uuid",
  "title": "Full-Stack JavaScript Developer Path",
  "description": "Comprehensive path to master full-stack development",
  "estimatedDuration": "6 months",
  "difficulty": "intermediate",
  "phases": [
    {
      "phaseNumber": 1,
      "title": "Frontend Fundamentals",
      "duration": "8 weeks",
      "modules": [
        {
          "moduleId": "uuid",
          "title": "Advanced React Patterns",
          "description": "Master React hooks, context, and performance",
          "difficulty": "intermediate",
          "estimatedHours": 40,
          "prerequisites": ["react-basics"],
          "learningObjectives": [
            "Understand React hooks in depth",
            "Implement custom hooks",
            "Optimize component performance"
          ],
          "resources": [
            {
              "type": "course",
              "title": "Advanced React Course",
              "provider": "Udemy",
              "url": "https://...",
              "duration": "20 hours",
              "cost": "$49.99",
              "rating": 4.7
            },
            {
              "type": "documentation",
              "title": "React Official Docs",
              "url": "https://react.dev",
              "cost": "free"
            },
            {
              "type": "project",
              "title": "Build a Real-time Dashboard",
              "description": "Apply React patterns in a practical project",
              "estimatedHours": 20
            }
          ],
          "assessments": [
            {
              "type": "quiz",
              "title": "React Hooks Quiz",
              "questions": 20
            },
            {
              "type": "project",
              "title": "Component Library",
              "description": "Build reusable components"
            }
          ]
        }
      ]
    }
  ],
  "milestones": [
    {
      "title": "Complete Frontend Phase",
      "targetDate": "2026-04-15",
      "criteria": ["Complete all modules", "Pass assessments"]
    }
  ]
}
```

**Path Generation Algorithm**:

```
1. ANALYZE SKILL GAPS
   └─ Identify target skills and proficiency levels

2. BUILD TOPIC GRAPH
   ├─ Map all required topics
   ├─ Identify prerequisites
   └─ Create dependency relationships

3. SEQUENCE LEARNING
   ├─ Topological sort based on prerequisites
   ├─ Group parallel learning opportunities
   └─ Balance difficulty progression

4. ASSIGN RESOURCES
   ├─ Match learning style preferences
   ├─ Consider budget constraints
   ├─ Prioritize quality and relevance
   └─ Include diverse resource types

5. ESTIMATE TIMELINE
   ├─ Calculate hours per topic
   ├─ Factor in user's available time
   └─ Set realistic milestones

6. GENERATE PATH DOCUMENT
   └─ Create structured learning roadmap
```

#### 3.8.3 Adaptive Learning Engine

**Responsibilities**:
- Adjust difficulty based on user performance
- Personalize content recommendations
- Optimize learning pace
- Identify struggling areas and provide support
- Celebrate achievements and maintain motivation
- Predict learning outcomes

**Key Functions**:

```javascript
adaptDifficulty(userProgress, performanceMetrics) {
  // Analyze completion rates and assessment scores
  // Adjust content difficulty dynamically
  // Return difficulty adjustment recommendations
}

personalizeContent(learningStyle, preferences, performance) {
  // Match content to learning preferences
  // Adjust based on what's working
  // Return personalized content recommendations
}

optimizePace(completionRate, timeSpent, comprehension) {
  // Analyze learning velocity
  // Detect if user is rushing or struggling
  // Recommend pace adjustments
}

detectStrugglingAreas(assessmentResults, timeMetrics) {
  // Identify topics with low comprehension
  // Detect patterns in mistakes
  // Return areas needing additional support
}

predictOutcome(currentProgress, historicalData) {
  // Estimate completion probability
  // Predict proficiency level at completion
  // Return outcome predictions
}
```

**Adaptation Mechanisms**:

**1. Difficulty Adaptation**:
```javascript
// If user consistently scores high (>85%)
if (averageScore > 85 && completionRate > 90) {
  action = "increase_difficulty";
  recommendation = "Skip beginner content, move to advanced";
}

// If user is struggling (<60%)
if (averageScore < 60 || completionRate < 50) {
  action = "decrease_difficulty";
  recommendation = "Add foundational content, slow pace";
}

// If user is progressing well (60-85%)
if (averageScore >= 60 && averageScore <= 85) {
  action = "maintain_difficulty";
  recommendation = "Continue current path";
}
```

**2. Content Personalization**:
```javascript
// Learning style preferences
const contentPreferences = {
  visual: ["video_tutorials", "infographics", "diagrams"],
  reading: ["documentation", "articles", "books"],
  practical: ["projects", "coding_challenges", "labs"],
  interactive: ["interactive_tutorials", "quizzes", "games"]
};

// Adjust content mix based on engagement
if (videoCompletionRate > textCompletionRate) {
  increaseContentType("video");
  decreaseContentType("text");
}
```

**3. Pace Optimization**:
```javascript
// Calculate optimal pace
const optimalPace = {
  hoursPerWeek: calculateAvailableTime(userSchedule),
  modulesPerWeek: Math.floor(hoursPerWeek / averageModuleDuration),
  adjustmentFactor: comprehensionScore / 100
};

// Adjust if user is burning out or bored
if (sessionDuration < 15 && frequency < 2) {
  recommendation = "reduce_pace";
} else if (sessionDuration > 60 && frequency > 5) {
  recommendation = "increase_pace";
}
```

**4. Intervention Triggers**:
```javascript
// Trigger support interventions
const interventions = {
  low_comprehension: {
    trigger: "assessment_score < 60",
    action: "provide_additional_resources",
    resources: ["simplified_explanations", "video_tutorials", "mentor_session"]
  },
  low_engagement: {
    trigger: "days_inactive > 7",
    action: "send_motivation_message",
    content: "personalized_encouragement"
  },
  rapid_progress: {
    trigger: "completion_rate > 150% of estimate",
    action: "offer_advanced_content",
    content: "challenge_projects"
  }
};
```

#### 3.8.4 Progress Tracking System

**Responsibilities**:
- Record learning activities and completions
- Track time spent on each topic
- Store assessment results
- Calculate progress percentages
- Generate progress reports
- Maintain learning streaks and achievements
- Provide analytics and insights

**Key Functions**:

```javascript
recordActivity(userId, activityType, activityData) {
  // Log learning activity with timestamp
  // Update progress metrics
  // Check for milestone completion
}

calculateProgress(userId, pathId) {
  // Compute completion percentage
  // Calculate time invested
  // Assess proficiency gains
  // Return progress summary
}

generateProgressReport(userId, timeframe) {
  // Compile activity data
  // Calculate metrics and trends
  // Generate visualizations
  // Return comprehensive report
}

trackStreak(userId) {
  // Monitor consecutive learning days
  // Update streak counter
  // Award streak achievements
}

awardAchievement(userId, achievementType) {
  // Unlock achievement badge
  // Update user profile
  // Send notification
}
```

**Progress Metrics**:

```javascript
{
  "userId": "uuid",
  "pathId": "uuid",
  "overallProgress": {
    "completionPercentage": 45,
    "modulesCompleted": 12,
    "modulesTotal": 27,
    "hoursInvested": 85,
    "estimatedHoursRemaining": 105,
    "currentPhase": 2,
    "totalPhases": 4
  },
  "proficiencyGains": {
    "react": {
      "startLevel": "beginner",
      "currentLevel": "intermediate",
      "targetLevel": "advanced",
      "progress": 60
    },
    "nodejs": {
      "startLevel": "none",
      "currentLevel": "beginner",
      "targetLevel": "intermediate",
      "progress": 30
    }
  },
  "assessmentScores": {
    "averageScore": 78,
    "highestScore": 95,
    "lowestScore": 62,
    "trend": "improving"
  },
  "timeMetrics": {
    "totalHours": 85,
    "averageHoursPerWeek": 12,
    "longestSession": 4.5,
    "averageSessionDuration": 1.8
  },
  "engagement": {
    "currentStreak": 14,
    "longestStreak": 21,
    "activeDays": 42,
    "lastActivity": "2026-02-15T10:30:00Z"
  },
  "achievements": [
    {
      "id": "first_module",
      "title": "First Steps",
      "unlockedAt": "2026-01-05T14:20:00Z"
    },
    {
      "id": "week_streak",
      "title": "Week Warrior",
      "unlockedAt": "2026-01-12T09:15:00Z"
    }
  ]
}
```

**Activity Tracking**:

```javascript
// Activity types tracked
const activityTypes = {
  MODULE_STARTED: "module_started",
  MODULE_COMPLETED: "module_completed",
  RESOURCE_VIEWED: "resource_viewed",
  RESOURCE_COMPLETED: "resource_completed",
  ASSESSMENT_STARTED: "assessment_started",
  ASSESSMENT_COMPLETED: "assessment_completed",
  PROJECT_STARTED: "project_started",
  PROJECT_COMPLETED: "project_completed",
  NOTE_CREATED: "note_created",
  QUESTION_ASKED: "question_asked"
};

// Activity record structure
{
  "activityId": "uuid",
  "userId": "uuid",
  "pathId": "uuid",
  "moduleId": "uuid",
  "activityType": "module_completed",
  "timestamp": "2026-02-15T10:30:00Z",
  "duration": 3600, // seconds
  "metadata": {
    "score": 85,
    "attempts": 1,
    "resourcesUsed": ["video", "documentation"]
  }
}
```

### 3.9 Adaptive Learning Architecture Extension

This section describes the architectural extension for the Adaptive Learning Module, following a layered approach: Input → Intelligence → Output. This extension integrates seamlessly with the existing analysis pipeline while maintaining clear separation of concerns.

#### Architecture Overview: Three-Layer Design

```
┌─────────────────────────────────────────────────────────────────────┐
│                          INPUT LAYER                                 │
│                                                                      │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────────┐   │
│  │  Analysis      │  │  User Role     │  │  User Preferences  │   │
│  │  Results       │  │  Information   │  │  (Time, Budget)    │   │
│  │  (Skills,      │  │  (Target Role, │  │                    │   │
│  │   Code Metrics)│  │   Experience)  │  │                    │   │
│  └────────┬───────┘  └────────┬───────┘  └────────┬───────────┘   │
└───────────┼──────────────────┼──────────────────┼─────────────────┘
            │                  │                  │
            └──────────────────┼──────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      INTELLIGENCE LAYER                              │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  1. Skill Gap Analyzer                                       │  │
│  │     - Categorizes gaps: Beginner/Intermediate/Advanced       │  │
│  │     - Assigns priority scores                                │  │
│  │     - Estimates learning hours                               │  │
│  └────────────────────────┬─────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  2. Diagnostic Quiz Engine                                   │  │
│  │     - Generates 5-10 questions per skill gap                 │  │
│  │     - Calculates quiz score (0-100%)                         │  │
│  │     - Identifies weak areas                                  │  │
│  └────────────────────────┬─────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  3. Learner Classification Engine (Rule-Based)               │  │
│  │     - Input: Quiz Score + Code Complexity Score              │  │
│  │     - Deterministic Rules:                                   │  │
│  │       • Quiz ≥75% & Complexity ≥70 → Fast-Paced Advanced    │  │
│  │       • Quiz ≥75% & Complexity <70 → Conceptual Deep        │  │
│  │       • Quiz <75% & Complexity ≥70 → Practice-Oriented      │  │
│  │       • Quiz <75% & Complexity <70 → Guided Step-by-Step    │  │
│  │     - Output: Learner Type                                   │  │
│  └────────────────────────┬─────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  4. Adaptive Pace Engine (Rule-Based)                        │  │
│  │     - Adjusts based on Learner Type:                         │  │
│  │       • Content Depth (basic → advanced)                     │  │
│  │       • Progression Speed (0.75x → 1.5x)                     │  │
│  │       • Difficulty Curve (gentle → steep)                    │  │
│  │     - Deterministic mapping (no ML)                          │  │
│  └────────────────────────┬─────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  5. Learning Path Generator                                  │  │
│  │     - Sequences modules by priority & prerequisites          │  │
│  │     - Matches resources to learner type                      │  │
│  │     - Estimates timeline based on available hours            │  │
│  │     - Creates phased roadmap (4 phases)                      │  │
│  └────────────────────────┬─────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  6. Learning Progress Store                                  │  │
│  │     - Stores: Modules completed, Time invested               │  │
│  │     - Calculates: Completion %, Progress metrics             │  │
│  │     - Tracks: Quiz scores, Resource usage                    │  │
│  └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────┬───────────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         OUTPUT LAYER                                 │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  7. Personalized Learning Roadmap                            │  │
│  │     - Executive Summary (Learner Type, Duration)             │  │
│  │     - Detailed Learning Path (Phases, Modules, Resources)    │  │
│  │     - Timeline & Milestones                                  │  │
│  │     - Adaptive Settings (Pace, Depth, Difficulty)            │  │
│  │     - Next Steps & Action Items                              │  │
│  │     - Export: PDF, JSON, Web View                            │  │
│  └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

#### Data Flow: Analysis to Learning Roadmap

```
Step 1: INPUT COLLECTION
┌─────────────────────────────────────────┐
│ Analysis Complete                       │
│ - Skills identified                     │
│ - Code complexity calculated            │
│ - Resume parsed                         │
│ - Role requirements mapped              │
└──────────────┬──────────────────────────┘
               ▼
Step 2: SKILL GAP ANALYSIS
┌─────────────────────────────────────────┐
│ Skill Gap Analyzer                      │
│                                         │
│ FOR EACH skill in target_role:          │
│   IF skill NOT IN user_skills:          │
│     gap_level = "Beginner"              │
│   ELSE IF skill_proficiency < required: │
│     gap_level = "Intermediate/Advanced" │
│   END IF                                │
│                                         │
│   priority = calculate_priority(        │
│     role_importance,                    │
│     market_demand,                      │
│     career_impact                       │
│   )                                     │
│ END FOR                                 │
│                                         │
│ Output: List of SkillGap objects        │
└──────────────┬──────────────────────────┘
               ▼
Step 3: DIAGNOSTIC ASSESSMENT
┌─────────────────────────────────────────┐
│ Diagnostic Quiz Engine                  │
│                                         │
│ FOR EACH skill_gap:                     │
│   questions = generate_quiz(            │
│     skill_name,                         │
│     gap_level,                          │
│     question_count = 5-10               │
│   )                                     │
│   user_answers = collect_responses()    │
│   score = calculate_score(answers)      │
│   store_quiz_result(skill, score)       │
│ END FOR                                 │
│                                         │
│ avg_quiz_score = average(all_scores)    │
│                                         │
│ Output: QuizResult objects              │
└──────────────┬──────────────────────────┘
               ▼
Step 4: LEARNER CLASSIFICATION (RULE-BASED)
┌─────────────────────────────────────────┐
│ Learner Classification Engine           │
│                                         │
│ INPUT:                                  │
│   quiz_score = avg_quiz_score           │
│   complexity = code_complexity_score    │
│                                         │
│ CLASSIFICATION LOGIC:                   │
│ IF quiz_score >= 75 AND complexity >= 70│
│   learner_type = "Fast-Paced Advanced"  │
│   content_depth = "high_level"          │
│   progression_speed = 1.5               │
│   difficulty_curve = "steep"            │
│                                         │
│ ELSE IF quiz_score >= 75 AND            │
│         complexity < 70                 │
│   learner_type = "Conceptual Deep"      │
│   content_depth = "comprehensive"       │
│   progression_speed = 1.0               │
│   difficulty_curve = "gradual"          │
│                                         │
│ ELSE IF quiz_score < 75 AND             │
│         complexity >= 70                │
│   learner_type = "Practice-Oriented"    │
│   content_depth = "practical"           │
│   progression_speed = 1.0               │
│   difficulty_curve = "moderate"         │
│                                         │
│ ELSE                                    │
│   learner_type = "Guided Step-by-Step"  │
│   content_depth = "detailed"            │
│   progression_speed = 0.75              │
│   difficulty_curve = "gentle"           │
│ END IF                                  │
│                                         │
│ Output: LearnerType object              │
└──────────────┬──────────────────────────┘
               ▼
Step 5: ADAPTIVE PACE CONFIGURATION
┌─────────────────────────────────────────┐
│ Adaptive Pace Engine                    │
│                                         │
│ BASED ON learner_type:                  │
│                                         │
│ pace_config = {                         │
│   content_depth: map_content_depth(),   │
│   progression_speed: get_speed_factor(),│
│   difficulty_curve: get_curve_type(),   │
│   resource_types: match_resources(),    │
│   assessment_frequency: set_frequency() │
│ }                                       │
│                                         │
│ EXAMPLE (Fast-Paced Advanced):          │
│ {                                       │
│   content_depth: "skip_basics",         │
│   progression_speed: 1.5,               │
│   difficulty_curve: "steep",            │
│   resource_types: ["advanced_courses",  │
│                     "documentation"],   │
│   assessment_frequency: "low"           │
│ }                                       │
│                                         │
│ Output: PaceConfiguration object        │
└──────────────┬──────────────────────────┘
               ▼
Step 6: LEARNING PATH GENERATION
┌─────────────────────────────────────────┐
│ Learning Path Generator                 │
│                                         │
│ 1. PRIORITIZE GAPS                      │
│    sorted_gaps = sort_by_priority(gaps) │
│                                         │
│ 2. BUILD DEPENDENCY GRAPH               │
│    graph = create_prerequisite_graph()  │
│                                         │
│ 3. SEQUENCE MODULES                     │
│    phases = [                           │
│      Phase 1: Critical gaps             │
│      Phase 2: High-priority gaps        │
│      Phase 3: Medium-priority gaps      │
│      Phase 4: Advanced/optional         │
│    ]                                    │
│                                         │
│ 4. ASSIGN RESOURCES                     │
│    FOR EACH module:                     │
│      resources = match_to_learner_type( │
│        module,                          │
│        learner_type,                    │
│        pace_config                      │
│      )                                  │
│    END FOR                              │
│                                         │
│ 5. ESTIMATE TIMELINE                    │
│    total_hours = sum(module_hours)      │
│    duration_weeks = total_hours /       │
│                     hours_per_week      │
│                                         │
│ 6. SET MILESTONES                       │
│    milestones = create_checkpoints(     │
│      phases,                            │
│      duration_weeks                     │
│    )                                    │
│                                         │
│ Output: LearningRoadmap object          │
└──────────────┬──────────────────────────┘
               ▼
Step 7: ROADMAP OUTPUT
┌─────────────────────────────────────────┐
│ Personalized Learning Roadmap           │
│                                         │
│ STRUCTURE:                              │
│ {                                       │
│   executive_summary: {                  │
│     learner_type,                       │
│     total_duration,                     │
│     key_skills                          │
│   },                                    │
│   learning_path: {                      │
│     phases: [                           │
│       {                                 │
│         phase_number,                   │
│         modules: [                      │
│           {                             │
│             title,                      │
│             resources,                  │
│             assessments                 │
│           }                             │
│         ]                               │
│       }                                 │
│     ]                                   │
│   },                                    │
│   timeline: {                           │
│     milestones,                         │
│     target_dates                        │
│   },                                    │
│   adaptive_settings: {                  │
│     pace_config                         │
│   }                                     │
│ }                                       │
│                                         │
│ EXPORT FORMATS:                         │
│ - PDF (formatted document)              │
│ - JSON (programmatic access)            │
│ - Web View (interactive)                │
└─────────────────────────────────────────┘
```

#### Rule-Based Learner Classification Logic

**Why Rule-Based Instead of ML?**

The system uses deterministic rule-based classification rather than machine learning for several strategic reasons:

1. **Transparency**: Users can understand exactly why they were classified into a specific learner type
2. **Predictability**: Same inputs always produce same outputs (no model drift)
3. **Maintainability**: Rules can be easily updated without retraining
4. **MVP Feasibility**: No need for training data collection or model training infrastructure
5. **Debugging**: Easy to trace and fix classification issues
6. **Compliance**: Clear audit trail for classification decisions

**Classification Algorithm**:

```javascript
function classifyLearner(quizScore, codeComplexityScore) {
  // Thresholds
  const QUIZ_THRESHOLD = 75;
  const COMPLEXITY_THRESHOLD = 70;
  
  // Deterministic classification
  if (quizScore >= QUIZ_THRESHOLD && codeComplexityScore >= COMPLEXITY_THRESHOLD) {
    return {
      type: "Fast-Paced Advanced",
      characteristics: {
        theoreticalKnowledge: "strong",
        practicalSkills: "strong",
        learningSpeed: "fast"
      },
      adaptations: {
        contentDepth: "high_level_overview",
        progressionSpeed: 1.5,
        difficultyCurve: "steep",
        resourceTypes: ["advanced_courses", "documentation", "research_papers"],
        assessmentFrequency: "low"
      }
    };
  }
  
  if (quizScore >= QUIZ_THRESHOLD && codeComplexityScore < COMPLEXITY_THRESHOLD) {
    return {
      type: "Conceptual Deep Learner",
      characteristics: {
        theoreticalKnowledge: "strong",
        practicalSkills: "developing",
        learningSpeed: "moderate"
      },
      adaptations: {
        contentDepth: "comprehensive_theory",
        progressionSpeed: 1.0,
        difficultyCurve: "gradual_conceptual",
        resourceTypes: ["books", "long_articles", "video_lectures"],
        assessmentFrequency: "moderate"
      }
    };
  }
  
  if (quizScore < QUIZ_THRESHOLD && codeComplexityScore >= COMPLEXITY_THRESHOLD) {
    return {
      type: "Practice-Oriented Learner",
      characteristics: {
        theoreticalKnowledge: "developing",
        practicalSkills: "strong",
        learningSpeed: "moderate"
      },
      adaptations: {
        contentDepth: "practical_focus",
        progressionSpeed: 1.0,
        difficultyCurve: "moderate_practical",
        resourceTypes: ["coding_challenges", "project_tutorials", "labs"],
        assessmentFrequency: "high"
      }
    };
  }
  
  // Default: Guided Step-by-Step
  return {
    type: "Guided Step-by-Step Learner",
    characteristics: {
      theoreticalKnowledge: "developing",
      practicalSkills: "developing",
      learningSpeed: "careful"
    },
    adaptations: {
      contentDepth: "detailed_step_by_step",
      progressionSpeed: 0.75,
      difficultyCurve: "gentle_incremental",
      resourceTypes: ["interactive_tutorials", "step_by_step_guides", "beginner_courses"],
      assessmentFrequency: "high"
    }
  };
}
```

**Deterministic Adaptation Behavior**:

```javascript
// Pace adaptation mapping (deterministic)
const PACE_ADAPTATIONS = {
  "Fast-Paced Advanced": {
    moduleSequencing: "parallel_tracks_allowed",
    skipBasics: true,
    advancedContentFirst: true,
    checkpointInterval: "per_phase",
    supportLevel: "minimal"
  },
  "Conceptual Deep Learner": {
    moduleSequencing: "sequential_with_deep_dives",
    skipBasics: false,
    advancedContentFirst: false,
    checkpointInterval: "per_module",
    supportLevel: "moderate"
  },
  "Practice-Oriented Learner": {
    moduleSequencing: "project_based_progression",
    skipBasics: false,
    advancedContentFirst: false,
    checkpointInterval: "per_project",
    supportLevel: "moderate"
  },
  "Guided Step-by-Step Learner": {
    moduleSequencing: "strict_sequential",
    skipBasics: false,
    advancedContentFirst: false,
    checkpointInterval: "frequent",
    supportLevel: "high"
  }
};

function applyAdaptations(learnerType, learningPath) {
  const adaptations = PACE_ADAPTATIONS[learnerType];
  
  // Apply deterministic transformations
  learningPath.modules = sequenceModules(
    learningPath.modules,
    adaptations.moduleSequencing
  );
  
  if (adaptations.skipBasics) {
    learningPath.modules = filterBasicModules(learningPath.modules);
  }
  
  learningPath.checkpoints = generateCheckpoints(
    learningPath.modules,
    adaptations.checkpointInterval
  );
  
  return learningPath;
}
```

#### Data Model Updates

**SkillGap Model**:
```javascript
{
  id: "uuid",
  analysisId: "uuid",
  skillName: "React Hooks",
  currentLevel: "beginner",      // none, beginner, intermediate, advanced
  targetLevel: "advanced",
  gapCategory: "Intermediate",   // Beginner, Intermediate, Advanced
  priority: "high",              // critical, high, medium, low
  priorityScore: 85,             // 0-100
  marketDemandScore: 90,
  careerImpactScore: 80,
  learningDifficulty: "moderate", // easy, moderate, hard, very_hard
  estimatedHours: 40,
  reason: "Required for target role as Senior Frontend Developer"
}
```

**QuizResult Model**:
```javascript
{
  id: "uuid",
  userId: "uuid",
  skillGapId: "uuid",
  skillName: "React Hooks",
  questionsTotal: 10,
  questionsCorrect: 6,
  score: 60,                     // percentage
  timeSpentSeconds: 420,
  weakAreas: ["useEffect dependencies", "custom hooks"],
  attemptNumber: 1,
  completedAt: "2026-02-15T10:30:00Z"
}
```

**LearnerType Model**:
```javascript
{
  id: "uuid",
  userId: "uuid",
  analysisId: "uuid",
  learnerType: "Practice-Oriented Learner",
  quizScore: 68,                 // average across all quizzes
  codeComplexityScore: 75,
  classificationReason: "Quiz score < 75 AND code complexity >= 70",
  characteristics: {
    theoreticalKnowledge: "developing",
    practicalSkills: "strong",
    learningSpeed: "moderate"
  },
  adaptations: {
    contentDepth: "practical_focus",
    progressionSpeed: 1.0,
    difficultyCurve: "moderate_practical",
    resourceTypes: ["coding_challenges", "project_tutorials", "labs"],
    assessmentFrequency: "high"
  },
  classifiedAt: "2026-02-15T10:35:00Z",
  manualOverride: false
}
```

**LearningRoadmap Model**:
```javascript
{
  id: "uuid",
  userId: "uuid",
  analysisId: "uuid",
  learnerTypeId: "uuid",
  title: "Full-Stack JavaScript Developer Path",
  description: "Personalized path to bridge skill gaps",
  estimatedDurationWeeks: 24,
  estimatedHours: 240,
  targetRole: "Senior Full-Stack Developer",
  phases: [
    {
      phaseNumber: 1,
      title: "Critical Skills - Frontend Fundamentals",
      durationWeeks: 8,
      modules: [
        {
          moduleId: "uuid",
          title: "Advanced React Patterns",
          skillGapId: "uuid",
          estimatedHours: 40,
          difficulty: "intermediate",
          prerequisites: ["react-basics"],
          resources: [
            {
              type: "course",
              title: "Advanced React Course",
              url: "https://...",
              duration: 20,
              cost: 49.99,
              matchReason: "Practical focus for Practice-Oriented learner"
            }
          ],
          assessments: [
            {
              type: "quiz",
              passingScore: 70
            },
            {
              type: "project",
              title: "Build a Dashboard"
            }
          ]
        }
      ]
    }
  ],
  milestones: [
    {
      title: "Complete Phase 1",
      targetDate: "2026-04-15",
      criteria: ["All modules completed", "All assessments passed"]
    }
  ],
  adaptiveSettings: {
    contentDepth: "practical_focus",
    progressionSpeed: 1.0,
    difficultyCurve: "moderate_practical"
  },
  createdAt: "2026-02-15T10:40:00Z",
  status: "active"
}
```

**LearningProgress Model**:
```javascript
{
  id: "uuid",
  userId: "uuid",
  roadmapId: "uuid",
  completionPercentage: 35,
  modulesCompleted: 9,
  modulesTotal: 27,
  hoursInvested: 85,
  currentPhase: 2,
  lastActivityAt: "2026-02-15T09:30:00Z",
  activities: [
    {
      activityType: "module_completed",
      moduleId: "uuid",
      timestamp: "2026-02-14T15:20:00Z",
      durationSeconds: 7200,
      metadata: {
        quizScore: 85,
        projectCompleted: true
      }
    }
  ],
  proficiencyGains: {
    "React Hooks": {
      startLevel: "beginner",
      currentLevel: "intermediate",
      progress: 60
    }
  },
  updatedAt: "2026-02-15T09:30:00Z"
}
```

#### Design Decisions: Why Rule-Based for MVP

**1. Transparency and Explainability**
- Users can see exactly why they were classified
- Clear cause-and-effect relationship
- Builds trust in the system
- Easy to communicate to users

**2. Maintainability**
- Rules can be updated without retraining
- No model versioning complexity
- Easy to A/B test different thresholds
- Simple to debug and fix issues

**3. Reduced ML Complexity**
- No training data collection required
- No model training infrastructure needed
- No model monitoring or drift detection
- No GPU/specialized hardware requirements
- Faster development and deployment

**4. MVP Feasibility**
- Can be implemented in weeks, not months
- Lower development cost
- Easier to test and validate
- Simpler deployment pipeline
- Reduced operational overhead

**5. Predictable Behavior**
- Same inputs always produce same outputs
- No unexpected model behavior
- Easier to test edge cases
- Deterministic for compliance and auditing

**6. Performance**
- Instant classification (no model inference latency)
- No need for model serving infrastructure
- Scales linearly with user base
- Minimal computational resources

**Trade-offs Accepted**:
- Less personalization than ML-based approach
- Fixed thresholds may not be optimal for all users
- Cannot learn from user behavior over time
- Limited to predefined learner types

**Future ML Enhancement Path**:
Once MVP is validated and sufficient data is collected:
1. Collect user engagement and outcome data
2. Train ML model to predict optimal learner type
3. A/B test ML model against rule-based system
4. Gradually transition to ML-based classification
5. Maintain rule-based system as fallback

---

## 4. Data Flow

### 4.1 End-to-End Analysis Flow

```
User Input → Validation → Job Creation → Processing → Report Generation → Delivery

Step-by-Step Flow:

1. USER SUBMITS ANALYSIS REQUEST
   ├─ GitHub URL
   ├─ Resume PDF
   └─ Current Role Info
   
2. FRONTEND VALIDATION
   ├─ URL format check
   ├─ File type and size validation
   └─ Required fields verification
   
3. API REQUEST TO BACKEND
   ├─ POST /api/v1/analysis
   ├─ Authentication check
   ├─ Rate limit verification
   └─ Request validation
   
4. JOB CREATION
   ├─ Generate unique analysis ID
   ├─ Store initial data in database
   ├─ Upload resume to object storage
   ├─ Create job in queue
   └─ Return job ID to client
   
5. ASYNCHRONOUS PROCESSING
   ├─ Job picked up by worker
   ├─ Update status: "processing"
   └─ Begin parallel processing
   
6. PARALLEL DATA COLLECTION
   ├─ GitHub Integration Engine
   │  ├─ Fetch repository metadata
   │  ├─ Clone repository
   │  └─ Extract file structure
   │
   ├─ Resume Parser Engine
   │  ├─ Download PDF from storage
   │  ├─ Extract text content
   │  └─ Parse structured data
   │
   └─ Role Information
      └─ Already stored in database
   
7. CODE ANALYSIS
   ├─ Analyze code quality
   ├─ Detect technology stack
   ├─ Identify architecture patterns
   ├─ Calculate metrics
   └─ Store results in database
   
8. AI EVALUATION
   ├─ Prepare context from all sources
   ├─ Call AI API with structured prompt
   ├─ Parse AI response
   ├─ Generate insights and recommendations
   └─ Store evaluation results
   
9. REPORT GENERATION
   ├─ Compile all analysis data
   ├─ Generate visualizations
   ├─ Create PDF document
   ├─ Upload to object storage
   ├─ Generate JSON export
   └─ Update database with report URLs
   
10. COMPLETION
    ├─ Update status: "completed"
    ├─ Send notification (optional)
    └─ Client polls or receives webhook
    
11. USER RETRIEVES REPORT
    ├─ GET /api/v1/reports/:id
    ├─ View in web interface
    └─ Download PDF
```


### 4.2 Data Flow Diagram

```
┌──────────┐
│  Client  │
└────┬─────┘
     │ 1. Submit Analysis Request
     │    (GitHub URL, Resume PDF, Role Info)
     ▼
┌─────────────────┐
│   API Gateway   │
└────┬────────────┘
     │ 2. Validate & Authenticate
     ▼
┌─────────────────┐
│ Analysis Service│
└────┬────────────┘
     │ 3. Create Job
     ▼
┌─────────────────┐         ┌──────────────┐
│   Job Queue     │────────▶│   Database   │
└────┬────────────┘         └──────────────┘
     │ 4. Process Job
     ▼
┌─────────────────────────────────────────┐
│         Processing Workers              │
│  ┌──────────┐  ┌──────────┐  ┌────────┐│
│  │ GitHub   │  │ Resume   │  │  Code  ││
│  │ Fetcher  │  │ Parser   │  │Analyzer││
│  └────┬─────┘  └────┬─────┘  └───┬────┘│
└───────┼─────────────┼────────────┼─────┘
        │             │            │
        └─────────────┼────────────┘
                      │ 5. Aggregate Data
                      ▼
              ┌───────────────┐
              │  AI Engine    │
              └───────┬───────┘
                      │ 6. Generate Insights
                      ▼
              ┌───────────────┐
              │Report Generator│
              └───────┬───────┘
                      │ 7. Create PDF
                      ▼
              ┌───────────────┐
              │Object Storage │
              └───────┬───────┘
                      │ 8. Store Report
                      ▼
              ┌───────────────┐
              │   Database    │
              └───────┬───────┘
                      │ 9. Update Status
                      ▼
              ┌───────────────┐
              │    Client     │
              └───────────────┘
```

### 4.3 Error Handling Flow

```
Error Occurs → Classify Error → Log → Retry (if applicable) → Notify User

Error Types:
├─ Validation Errors (4xx)
│  └─ Return immediately with error message
│
├─ External Service Errors
│  ├─ GitHub API failure → Retry with exponential backoff
│  ├─ AI API failure → Retry up to 3 times
│  └─ Timeout → Mark job as failed, allow retry
│
├─ Processing Errors
│  ├─ PDF parsing failure → Skip resume analysis, continue
│  ├─ Code analysis failure → Use partial results
│  └─ Report generation failure → Retry once
│
└─ System Errors (5xx)
   ├─ Log to monitoring system
   ├─ Alert operations team
   └─ Return generic error to user
```

### 4.4 Adaptive Learning Module Data Flow

```
Analysis Completion → Skill Gap Analysis → Learning Path Generation → 
User Engagement → Progress Tracking → Adaptive Adjustments → Continuous Learning

Detailed Flow:

1. TRIGGER: ANALYSIS COMPLETED
   ├─ User's skill profile is ready
   ├─ Code metrics available
   ├─ Resume data parsed
   └─ AI evaluation complete

2. SKILL GAP ANALYSIS
   ├─ Fetch user's current skills
   ├─ Get target role requirements
   ├─ Retrieve industry benchmarks
   ├─ Calculate skill gaps
   │  ├─ Missing skills
   │  ├─ Proficiency gaps
   │  └─ Outdated technologies
   ├─ Prioritize gaps by severity
   └─ Store gap analysis results

3. LEARNING PATH GENERATION
   ├─ Retrieve skill gaps
   ├─ Fetch user preferences
   │  ├─ Learning style
   │  ├─ Available time
   │  └─ Budget constraints
   ├─ Build topic dependency graph
   ├─ Sequence learning modules
   ├─ Assign resources to each module
   │  ├─ Query resource database
   │  ├─ Match to learning style
   │  └─ Filter by budget
   ├─ Estimate time requirements
   ├─ Set milestones
   └─ Store learning path

4. USER VIEWS LEARNING PATH
   ├─ GET /api/v1/learning/paths/:pathId
   ├─ Display path overview
   ├─ Show current phase and modules
   └─ Present recommended next steps

5. USER STARTS LEARNING
   ├─ User selects a module
   ├─ POST /api/v1/learning/activities
   │  └─ Record "module_started" activity
   ├─ Display module content
   └─ Track time spent

6. USER COMPLETES ACTIVITIES
   ├─ User views resources
   │  └─ Record "resource_viewed" activity
   ├─ User completes resource
   │  └─ Record "resource_completed" activity
   ├─ User takes assessment
   │  ├─ Record "assessment_started"
   │  ├─ Submit answers
   │  ├─ Calculate score
   │  └─ Record "assessment_completed" with score
   └─ Update progress metrics

7. PROGRESS TRACKING
   ├─ Calculate completion percentage
   ├─ Update proficiency levels
   ├─ Check for milestone completion
   ├─ Update streak counter
   ├─ Check for achievement unlocks
   └─ Store updated progress

8. ADAPTIVE ADJUSTMENTS
   ├─ Analyze recent performance
   │  ├─ Assessment scores
   │  ├─ Completion rates
   │  ├─ Time spent
   │  └─ Engagement patterns
   ├─ Determine if adjustments needed
   │  ├─ Difficulty too high/low?
   │  ├─ Pace too fast/slow?
   │  ├─ Content type not engaging?
   │  └─ User struggling or excelling?
   ├─ Generate recommendations
   │  ├─ Adjust difficulty level
   │  ├─ Modify content mix
   │  ├─ Change pace
   │  └─ Provide additional support
   └─ Update learning path

9. INTERVENTION (IF NEEDED)
   ├─ Low comprehension detected
   │  └─ Recommend additional resources
   ├─ Low engagement detected
   │  └─ Send motivation message
   ├─ Rapid progress detected
   │  └─ Offer advanced content
   └─ Long inactivity detected
      └─ Send reminder notification

10. CONTINUOUS MONITORING
    ├─ Track daily activities
    ├─ Update analytics
    ├─ Generate weekly reports
    └─ Refine recommendations

11. PATH COMPLETION
    ├─ All modules completed
    ├─ Final assessment passed
    ├─ Award completion certificate
    ├─ Update user skill profile
    ├─ Suggest next learning path
    └─ Request feedback
```

**Data Flow Diagram: Skill Analysis to Learning Recommendation**

```
┌──────────────┐
│   Analysis   │
│   Complete   │
└──────┬───────┘
       │
       ▼
┌──────────────────────────────────────────┐
│      Skill Gap Analysis Engine           │
│  ┌────────────────────────────────────┐  │
│  │ 1. Extract user skills             │  │
│  │ 2. Fetch role requirements         │  │
│  │ 3. Compare with benchmarks         │  │
│  │ 4. Calculate gap severity          │  │
│  │ 5. Prioritize gaps                 │  │
│  └────────────────────────────────────┘  │
└──────┬───────────────────────────────────┘
       │ Skill Gaps (JSON)
       ▼
┌──────────────────────────────────────────┐
│      Learning Path Generator             │
│  ┌────────────────────────────────────┐  │
│  │ 1. Build topic graph               │  │
│  │ 2. Sequence modules                │  │
│  │ 3. Fetch resources                 │  │
│  │ 4. Estimate timeline               │  │
│  │ 5. Create structured path          │  │
│  └────────────────────────────────────┘  │
└──────┬───────────────────────────────────┘
       │ Learning Path (JSON)
       ▼
┌──────────────┐         ┌──────────────┐
│   Database   │◄────────│    Cache     │
└──────┬───────┘         └──────────────┘
       │
       ▼
┌──────────────┐
│     User     │
│  Interface   │
└──────┬───────┘
       │ User Engagement
       ▼
┌──────────────────────────────────────────┐
│      Progress Tracking System            │
│  ┌────────────────────────────────────┐  │
│  │ 1. Record activities               │  │
│  │ 2. Calculate progress              │  │
│  │ 3. Update metrics                  │  │
│  │ 4. Check milestones                │  │
│  │ 5. Award achievements              │  │
│  └────────────────────────────────────┘  │
└──────┬───────────────────────────────────┘
       │ Progress Data
       ▼
┌──────────────────────────────────────────┐
│      Adaptive Learning Engine            │
│  ┌────────────────────────────────────┐  │
│  │ 1. Analyze performance             │  │
│  │ 2. Detect patterns                 │  │
│  │ 3. Calculate adjustments           │  │
│  │ 4. Generate recommendations        │  │
│  │ 5. Update path dynamically         │  │
│  └────────────────────────────────────┘  │
└──────┬───────────────────────────────────┘
       │ Adaptations
       ▼
┌──────────────┐
│   Updated    │
│ Learning Path│
└──────────────┘
```

### 4.5 Integrated Learning Pipeline Data Flow

This section describes how the Adaptive Learning Module integrates with the existing analysis pipeline:

```
EXISTING ANALYSIS PIPELINE          ADAPTIVE LEARNING PIPELINE
─────────────────────────────       ────────────────────────────

1. User submits inputs
   ├─ GitHub URL
   ├─ Resume PDF
   └─ Role info
        │
        ▼
2. Analysis processing
   ├─ Code analysis
   ├─ Resume parsing
   └─ Skill evaluation
        │
        ▼
3. Generate report
   ├─ Skills identified
   ├─ Code metrics
   └─ Recommendations
        │
        │ INTEGRATION POINT
        ├──────────────────────────────────▶ 4. Skill Gap Analysis
        │                                      ├─ Compare skills vs role
        │                                      ├─ Categorize gaps
        │                                      └─ Prioritize
        │                                           │
        │                                           ▼
        │                                    5. Diagnostic Quiz
        │                                      ├─ Generate questions
        │                                      ├─ User takes quiz
        │                                      └─ Calculate score
        │                                           │
        │                                           ▼
        │                                    6. Learner Classification
        │                                      ├─ Apply rules
        │                                      ├─ Determine type
        │                                      └─ Set adaptations
        │                                           │
        │                                           ▼
        │                                    7. Generate Learning Path
        │                                      ├─ Sequence modules
        │                                      ├─ Match resources
        │                                      └─ Create roadmap
        │                                           │
        │                                           ▼
        └──────────────────────────────────▶ 8. Combined Output
                                               ├─ Analysis Report (PDF)
                                               └─ Learning Roadmap (PDF)

ONGOING LEARNING CYCLE
────────────────────────

9. User follows roadmap
   ├─ Complete modules
   ├─ Take assessments
   └─ Track progress
        │
        ▼
10. Progress tracking
    ├─ Update completion %
    ├─ Record time invested
    └─ Store quiz scores
        │
        ▼
11. Optional: Re-analysis
    ├─ User improves code
    ├─ Submits new analysis
    └─ Updated roadmap generated
```

**Integration Points**:

1. **Analysis → Skill Gap**: Analysis results feed directly into skill gap analyzer
2. **Code Metrics → Classification**: Code complexity score used for learner classification
3. **Role Info → Path Generation**: Target role determines learning path structure
4. **Progress → Re-analysis**: Progress data can inform future analyses

**Data Flow Timing**:

```
T+0:00  User submits analysis request
T+0:05  Analysis processing begins
T+2:00  Analysis complete, report generated
T+2:01  Skill gap analysis triggered automatically
T+2:02  User presented with diagnostic quiz
T+2:15  User completes quiz (13 minutes)
T+2:16  Learner classification executed (instant)
T+2:17  Learning path generation begins
T+2:30  Learning roadmap complete (13 seconds)
T+2:31  User receives both analysis report and learning roadmap
```

---

## 5. Database Design

### 5.1 Database Schema

**Database**: PostgreSQL 14+

#### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    github_username VARCHAR(255),
    github_access_token TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login_at TIMESTAMP,
    is_active BOOLEAN DEFAULT true,
    email_verified BOOLEAN DEFAULT false
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_github_username ON users(github_username);
```

#### Analyses Table
```sql
CREATE TABLE analyses (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    github_repo_url VARCHAR(500) NOT NULL,
    resume_file_path VARCHAR(500),
    current_role VARCHAR(255),
    years_experience DECIMAL(3,1),
    primary_technologies TEXT[],
    company_size VARCHAR(50),
    status VARCHAR(50) DEFAULT 'pending',
    -- Status: pending, processing, completed, failed
    error_message TEXT,
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_analyses_user_id ON analyses(user_id);
CREATE INDEX idx_analyses_status ON analyses(status);
CREATE INDEX idx_analyses_created_at ON analyses(created_at DESC);
```

#### Code Metrics Table
```sql
CREATE TABLE code_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    total_lines_of_code INTEGER,
    number_of_files INTEGER,
    cyclomatic_complexity DECIMAL(5,2),
    maintainability_index DECIMAL(5,2),
    code_duplication_percentage DECIMAL(5,2),
    comment_ratio DECIMAL(5,2),
    test_coverage_percentage DECIMAL(5,2),
    number_of_dependencies INTEGER,
    code_organization_score DECIMAL(5,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_code_metrics_analysis_id ON code_metrics(analysis_id);
```

#### Technologies Table
```sql
CREATE TABLE technologies (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    category VARCHAR(50),
    -- Category: language, framework, library, database, tool, devops
    version VARCHAR(50),
    usage_percentage DECIMAL(5,2),
    proficiency_level VARCHAR(50),
    -- Proficiency: beginner, intermediate, advanced, expert
    source VARCHAR(50),
    -- Source: code, resume, both
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_technologies_analysis_id ON technologies(analysis_id);
CREATE INDEX idx_technologies_category ON technologies(category);
```

#### Skills Table
```sql
CREATE TABLE skills (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    skill_name VARCHAR(100) NOT NULL,
    proficiency_level VARCHAR(50),
    in_code BOOLEAN DEFAULT false,
    in_resume BOOLEAN DEFAULT false,
    alignment_score DECIMAL(5,2),
    evidence_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_skills_analysis_id ON skills(analysis_id);
```

#### Recommendations Table
```sql
CREATE TABLE recommendations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    category VARCHAR(100),
    -- Category: learning, code_quality, architecture, career
    title VARCHAR(255) NOT NULL,
    description TEXT,
    priority INTEGER,
    -- Priority: 1 (highest) to 5 (lowest)
    estimated_impact VARCHAR(50),
    -- Impact: high, medium, low
    resources JSONB,
    -- Array of learning resources
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_recommendations_analysis_id ON recommendations(analysis_id);
CREATE INDEX idx_recommendations_priority ON recommendations(priority);
```

#### Reports Table
```sql
CREATE TABLE reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID UNIQUE NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    pdf_file_path VARCHAR(500),
    json_file_path VARCHAR(500),
    share_token VARCHAR(100) UNIQUE,
    share_expires_at TIMESTAMP,
    download_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_reports_analysis_id ON reports(analysis_id);
CREATE INDEX idx_reports_share_token ON reports(share_token);
```


#### Resume Data Table
```sql
CREATE TABLE resume_data (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID UNIQUE NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    raw_text TEXT,
    education JSONB,
    -- Array of education entries
    work_experience JSONB,
    -- Array of work experience entries
    certifications JSONB,
    -- Array of certifications
    extracted_skills TEXT[],
    total_years_experience DECIMAL(4,1),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_resume_data_analysis_id ON resume_data(analysis_id);
```

#### Architecture Patterns Table
```sql
CREATE TABLE architecture_patterns (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    pattern_name VARCHAR(100) NOT NULL,
    -- Pattern: MVC, microservices, monolithic, layered, etc.
    confidence_score DECIMAL(5,2),
    evidence TEXT,
    quality_score DECIMAL(5,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_architecture_patterns_analysis_id ON architecture_patterns(analysis_id);
```

#### Skill Gaps Table
```sql
CREATE TABLE skill_gaps (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_id UUID NOT NULL REFERENCES analyses(id) ON DELETE CASCADE,
    skill_name VARCHAR(100) NOT NULL,
    current_level VARCHAR(50),
    -- Current: none, beginner, intermediate, advanced
    target_level VARCHAR(50) NOT NULL,
    -- Target: beginner, intermediate, advanced, expert
    gap_severity VARCHAR(50) NOT NULL,
    -- Severity: critical, high, medium, low
    severity_score DECIMAL(5,2),
    -- Score: 0-100
    priority INTEGER,
    -- Priority: 1 (highest) to 5 (lowest)
    market_demand_score DECIMAL(5,2),
    career_impact_score DECIMAL(5,2),
    learning_difficulty VARCHAR(50),
    -- Difficulty: easy, moderate, hard, very_hard
    estimated_learning_hours INTEGER,
    reason TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_skill_gaps_analysis_id ON skill_gaps(analysis_id);
CREATE INDEX idx_skill_gaps_severity ON skill_gaps(gap_severity);
CREATE INDEX idx_skill_gaps_priority ON skill_gaps(priority);
```

#### Learning Paths Table
```sql
CREATE TABLE learning_paths (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    analysis_id UUID REFERENCES analyses(id) ON DELETE SET NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    difficulty VARCHAR(50),
    -- Difficulty: beginner, intermediate, advanced, expert
    estimated_duration_weeks INTEGER,
    estimated_hours INTEGER,
    status VARCHAR(50) DEFAULT 'active',
    -- Status: active, paused, completed, abandoned
    target_role VARCHAR(255),
    learning_style VARCHAR(50),
    -- Style: visual, reading, practical, interactive, mixed
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    started_at TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE INDEX idx_learning_paths_user_id ON learning_paths(user_id);
CREATE INDEX idx_learning_paths_status ON learning_paths(status);
CREATE INDEX idx_learning_paths_analysis_id ON learning_paths(analysis_id);
```

#### Learning Modules Table
```sql
CREATE TABLE learning_modules (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    path_id UUID NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    phase_number INTEGER NOT NULL,
    phase_title VARCHAR(255),
    module_order INTEGER NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    difficulty VARCHAR(50),
    estimated_hours INTEGER,
    prerequisites TEXT[],
    -- Array of prerequisite module IDs or skill names
    learning_objectives TEXT[],
    status VARCHAR(50) DEFAULT 'not_started',
    -- Status: not_started, in_progress, completed, skipped
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    started_at TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE INDEX idx_learning_modules_path_id ON learning_modules(path_id);
CREATE INDEX idx_learning_modules_status ON learning_modules(status);
CREATE INDEX idx_learning_modules_order ON learning_modules(path_id, phase_number, module_order);
```

#### Learning Resources Table
```sql
CREATE TABLE learning_resources (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    module_id UUID NOT NULL REFERENCES learning_modules(id) ON DELETE CASCADE,
    resource_type VARCHAR(50) NOT NULL,
    -- Type: course, video, article, book, documentation, project, tutorial
    title VARCHAR(255) NOT NULL,
    description TEXT,
    provider VARCHAR(100),
    url TEXT,
    duration_hours DECIMAL(5,2),
    cost_usd DECIMAL(8,2),
    cost_type VARCHAR(50),
    -- Cost type: free, one_time, subscription
    rating DECIMAL(3,2),
    difficulty VARCHAR(50),
    is_recommended BOOLEAN DEFAULT false,
    resource_order INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_learning_resources_module_id ON learning_resources(module_id);
CREATE INDEX idx_learning_resources_type ON learning_resources(resource_type);
```

#### Learning Activities Table
```sql
CREATE TABLE learning_activities (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    path_id UUID NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    module_id UUID REFERENCES learning_modules(id) ON DELETE SET NULL,
    resource_id UUID REFERENCES learning_resources(id) ON DELETE SET NULL,
    activity_type VARCHAR(50) NOT NULL,
    -- Type: module_started, module_completed, resource_viewed, 
    --       resource_completed, assessment_started, assessment_completed,
    --       project_started, project_completed, note_created
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    duration_seconds INTEGER,
    metadata JSONB,
    -- Flexible field for activity-specific data (scores, notes, etc.)
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_learning_activities_user_id ON learning_activities(user_id);
CREATE INDEX idx_learning_activities_path_id ON learning_activities(path_id);
CREATE INDEX idx_learning_activities_module_id ON learning_activities(module_id);
CREATE INDEX idx_learning_activities_timestamp ON learning_activities(timestamp DESC);
CREATE INDEX idx_learning_activities_type ON learning_activities(activity_type);
```

#### Assessments Table
```sql
CREATE TABLE assessments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    module_id UUID NOT NULL REFERENCES learning_modules(id) ON DELETE CASCADE,
    assessment_type VARCHAR(50) NOT NULL,
    -- Type: quiz, coding_challenge, project, peer_review
    title VARCHAR(255) NOT NULL,
    description TEXT,
    passing_score INTEGER DEFAULT 70,
    time_limit_minutes INTEGER,
    max_attempts INTEGER,
    questions JSONB,
    -- Array of question objects
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_assessments_module_id ON assessments(module_id);
```

#### Assessment Results Table
```sql
CREATE TABLE assessment_results (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    assessment_id UUID NOT NULL REFERENCES assessments(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    attempt_number INTEGER NOT NULL,
    score INTEGER NOT NULL,
    passed BOOLEAN NOT NULL,
    time_spent_seconds INTEGER,
    answers JSONB,
    -- User's answers
    feedback JSONB,
    -- Detailed feedback per question
    started_at TIMESTAMP NOT NULL,
    completed_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_assessment_results_assessment_id ON assessment_results(assessment_id);
CREATE INDEX idx_assessment_results_user_id ON assessment_results(user_id);
CREATE INDEX idx_assessment_results_completed_at ON assessment_results(completed_at DESC);
```

#### Progress Tracking Table
```sql
CREATE TABLE progress_tracking (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    path_id UUID NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    completion_percentage DECIMAL(5,2) DEFAULT 0,
    modules_completed INTEGER DEFAULT 0,
    modules_total INTEGER NOT NULL,
    hours_invested DECIMAL(8,2) DEFAULT 0,
    estimated_hours_remaining DECIMAL(8,2),
    current_phase INTEGER DEFAULT 1,
    total_phases INTEGER NOT NULL,
    average_assessment_score DECIMAL(5,2),
    current_streak_days INTEGER DEFAULT 0,
    longest_streak_days INTEGER DEFAULT 0,
    total_active_days INTEGER DEFAULT 0,
    last_activity_at TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_progress_tracking_user_id ON progress_tracking(user_id);
CREATE INDEX idx_progress_tracking_path_id ON progress_tracking(path_id);
CREATE UNIQUE INDEX idx_progress_tracking_user_path ON progress_tracking(user_id, path_id);
```

#### Proficiency Tracking Table
```sql
CREATE TABLE proficiency_tracking (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    path_id UUID NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    skill_name VARCHAR(100) NOT NULL,
    start_level VARCHAR(50),
    current_level VARCHAR(50),
    target_level VARCHAR(50),
    progress_percentage DECIMAL(5,2) DEFAULT 0,
    last_assessed_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_proficiency_tracking_user_id ON proficiency_tracking(user_id);
CREATE INDEX idx_proficiency_tracking_path_id ON proficiency_tracking(path_id);
CREATE INDEX idx_proficiency_tracking_skill ON proficiency_tracking(skill_name);
```

#### Achievements Table
```sql
CREATE TABLE achievements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    achievement_key VARCHAR(100) UNIQUE NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    icon_url VARCHAR(500),
    category VARCHAR(50),
    -- Category: milestone, streak, mastery, special
    points INTEGER DEFAULT 0,
    rarity VARCHAR(50),
    -- Rarity: common, uncommon, rare, epic, legendary
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_achievements_category ON achievements(category);
```

#### User Achievements Table
```sql
CREATE TABLE user_achievements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    achievement_id UUID NOT NULL REFERENCES achievements(id) ON DELETE CASCADE,
    unlocked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    progress_data JSONB,
    -- For progressive achievements
    UNIQUE(user_id, achievement_id)
);

CREATE INDEX idx_user_achievements_user_id ON user_achievements(user_id);
CREATE INDEX idx_user_achievements_unlocked_at ON user_achievements(unlocked_at DESC);
```

#### Adaptive Settings Table
```sql
CREATE TABLE adaptive_settings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    path_id UUID NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    current_difficulty VARCHAR(50) DEFAULT 'intermediate',
    difficulty_adjustment_factor DECIMAL(3,2) DEFAULT 1.0,
    -- Factor: 0.5 (easier) to 2.0 (harder)
    pace_adjustment VARCHAR(50) DEFAULT 'normal',
    -- Pace: slow, normal, fast
    content_preference JSONB,
    -- Preferred content types and weights
    intervention_history JSONB,
    -- Record of adaptive interventions
    last_adjustment_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, path_id)
);

CREATE INDEX idx_adaptive_settings_user_id ON adaptive_settings(user_id);
CREATE INDEX idx_adaptive_settings_path_id ON adaptive_settings(path_id);
```

### 5.2 Entity Relationship Diagram

```
┌─────────────┐
│    Users    │
└──────┬──────┘
       │ 1:N
       ├──────────────────────────────────────────────────────┐
       │                                                       │
       ▼                                                       ▼
┌─────────────┐                                      ┌─────────────────┐
│  Analyses   │                                      │ Learning Paths  │
└──────┬──────┘                                      └────────┬────────┘
       │ 1:1                                                  │ 1:N
       ├──────────────┬──────────────┬──────────────┐        │
       │              │              │              │        ▼
       ▼              ▼              ▼              ▼  ┌──────────────────┐
┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐│Learning Modules│
│Code Metrics│ │Technologies│ │   Skills   │ │Recommend-  │└────────┬───────┘
└────────────┘ └────────────┘ └────────────┘ │  ations    │         │ 1:N
                                              └────────────┘         │
       │                                                             ▼
       ├──────────────┬──────────────┬──────────────┐    ┌──────────────────┐
       │              │              │              │    │Learning Resources│
       ▼              ▼              ▼              ▼    └──────────────────┘
┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐         │
│Resume Data │ │Architecture│ │ Skill Gaps │ │  Reports   │         │ 1:N
│            │ │  Patterns  │ │            │ │            │         │
└────────────┘ └────────────┘ └────────────┘ └────────────┘         ▼
                                                            ┌──────────────────┐
                                                            │   Assessments    │
                                                            └────────┬─────────┘
                                                                     │ 1:N
                                                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    LEARNING & PROGRESS TRACKING                          │
│                                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐│
│  │  Learning    │  │   Progress   │  │ Proficiency  │  │  Assessment │││
│  │  Activities  │  │   Tracking   │  │   Tracking   │  │   Results   │││
│  └──────────────┘  └──────────────┘  └──────────────┘  └─────────────┘││
│                                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                 │
│  │     User     │  │ Achievements │  │   Adaptive   │                 │
│  │ Achievements │  │   (Master)   │  │   Settings   │                 │
│  └──────────────┘  └──────────────┘  └──────────────┘                 │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Data Retention Policy

- Active analyses: Retained indefinitely while user account is active
- Completed analyses: Retained for 1 year after creation
- Failed analyses: Retained for 30 days
- Deleted user data: Soft delete for 30 days, then hard delete
- Resume files: Deleted after 90 days or when analysis is deleted
- Generated reports: Retained for 1 year

---

## 6. External Integrations

### 6.1 GitHub API Integration

**Purpose**: Fetch repository data and metadata

**Authentication**: OAuth 2.0 or Personal Access Token

**Rate Limits**:
- Authenticated: 5,000 requests/hour
- Unauthenticated: 60 requests/hour

**Endpoints Used**:
```
GET /repos/{owner}/{repo}
GET /repos/{owner}/{repo}/languages
GET /repos/{owner}/{repo}/contents/{path}
GET /repos/{owner}/{repo}/commits
GET /repos/{owner}/{repo}/contributors
GET /repos/{owner}/{repo}/stats/code_frequency
```

**Error Handling**:
- 404: Repository not found → User-friendly error
- 403: Rate limit exceeded → Queue for retry
- 401: Authentication failed → Request re-authentication
- 500: GitHub service error → Retry with exponential backoff

**Caching Strategy**:
- Cache repository metadata for 1 hour
- Cache file contents for duration of analysis
- Invalidate cache on new analysis request

### 6.2 OpenAI API Integration

**Purpose**: AI-powered analysis and insights generation

**Authentication**: API Key

**Model**: GPT-4 or GPT-3.5-turbo

**Rate Limits**:
- Tier-dependent (typically 3,500 requests/minute for GPT-4)
- Token limits per request

**Usage Pattern**:
```javascript
const prompt = {
  model: "gpt-4",
  messages: [
    {
      role: "system",
      content: "You are an expert technical recruiter..."
    },
    {
      role: "user",
      content: JSON.stringify(analysisData)
    }
  ],
  temperature: 0.7,
  max_tokens: 2000,
  response_format: { type: "json_object" }
};
```

**Cost Optimization**:
- Use GPT-3.5-turbo for initial analysis
- Use GPT-4 only for complex evaluations
- Cache similar analysis results
- Batch requests when possible

**Error Handling**:
- Rate limit: Implement exponential backoff
- Invalid response: Retry with adjusted prompt
- Timeout: Retry up to 3 times
- Service unavailable: Queue for later processing

### 6.3 Email Service Integration

**Purpose**: User notifications and report delivery

**Provider Options**: SendGrid, AWS SES, or Mailgun

**Email Types**:
- Welcome email on registration
- Email verification
- Analysis completion notification
- Password reset
- Weekly digest (optional)

**Template Structure**:
```
Subject: Your Developer Profile Analysis is Ready

Hi {user_name},

Your analysis for repository {repo_name} is complete!

View your report: {report_link}

Key Insights:
- Overall Score: {score}/100
- Top Strength: {top_strength}
- Priority Recommendation: {top_recommendation}

Best regards,
Developer Profile Analyzer Team
```

### 6.4 Object Storage Integration

**Purpose**: Store resumes and generated reports

**Provider**: AWS S3, Google Cloud Storage, or Azure Blob Storage

**Bucket Structure**:
```
/resumes/{user_id}/{analysis_id}/resume.pdf
/reports/{user_id}/{analysis_id}/report.pdf
/reports/{user_id}/{analysis_id}/report.json
/cache/{repo_hash}/repository_data.zip
```

**Access Control**:
- Private by default
- Pre-signed URLs for temporary access
- Expiration time: 1 hour for downloads
- CORS configuration for direct uploads

**Lifecycle Policies**:
- Move to infrequent access after 30 days
- Delete after 1 year
- Automatic cleanup of failed uploads

### 6.5 Monitoring and Logging Integration

**Purpose**: System health monitoring and debugging

**Provider**: DataDog, New Relic, or ELK Stack

**Metrics Tracked**:
- API response times
- Error rates by endpoint
- Analysis processing time
- External API latency
- Database query performance
- Queue depth and processing rate
- User activity metrics

**Logging Levels**:
- ERROR: System failures, unhandled exceptions
- WARN: Recoverable errors, rate limits
- INFO: Analysis lifecycle events
- DEBUG: Detailed processing information

**Alerts**:
- Error rate > 5% for 5 minutes
- API response time > 2 seconds
- Queue depth > 100 jobs
- External service failures
- Database connection issues

---

## 7. Security Considerations

### 7.1 Authentication and Authorization

**Authentication Method**: JWT (JSON Web Tokens)

**Token Structure**:
```javascript
{
  "access_token": "eyJhbGc...",  // Short-lived (15 minutes)
  "refresh_token": "eyJhbGc...", // Long-lived (7 days)
  "token_type": "Bearer",
  "expires_in": 900
}
```

**Password Security**:
- Minimum 8 characters
- Hashing: bcrypt with salt rounds = 12
- Password reset via email with time-limited tokens
- Account lockout after 5 failed attempts

**OAuth Integration**:
- GitHub OAuth for repository access
- Secure token storage (encrypted at rest)
- Token refresh mechanism
- Scope limitation (read-only access)


### 7.2 Data Protection

**Encryption at Rest**:
- Database: AES-256 encryption
- Object storage: Server-side encryption (SSE)
- Sensitive fields: Application-level encryption for tokens

**Encryption in Transit**:
- TLS 1.3 for all API communications
- HTTPS enforced (HSTS headers)
- Certificate pinning for mobile apps (future)

**Data Sanitization**:
- Input validation on all endpoints
- SQL injection prevention (parameterized queries)
- XSS prevention (output encoding)
- CSRF protection (tokens)

**PII Handling**:
- Minimal PII collection
- Data anonymization for analytics
- Right to deletion (GDPR compliance)
- Data export capability

### 7.3 API Security

**Rate Limiting**:
```javascript
// Per user limits
{
  "anonymous": "10 requests/hour",
  "authenticated": "100 requests/hour",
  "premium": "1000 requests/hour"
}

// Per endpoint limits
{
  "/api/v1/analysis": "5 requests/hour",
  "/api/v1/reports/:id": "50 requests/hour"
}
```

**Request Validation**:
- Schema validation (JSON Schema or Joi)
- File type validation (magic number check)
- File size limits
- URL validation and sanitization

**CORS Configuration**:
```javascript
{
  "origin": ["https://app.example.com"],
  "methods": ["GET", "POST", "PUT", "DELETE"],
  "allowedHeaders": ["Content-Type", "Authorization"],
  "credentials": true,
  "maxAge": 86400
}
```

### 7.4 Infrastructure Security

**Network Security**:
- VPC isolation for backend services
- Security groups with least privilege
- Private subnets for databases
- WAF (Web Application Firewall) for DDoS protection

**Access Control**:
- IAM roles with minimal permissions
- Service accounts for inter-service communication
- Secrets management (AWS Secrets Manager, HashiCorp Vault)
- Regular credential rotation

**Vulnerability Management**:
- Automated dependency scanning
- Regular security audits
- Penetration testing (quarterly)
- Security patch management

### 7.5 Compliance

**GDPR Compliance**:
- Data processing agreements
- User consent management
- Right to access data
- Right to deletion
- Data portability
- Privacy policy and terms of service

**Data Residency**:
- EU data stored in EU regions
- US data stored in US regions
- Compliance with local regulations

---

## 8. Scalability Considerations

### 8.1 Horizontal Scaling

**Application Layer**:
- Stateless API servers
- Load balancer distribution (Round-robin, Least connections)
- Auto-scaling based on CPU/memory metrics
- Container orchestration (Kubernetes or ECS)

**Processing Layer**:
- Worker pool for analysis jobs
- Dynamic worker scaling based on queue depth
- Separate worker types for different tasks
- Resource isolation per worker

**Scaling Triggers**:
```javascript
{
  "scale_up": {
    "cpu_threshold": "70%",
    "memory_threshold": "80%",
    "queue_depth": "> 50 jobs",
    "response_time": "> 2 seconds"
  },
  "scale_down": {
    "cpu_threshold": "< 30%",
    "memory_threshold": "< 40%",
    "queue_depth": "< 10 jobs",
    "cooldown_period": "10 minutes"
  }
}
```

### 8.2 Database Scaling

**Read Replicas**:
- Primary for writes
- Multiple read replicas for queries
- Read/write splitting in application
- Eventual consistency acceptable for reports

**Connection Pooling**:
- PgBouncer or similar
- Pool size: 20-50 connections per instance
- Connection timeout: 30 seconds

**Query Optimization**:
- Proper indexing strategy
- Query plan analysis
- Materialized views for complex queries
- Pagination for large result sets

**Partitioning Strategy**:
- Partition analyses table by created_at (monthly)
- Archive old data to cold storage
- Separate hot and cold data

### 8.3 Caching Strategy

**Cache Layers**:

**L1 - Application Cache** (In-memory):
- User session data
- Frequently accessed configuration
- TTL: 5-15 minutes

**L2 - Redis Cache**:
- API responses
- Repository metadata
- Analysis results
- TTL: 1-24 hours

**L3 - CDN Cache**:
- Static assets (JS, CSS, images)
- Public reports (if applicable)
- TTL: 7 days

**Cache Invalidation**:
- Time-based expiration
- Event-based invalidation (on data update)
- Manual purge capability
- Cache warming for popular data

### 8.4 Asynchronous Processing

**Job Queue Architecture**:
```
┌──────────────┐
│  API Server  │
└──────┬───────┘
       │ Enqueue Job
       ▼
┌──────────────┐
│  Job Queue   │
│   (Redis)    │
└──────┬───────┘
       │ Distribute
       ├─────────┬─────────┬─────────┐
       ▼         ▼         ▼         ▼
   ┌────────┐┌────────┐┌────────┐┌────────┐
   │Worker 1││Worker 2││Worker 3││Worker N│
   └────────┘└────────┘└────────┘└────────┘
```

**Job Priorities**:
- High: Premium user analyses
- Normal: Standard user analyses
- Low: Batch processing, cleanup tasks

**Retry Strategy**:
- Max retries: 3
- Backoff: Exponential (1min, 5min, 15min)
- Dead letter queue for failed jobs
- Manual retry capability

### 8.5 Performance Optimization

**Code Analysis Optimization**:
- Parallel file processing
- Incremental analysis (cache previous results)
- Skip binary and large media files
- Limit analysis to relevant file types

**API Optimization**:
- Response compression (gzip)
- Pagination for list endpoints
- Field filtering (sparse fieldsets)
- ETags for conditional requests

**Database Optimization**:
- Query result caching
- Batch inserts for bulk data
- Async writes where possible
- Database connection pooling

**Frontend Optimization**:
- Code splitting and lazy loading
- Image optimization
- Service worker for offline capability
- Progressive web app (PWA) features

**Learning Module Specific Optimizations**:
- Cache learning paths and module content
- Lazy load module resources
- Batch activity tracking (buffer and send in batches)
- Preload next module content
- Offline mode for downloaded content

### 8.6 Scalability Considerations for Adaptive Learning Module

**Data Volume Challenges**:
- High-frequency activity tracking (potentially millions of events/day)
- Real-time progress calculations
- Complex adaptive algorithm computations
- Large resource database queries

**Scalability Solutions**:

**1. Activity Tracking Optimization**:
```javascript
// Client-side buffering
const activityBuffer = [];
const BUFFER_SIZE = 10;
const FLUSH_INTERVAL = 30000; // 30 seconds

function trackActivity(activity) {
  activityBuffer.push(activity);
  
  if (activityBuffer.length >= BUFFER_SIZE) {
    flushActivities();
  }
}

function flushActivities() {
  if (activityBuffer.length > 0) {
    api.post('/learning/activities/batch', activityBuffer);
    activityBuffer.length = 0;
  }
}

// Periodic flush
setInterval(flushActivities, FLUSH_INTERVAL);
```

**2. Progress Calculation Optimization**:
- Use materialized views for complex progress queries
- Cache progress data in Redis (TTL: 5 minutes)
- Update progress asynchronously via job queue
- Denormalize frequently accessed metrics

```sql
-- Materialized view for progress summary
CREATE MATERIALIZED VIEW user_progress_summary AS
SELECT 
  user_id,
  path_id,
  COUNT(DISTINCT module_id) as modules_completed,
  SUM(duration_seconds) / 3600.0 as hours_invested,
  AVG(CASE WHEN activity_type = 'assessment_completed' 
      THEN (metadata->>'score')::int END) as avg_score
FROM learning_activities
GROUP BY user_id, path_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW CONCURRENTLY user_progress_summary;
```

**3. Adaptive Algorithm Optimization**:
- Run adaptive adjustments asynchronously
- Batch process multiple users
- Cache algorithm results
- Use sampling for large datasets

```javascript
// Async adaptive processing
async function scheduleAdaptiveAdjustment(userId, pathId) {
  await jobQueue.add('adaptive-adjustment', {
    userId,
    pathId,
    priority: 'normal'
  }, {
    delay: 60000, // Process after 1 minute
    attempts: 3
  });
}

// Batch processing worker
async function processAdaptiveAdjustments(jobs) {
  const userPaths = jobs.map(j => j.data);
  const activities = await fetchActivitiesBatch(userPaths);
  const adjustments = await calculateAdjustmentsBatch(activities);
  await applyAdjustmentsBatch(adjustments);
}
```

**4. Resource Database Scaling**:
- Implement full-text search with Elasticsearch
- Cache popular resources
- Use CDN for resource metadata
- Partition by resource type

**5. Real-time Features Scaling**:
- Use WebSockets for live progress updates
- Implement pub/sub for achievement notifications
- Use Redis for real-time leaderboards
- Implement connection pooling

**6. Database Partitioning Strategy**:
```sql
-- Partition learning_activities by month
CREATE TABLE learning_activities_2026_02 
PARTITION OF learning_activities
FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');

-- Partition by user_id for large tables
CREATE TABLE learning_activities_partition_0
PARTITION OF learning_activities
FOR VALUES WITH (MODULUS 10, REMAINDER 0);
```

**7. Caching Strategy for Learning Module**:
```javascript
// Multi-level caching
const cacheStrategy = {
  learningPaths: {
    location: 'redis',
    ttl: 3600, // 1 hour
    invalidateOn: ['path_updated', 'module_completed']
  },
  moduleContent: {
    location: 'cdn',
    ttl: 86400, // 24 hours
    invalidateOn: ['content_updated']
  },
  progressData: {
    location: 'redis',
    ttl: 300, // 5 minutes
    invalidateOn: ['activity_recorded']
  },
  resources: {
    location: 'redis',
    ttl: 7200, // 2 hours
    invalidateOn: ['resource_updated']
  }
};
```

**8. Load Distribution**:
- Separate read/write workloads
- Use read replicas for progress queries
- Dedicated workers for adaptive processing
- Queue-based activity processing

**9. Monitoring Metrics for Learning Module**:
```javascript
const learningMetrics = {
  activityRate: 'activities_per_second',
  progressCalculationTime: 'avg_progress_calc_ms',
  adaptiveAdjustmentTime: 'avg_adaptive_calc_ms',
  cacheHitRate: 'learning_cache_hit_percentage',
  queueDepth: 'learning_jobs_pending',
  userEngagement: 'daily_active_learners',
  moduleCompletionRate: 'modules_completed_per_day'
};
```

**10. Horizontal Scaling Approach**:
```
┌─────────────────────────────────────────────────────┐
│              Load Balancer                          │
└────────────┬────────────────────────────────────────┘
             │
    ┌────────┼────────┬────────┐
    ▼        ▼        ▼        ▼
┌────────┐┌────────┐┌────────┐┌────────┐
│API     ││API     ││API     ││API     │
│Server 1││Server 2││Server 3││Server N│
└────────┘└────────┘└────────┘└────────┘
             │
    ┌────────┼────────┬────────┐
    ▼        ▼        ▼        ▼
┌────────┐┌────────┐┌────────┐┌────────┐
│Worker  ││Worker  ││Worker  ││Worker  │
│Pool 1  ││Pool 2  ││Pool 3  ││Pool N  │
│(Activity)│(Progress)│(Adaptive)│(Reports)│
└────────┘└────────┘└────────┘└────────┘
```

---

## 9. Technology Stack Recommendation

### 9.1 Frontend Stack

**Core Framework**: React 18+ with TypeScript
- Component-based architecture
- Strong typing for reliability
- Large ecosystem and community
- Excellent performance

**State Management**: Redux Toolkit or Zustand
- Predictable state management
- DevTools for debugging
- Middleware support

**UI Framework**: Material-UI (MUI) or Tailwind CSS
- Pre-built components
- Responsive design system
- Customizable theming

**Data Visualization**: Chart.js or Recharts
- Interactive charts
- Responsive and accessible
- Good documentation

**HTTP Client**: Axios
- Promise-based
- Request/response interceptors
- Automatic JSON transformation

**Build Tool**: Vite
- Fast development server
- Optimized production builds
- Modern ES modules support

**Testing**:
- Jest for unit tests
- React Testing Library for component tests
- Cypress for E2E tests

### 9.2 Backend Stack

**Runtime**: Node.js 18+ LTS
- JavaScript/TypeScript consistency with frontend
- Large package ecosystem
- Excellent async I/O performance
- Strong community support

**Framework**: Express.js or Fastify
- Lightweight and flexible
- Extensive middleware ecosystem
- Well-documented

**Alternative**: Python 3.11+ with FastAPI
- Excellent for data processing
- Strong ML/AI library support
- Type hints and validation
- Async support

**API Documentation**: Swagger/OpenAPI
- Auto-generated documentation
- Interactive API testing
- Client SDK generation

**Validation**: Joi or Zod
- Schema-based validation
- TypeScript integration
- Detailed error messages

**Testing**:
- Jest for unit tests
- Supertest for API tests
- Artillery for load testing


### 9.3 Database and Storage

**Primary Database**: PostgreSQL 14+
- ACID compliance
- JSON support (JSONB)
- Full-text search
- Excellent performance
- Strong community

**Caching**: Redis 7+
- In-memory performance
- Pub/sub for real-time features
- Job queue support
- Session storage

**Object Storage**: AWS S3 or Compatible
- Scalable file storage
- Lifecycle management
- Pre-signed URLs
- Cost-effective

**Search Engine** (Future): Elasticsearch
- Full-text search
- Analytics
- Log aggregation

### 9.4 Processing and Analysis

**Code Analysis Tools**:

For JavaScript/TypeScript:
- ESLint for linting
- TypeScript compiler for type checking
- Complexity-report for metrics
- Istanbul for coverage

For Python:
- Pylint for linting
- Radon for complexity
- Bandit for security
- Coverage.py for coverage

For Java:
- Checkstyle for style
- PMD for code quality
- SpotBugs for bugs
- JaCoCo for coverage

**PDF Processing**:
- pdf-parse (Node.js)
- PyPDF2 or pdfplumber (Python)

**AI/ML Services**:
- OpenAI API (GPT-4)
- Alternative: Anthropic Claude
- Fallback: Open-source models (Llama, Mistral)

**Report Generation**:
- PDFKit (Node.js)
- ReportLab (Python)
- Puppeteer for HTML to PDF

### 9.5 Infrastructure and DevOps

**Cloud Provider**: AWS (Primary recommendation)
- Comprehensive service offering
- Global infrastructure
- Mature ecosystem

**Alternative**: Google Cloud Platform or Azure

**Compute**:
- AWS ECS or EKS for containers
- EC2 for VMs
- Lambda for serverless functions

**Container Orchestration**: Kubernetes or AWS ECS
- Service discovery
- Auto-scaling
- Health checks
- Rolling deployments

**CI/CD**: GitHub Actions or GitLab CI
- Automated testing
- Automated deployments
- Environment management

**Infrastructure as Code**: Terraform
- Multi-cloud support
- Version control
- Reproducible infrastructure

**Monitoring and Logging**:
- DataDog or New Relic for APM
- CloudWatch for AWS metrics
- Sentry for error tracking
- ELK Stack for log aggregation

**Message Queue**: Redis or RabbitMQ
- Job processing
- Event-driven architecture
- Reliable message delivery

### 9.6 Security Tools

**Authentication**: Passport.js or Auth0
- Multiple strategies
- OAuth integration
- Session management

**Secrets Management**: AWS Secrets Manager or HashiCorp Vault
- Encrypted storage
- Automatic rotation
- Audit logging

**Security Scanning**:
- Snyk for dependency scanning
- OWASP ZAP for penetration testing
- SonarQube for code security

### 9.7 Development Tools

**Version Control**: Git with GitHub
- Code review (Pull Requests)
- Issue tracking
- CI/CD integration

**Code Quality**:
- ESLint/Prettier for formatting
- Husky for git hooks
- Commitlint for commit messages

**Documentation**:
- JSDoc or TypeDoc for code
- Swagger for API
- Storybook for UI components

**Project Management**:
- Jira or Linear for tasks
- Confluence for documentation
- Slack for communication

### 9.8 Recommended Tech Stack Summary

```
┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                        │
│  React 18 + TypeScript + Redux + Material-UI + Vite    │
└─────────────────────────────────────────────────────────┘
                           │
                           │ HTTPS/REST
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    API GATEWAY                           │
│              AWS API Gateway + WAF                       │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   BACKEND LAYER                          │
│     Node.js 18 + Express + TypeScript + Passport.js    │
└─────────────────────────────────────────────────────────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│  PostgreSQL  │  │    Redis     │  │   AWS S3     │
│      14      │  │      7       │  │              │
└──────────────┘  └──────────────┘  └──────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                 PROCESSING LAYER                         │
│  Worker Nodes + ESLint + Pylint + OpenAI API + PDFKit  │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                 INFRASTRUCTURE                           │
│   AWS ECS + CloudWatch + DataDog + GitHub Actions      │
└─────────────────────────────────────────────────────────┘
```

---

## 10. Deployment Architecture

### 10.1 Environment Strategy

**Environments**:

1. **Development**
   - Local development machines
   - Docker Compose for services
   - Mock external services
   - Hot reload enabled

2. **Staging**
   - Production-like environment
   - Real external services (test accounts)
   - Performance testing
   - UAT (User Acceptance Testing)

3. **Production**
   - High availability setup
   - Auto-scaling enabled
   - Full monitoring and alerting
   - Backup and disaster recovery

### 10.2 Container Architecture

**Docker Compose Structure** (Development):

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:4000
    volumes:
      - ./frontend:/app
      - /app/node_modules

  backend:
    build: ./backend
    ports:
      - "4000:4000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/devanalyzer
      - REDIS_URL=redis://redis:6379
      - GITHUB_CLIENT_ID=${GITHUB_CLIENT_ID}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      - db
      - redis
    volumes:
      - ./backend:/app
      - /app/node_modules

  worker:
    build: ./backend
    command: npm run worker
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/devanalyzer
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis

  db:
    image: postgres:14
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=devanalyzer
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### 10.3 Production Deployment

**AWS ECS Architecture**:

```
┌─────────────────────────────────────────────────────────┐
│                    Route 53 (DNS)                        │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              CloudFront (CDN)                            │
│         - Static assets caching                          │
│         - SSL/TLS termination                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│        Application Load Balancer (ALB)                   │
│         - Health checks                                  │
│         - SSL termination                                │
│         - Path-based routing                             │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
┌──────────────┐          ┌──────────────┐
│  ECS Service │          │  ECS Service │
│  (Frontend)  │          │  (Backend)   │
│              │          │              │
│  Tasks: 2-10 │          │  Tasks: 2-20 │
└──────────────┘          └──────┬───────┘
                                 │
                    ┌────────────┼────────────┐
                    ▼            ▼            ▼
            ┌──────────┐  ┌──────────┐  ┌──────────┐
            │   RDS    │  │  Redis   │  │    S3    │
            │PostgreSQL│  │ ElastiC. │  │  Bucket  │
            │Multi-AZ  │  │          │  │          │
            └──────────┘  └──────────┘  └──────────┘
```

### 10.4 CI/CD Pipeline

**GitHub Actions Workflow**:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run linter
        run: npm run lint
      - name: Run tests
        run: npm test
      - name: Run security scan
        run: npm audit

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker image
        run: docker build -t app:${{ github.sha }} .
      - name: Push to ECR
        run: |
          aws ecr get-login-password | docker login --username AWS --password-stdin
          docker push app:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster prod --service api --force-new-deployment
```

---

## 11. Monitoring and Observability

### 11.1 Key Metrics

**Application Metrics**:
- Request rate (requests/second)
- Error rate (errors/total requests)
- Response time (p50, p95, p99)
- Analysis completion time
- Queue depth and processing rate

**Business Metrics**:
- Daily active users
- Analyses created per day
- Analysis success rate
- Report downloads
- User retention rate

**Infrastructure Metrics**:
- CPU utilization
- Memory usage
- Disk I/O
- Network throughput
- Database connections

### 11.2 Logging Strategy

**Log Levels and Use Cases**:
```javascript
// ERROR - System failures
logger.error('Failed to process analysis', {
  analysisId,
  error: error.message,
  stack: error.stack
});

// WARN - Recoverable issues
logger.warn('GitHub API rate limit approaching', {
  remaining: rateLimitRemaining,
  resetAt: rateLimitReset
});

// INFO - Important events
logger.info('Analysis completed', {
  analysisId,
  duration: processingTime,
  userId
});

// DEBUG - Detailed information
logger.debug('Code metrics calculated', {
  analysisId,
  metrics: codeMetrics
});
```

**Structured Logging Format**:
```json
{
  "timestamp": "2026-02-15T10:30:00Z",
  "level": "INFO",
  "service": "analysis-service",
  "message": "Analysis completed",
  "context": {
    "analysisId": "uuid",
    "userId": "uuid",
    "duration": 45000,
    "repoUrl": "github.com/user/repo"
  },
  "traceId": "trace-uuid"
}
```

---

## 12. Disaster Recovery and Backup

### 12.1 Backup Strategy

**Database Backups**:
- Automated daily backups
- Point-in-time recovery (7 days)
- Cross-region replication
- Monthly backup retention (12 months)

**File Storage Backups**:
- Versioning enabled on S3
- Cross-region replication
- Lifecycle policies for old versions

### 12.2 Recovery Procedures

**RTO (Recovery Time Objective)**: 4 hours
**RPO (Recovery Point Objective)**: 1 hour

**Disaster Scenarios**:
1. Database failure → Failover to standby (< 5 minutes)
2. Region outage → Switch to backup region (< 1 hour)
3. Data corruption → Restore from backup (< 4 hours)

---

## 13. Design Decisions and Rationale

This section documents key architectural and design decisions made for the platform, with special focus on the Adaptive Learning Module.

### 13.1 Rule-Based vs. ML-Based Learner Classification

**Decision**: Use deterministic rule-based classification for learner type identification in MVP.

**Rationale**:

**Transparency and Trust**
- Users can understand exactly why they were classified into a specific learner type
- Clear explanation: "You scored 68% on quizzes and have a code complexity score of 75, which indicates a Practice-Oriented learning style"
- No "black box" decision-making
- Builds user confidence in the system

**Predictability and Consistency**
- Same inputs always produce same outputs
- No model drift over time
- Deterministic behavior for compliance and auditing
- Easy to reproduce and verify results
- Consistent user experience

**Maintainability and Iteration**
- Rules can be updated instantly without retraining
- Easy to A/B test different thresholds (e.g., 70% vs 75%)
- Simple to debug classification issues
- Clear code that any developer can understand
- Fast iteration based on user feedback

**MVP Feasibility**
- Can be implemented in 2-3 weeks vs 2-3 months for ML
- No training data collection phase required
- No model training infrastructure needed
- No GPU or specialized hardware requirements
- Lower development and operational costs
- Faster time to market

**Reduced Complexity**
- No model versioning or model registry
- No model monitoring or drift detection
- No A/B testing infrastructure for models
- No feature engineering pipeline
- No model serving infrastructure
- Simpler deployment and rollback

**Performance**
- Instant classification (< 1ms)
- No model inference latency
- No need for model caching or optimization
- Scales linearly with user base
- Minimal computational resources

**Trade-offs Accepted**:
- Less personalization than ML could provide
- Fixed thresholds may not be optimal for all users
- Cannot learn from user behavior automatically
- Limited to 4 predefined learner types
- May miss nuanced learning patterns

**Future ML Enhancement Path**:
```
Phase 1 (MVP): Rule-based classification
  ↓
Phase 2 (3-6 months): Data collection
  - Track user engagement with learning paths
  - Collect completion rates and satisfaction scores
  - Monitor which resources work best for each type
  ↓
Phase 3 (6-12 months): ML model development
  - Train classification model on collected data
  - Predict optimal learner type and adaptations
  - A/B test ML model vs rule-based system
  ↓
Phase 4 (12+ months): Hybrid approach
  - Use ML for primary classification
  - Keep rule-based as fallback
  - Continuous learning from user behavior
```

### 13.2 Diagnostic Quiz Before Learning Path

**Decision**: Require users to complete diagnostic quizzes before generating learning path.

**Rationale**:

**Accurate Classification**
- Quiz score is critical input for learner type classification
- Validates self-reported skills from resume
- Identifies knowledge gaps not visible in code
- Provides baseline for progress tracking

**User Engagement**
- Interactive experience increases engagement
- Immediate value (users learn about their knowledge gaps)
- Sets expectations for learning journey
- Gamification opportunity

**Quality Control**
- Prevents misclassification from code analysis alone
- Catches discrepancies between claimed and actual skills
- Ensures learning path matches actual knowledge level

**Trade-offs**:
- Adds 10-15 minutes to onboarding flow
- Some users may skip or rush through quizzes
- Requires quiz content creation and maintenance

**Mitigation**:
- Keep quizzes short (5-10 questions per skill)
- Make quizzes optional but highly recommended
- Allow retakes to reduce anxiety
- Provide immediate feedback to add value

### 13.3 Four Learner Types (Not More, Not Less)

**Decision**: Classify users into exactly 4 learner types.

**Rationale**:

**Cognitive Load**
- 4 types are easy to understand and remember
- Clear differentiation between types
- Not overwhelming for users or developers

**2x2 Matrix Design**
- Two dimensions: Quiz Score (theory) and Code Complexity (practice)
- Each dimension has binary threshold (high/low)
- Results in 4 distinct quadrants
- Mathematically elegant and explainable

**Sufficient Granularity**
- Captures major learning style differences
- Enough variation for meaningful adaptation
- Not so many that adaptations become similar

**Implementation Simplicity**
- Easy to implement and test
- Clear mapping to adaptation strategies
- Manageable number of resource type preferences

**Trade-offs**:
- May not capture all learning style nuances
- Some users may feel they don't fit perfectly
- Less personalization than continuous spectrum

**Future Enhancement**:
- Add sub-types within each category
- Use ML to identify additional patterns
- Allow users to customize their type

### 13.4 Phased Learning Path Structure

**Decision**: Organize learning paths into 4 phases based on priority.

**Rationale**:

**Clear Progression**
- Users see logical progression from critical to optional skills
- Milestone-based structure provides sense of achievement
- Easy to track progress (25%, 50%, 75%, 100%)

**Prioritization**
- Critical skills addressed first
- Ensures users focus on high-impact learning
- Aligns with career goals and role requirements

**Flexibility**
- Users can pause after any phase
- Can skip to later phases if needed
- Easy to adjust based on time constraints

**Motivation**
- Completing a phase is a significant milestone
- Provides natural break points
- Reduces overwhelm from long learning paths

**Trade-offs**:
- May feel rigid for some users
- Not all skills fit neatly into phases
- Some users may want different sequencing

### 13.5 Basic Progress Tracking (Not Gamification)

**Decision**: Implement simple progress tracking without gamification features in MVP.

**Rationale**:

**MVP Focus**
- Core value is learning, not gaming
- Reduces development complexity
- Faster time to market
- Focus on essential features

**Professional Audience**
- Developers may prefer straightforward tracking
- Avoid appearing gimmicky
- Professional tone and approach

**Data Collection**
- Simple tracking provides data for future enhancements
- Learn what metrics matter to users
- Inform gamification design in future phases

**Trade-offs**:
- May have lower engagement than gamified systems
- Misses opportunity for viral growth
- Less fun and addictive

**Future Enhancement**:
- Add achievements and badges
- Implement streaks and challenges
- Create leaderboards (optional)
- Social sharing features

### 13.6 Resource Matching (Not Creation)

**Decision**: Match users to existing learning resources rather than creating custom content.

**Rationale**:

**Leverage Existing Content**
- Thousands of high-quality courses already exist
- No need to reinvent the wheel
- Faster MVP development
- Lower content creation costs

**Curation Over Creation**
- Focus on finding and recommending best resources
- Quality filtering and rating
- Personalized matching to learning style

**Scalability**
- Can recommend resources for any technology
- Easy to add new resources
- Community can contribute recommendations

**Trade-offs**:
- Dependent on external platforms
- No control over content quality changes
- Some resources may become unavailable
- Cannot customize content to exact needs

**Future Enhancement**:
- Partner with learning platforms for exclusive content
- Create supplementary materials
- Develop platform-specific courses
- Interactive coding environments

### 13.7 PostgreSQL for All Data (Not Specialized Databases)

**Decision**: Use PostgreSQL for all data storage including learning progress.

**Rationale**:

**Simplicity**
- Single database to manage
- Reduced operational complexity
- Easier backup and recovery
- Lower infrastructure costs

**ACID Compliance**
- Ensures data consistency
- Important for progress tracking
- Reliable for user data

**Rich Feature Set**
- JSONB for flexible schemas
- Full-text search capabilities
- Array types for lists
- Mature and battle-tested

**Trade-offs**:
- May not be optimal for time-series data
- Could benefit from specialized databases later
- Potential performance bottlenecks at scale

**Future Enhancement**:
- Add Redis for real-time features
- Consider time-series DB for analytics
- Elasticsearch for advanced search

### 13.8 Synchronous Quiz, Asynchronous Path Generation

**Decision**: Quiz is synchronous (user waits), path generation is asynchronous (background job).

**Rationale**:

**Quiz (Synchronous)**
- Quick to complete (10-15 minutes)
- User expects immediate feedback
- Results needed for classification
- Better user experience

**Path Generation (Asynchronous)**
- May take 30-60 seconds
- Complex computation (resource matching, sequencing)
- User can do other things while waiting
- Prevents timeout issues

**User Experience**
- Clear progress indicators during generation
- Email notification when ready
- Can view partial results
- Reduces perceived wait time

### 13.9 PDF + JSON Export (Not Just Web View)

**Decision**: Provide both PDF and JSON export formats for learning roadmaps.

**Rationale**:

**PDF for Users**
- Offline access
- Easy to share with mentors/managers
- Professional format
- Printable for reference

**JSON for Developers**
- Programmatic access
- Integration with other tools
- API consumers
- Future automation

**Flexibility**
- Users choose preferred format
- Supports different use cases
- Future-proof design

---

## Document Control

**Version**: 1.1  
**Last Updated**: February 15, 2026  
**Status**: Design Phase  
**Next Review**: March 1, 2026  
**Approved By**: Engineering Lead, Product Manager  
**Change Summary**: Added Adaptive Learning Module architecture extension with rule-based learner classification

## Appendix

### A. Glossary

**General Terms**:
- **API Gateway**: Entry point for all API requests
- **ECS**: Elastic Container Service (AWS)
- **JWT**: JSON Web Token for authentication
- **RTO**: Recovery Time Objective
- **RPO**: Recovery Point Objective
- **WAF**: Web Application Firewall

**Adaptive Learning Terms**:
- **Skill Gap**: Difference between current skill level and target skill level required for a role
- **Gap Category**: Classification of skill gap as Beginner, Intermediate, or Advanced
- **Diagnostic Quiz**: Assessment to evaluate baseline knowledge for identified skill gaps
- **Learner Type**: Classification of learning style and pace preference (4 types)
- **Fast-Paced Advanced Learner**: High quiz score + high code complexity; learns quickly with minimal guidance
- **Conceptual Deep Learner**: High quiz score + low code complexity; prefers theory-first approach
- **Practice-Oriented Learner**: Low quiz score + high code complexity; learns by doing
- **Guided Step-by-Step Learner**: Low quiz score + low code complexity; needs structured guidance
- **Rule-Based Classification**: Deterministic logic using explicit conditional rules (no ML training)
- **Adaptive Pace**: Dynamic adjustment of content depth, progression speed, and difficulty
- **Learning Path**: Structured sequence of modules and resources designed to bridge skill gaps
- **Learning Roadmap**: Comprehensive document containing learning path, timeline, and resources
- **Content Depth**: Level of detail in learning materials (basic to advanced)
- **Progression Speed**: Rate at which user moves through learning content (0.75x to 1.5x)
- **Difficulty Curve**: Rate of difficulty increase throughout learning path (gentle to steep)
- **Learning Module**: Individual unit of learning within a phase
- **Learning Phase**: Group of related modules (typically 4 phases per path)
- **Code Complexity Score**: Metric derived from code analysis (cyclomatic complexity, organization, patterns)

### B. References

- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [GitHub REST API Documentation](https://docs.github.com/en/rest)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [React Documentation](https://react.dev/)

### C. Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-15 | Engineering Team | Initial design document |
| 1.1 | 2026-02-15 | Engineering Team | Added Adaptive Learning Module |

### D. Adaptive Learning Module Summary

The Adaptive Learning Module transforms the platform from a one-time analysis tool into a continuous learning companion. Key features include:

**Skill Gap Analysis Engine**:
- Identifies missing skills and proficiency gaps
- Prioritizes gaps by severity and impact
- Compares against industry benchmarks
- Detects outdated technologies

**Learning Path Generator**:
- Creates personalized learning roadmaps
- Sequences topics with prerequisite handling
- Recommends specific resources (courses, projects, documentation)
- Estimates time requirements and sets milestones

**Adaptive Learning Engine**:
- Adjusts difficulty based on performance
- Personalizes content to learning style
- Optimizes learning pace
- Provides interventions when needed
- Predicts learning outcomes

**Progress Tracking System**:
- Records all learning activities
- Calculates completion percentages
- Tracks proficiency gains
- Maintains learning streaks
- Awards achievements
- Generates progress reports

**Data Flow**:
1. Analysis completion triggers skill gap analysis
2. Gaps are prioritized and learning path is generated
3. User engages with modules and resources
4. Activities are tracked in real-time
5. Progress is calculated and stored
6. Adaptive engine analyzes performance
7. Path is adjusted dynamically based on user behavior
8. Continuous monitoring and optimization

**Scalability Approach**:
- Client-side activity buffering
- Asynchronous progress calculations
- Materialized views for complex queries
- Multi-level caching strategy
- Database partitioning for high-volume tables
- Dedicated worker pools for different tasks
- Horizontal scaling of API and worker layers

---

**End of Design Document**
