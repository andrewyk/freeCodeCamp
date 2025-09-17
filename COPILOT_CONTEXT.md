# freeCodeCamp.org Codebase Context for GitHub Copilot

## 🎯 **Project Overview**
freeCodeCamp.org is a non-profit open-source platform providing free coding education to millions of learners worldwide. The codebase contains a full-stack web application with curriculum management, user authentication, progress tracking, and certification systems.

**Key Stats:**
- 13,589+ coding challenges across 20+ certifications
- 100,000+ developers have gotten their first job through freeCodeCamp
- Full-stack curriculum covering web development, data science, and more
- Multi-language support with active translation community

## 🏗️ **Architecture Overview**

### **Monorepo Structure**
```
/workspaces/freeCodeCamp/
├── api/                    # Backend API (Fastify + MongoDB + Prisma)
├── client/                 # Frontend SPA (React + Gatsby + Redux)
├── curriculum/             # Educational content (13,589+ challenges)
├── shared/                 # Shared utilities and TypeScript types
├── tools/                  # Development and maintenance tools
├── e2e/                    # End-to-end Playwright tests
└── docker/                 # Docker configurations for development
```

### **Technology Stack**

#### **Frontend (client/)**
- **Framework**: React 17.0.2 with Gatsby 3.15.0 for SSG
- **State Management**: Redux 4.2.1 + Redux-Saga 1.2.3
- **Styling**: Tailwind CSS + Custom @freecodecamp/ui components
- **Code Editor**: Monaco Editor 0.33.0 for in-browser coding
- **Build**: Webpack via Gatsby, pnpm workspaces
- **Type Safety**: TypeScript 5.7.3

#### **Backend (api/)**
- **Framework**: Fastify 5.2.0 (high-performance Node.js framework)
- **Database**: MongoDB with Prisma ORM 5.5.2
- **Authentication**: OAuth2 via Auth0 + JWT tokens
- **Validation**: TypeBox + Ajv for schema validation
- **Logging**: Pino with Sentry integration
- **Testing**: Vitest with MSW for API mocking

#### **Curriculum System (curriculum/)**
- **Format**: Markdown files with YAML frontmatter
- **Challenges**: 13,589+ challenges across 20+ challenge types
- **Structure**: Certifications → Superblocks → Blocks → Challenges
- **Build**: Custom Node.js processing pipeline
- **i18n**: Multi-language support via Crowdin

## 🔧 **Development Environment**

### **Package Management**
- **Tool**: pnpm (>=10) with workspaces
- **Node**: >=22 required
- **Monorepo**: 14 packages in pnpm-workspace.yaml

### **Key Scripts**
```bash
pnpm run develop          # Start development servers
pnpm run build           # Build all packages
pnpm run test            # Run all tests
pnpm run lint            # ESLint + TypeScript + Prettier
pnpm run seed            # Seed development database
pnpm run clean           # Clean build artifacts
```

### **Development URLs**
- Client: http://localhost:8000
- API: http://localhost:3000
- API Docs: http://localhost:3000/docs
- MailHog: http://localhost:8025

## 📁 **Key Directories & Patterns**

### **Client Architecture (client/src/)**
```
src/
├── components/             # Reusable React components
├── pages/                 # Gatsby page routes
├── templates/             # Dynamic page templates
├── redux/                 # State management (actions, sagas, reducers)
├── utils/                 # Helper functions
└── client-only-routes/    # SPA-only routes
```

**Key Components:**
- `components/Map/` - Challenge navigation
- `components/settings/` - User preferences
- `templates/Challenges/` - Interactive coding environment
- `redux/` - 29 files managing app state with sagas

### **API Architecture (api/src/)**
```
src/
├── routes/                # Fastify route handlers
├── plugins/               # Fastify plugins (auth, cors, etc.)
├── schemas/               # TypeBox validation schemas
├── utils/                 # Helper utilities
└── db/                    # Database utilities
```

**Key Features:**
- OAuth2 authentication with multiple providers
- RESTful API with OpenAPI/Swagger docs
- CSRF protection and rate limiting
- Stripe/PayPal payment integration

### **Curriculum Structure (curriculum/)**
```
curriculum/
├── challenges/english/
│   ├── certifications/    # 20+ certification definitions
│   └── blocks/           # Challenge groupings
├── structure/            # Metadata and organization
└── schema/              # Validation schemas
```

**Challenge Types (0-20):**
- 0: HTML/CSS challenges
- 1: JavaScript challenges
- 6: Front-end projects
- 7: Back-end projects
- 11: Python challenges
- 12: Database challenges
- 16: Lab challenges
- 17: Quiz challenges

## 🧪 **Testing Strategy**

### **Test Frameworks**
- **Jest**: Legacy unit tests
- **Vitest**: Modern unit testing (migration in progress)
- **Testing Library**: React component testing
- **Playwright**: E2E browser testing
- **Supertest**: API integration testing

### **Test Commands**
```bash
pnpm run test                    # All tests
pnpm run test:api               # API tests
pnpm run test:client            # Client tests
pnpm run test:curriculum        # Curriculum validation
pnpm run playwright:run         # E2E tests
```

## 🔒 **Security & Authentication**

### **Auth Flow**
1. OAuth2 via Auth0 (GitHub, Google, Facebook, Apple)
2. JWT token generation and validation
3. Session management with secure cookies
4. CSRF protection on all API endpoints

### **Environment Variables**
```bash
# Core
MONGOHQ_URL=mongodb://localhost:27017/freecodecamp
SESSION_SECRET=secure-session-secret
JWT_SECRET=jwt-signing-secret

# Auth0
AUTH0_CLIENT_ID=client-id
AUTH0_CLIENT_SECRET=client-secret
AUTH0_DOMAIN=domain.auth0.com

# External Services
STRIPE_SECRET_KEY=sk_...
ALGOLIA_APP_ID=app-id
SENTRY_DSN=sentry-dsn
```

## 📊 **Database Schema (Prisma)**

### **Key Models**
```typescript
// User model
model User {
  id                    String @id @default(auto()) @map("_id") @db.ObjectId
  email                 String @unique
  username              String? @unique
  completedChallenges   CompletedChallenge[]
  progressTimestamps    Int[]
  points                Int @default(1)
  theme                 String @default("default")
  // ... additional fields
}

// Challenge completion tracking
model CompletedChallenge {
  id                    String @id @default(auto()) @map("_id") @db.ObjectId
  userId                String @db.ObjectId
  challengeId           String
  challengeType         Int
  solution              String?
  completedDate         DateTime @default(now())
  user                  User @relation(fields: [userId], references: [id])
}
```

## 🎨 **UI/UX Patterns**

### **Design System**
- Custom `@freecodecamp/ui` component library
- Tailwind CSS for utility styling
- Responsive design (mobile-first)
- Dark/light theme support
- WCAG 2.1 AA accessibility compliance

### **State Management Patterns**
```typescript
// Redux store structure
interface AppState {
  app: {
    user: UserState;
    superBlocks: SuperBlock[];
    curriculum: CurriculumState;
    flashMessage: FlashMessageState;
  };
  challenge: {
    challengeData: Challenge;
    challengeFiles: ChallengeFile[];
    output: string[];
    isSubmitting: boolean;
  };
}
```

## 🌍 **Internationalization (i18n)**

### **Translation System**
- **Primary Language**: English
- **Translation Platform**: Crowdin
- **Supported Languages**: 30+ languages
- **Client i18n**: react-i18next
- **Curriculum**: Separate challenge files per language

## 🚀 **Performance Optimizations**

### **Frontend**
- Static site generation with Gatsby
- Code splitting and lazy loading
- Bundle analysis and optimization
- Image optimization (WebP, lazy loading)
- Service worker for offline support

### **Backend**
- MongoDB connection pooling
- Query optimization with Prisma
- Response compression and caching
- Rate limiting and DDOS protection

## 🔍 **Search & Analytics**

### **Search Integration**
- **Algolia**: Challenge and content search
- **InstantSearch**: React search components
- **Indexing**: Automated curriculum indexing

### **Analytics & A/B Testing**
- **Growth Book**: Feature flags and A/B testing
- **Sentry**: Error tracking and performance monitoring
- **Custom Analytics**: User progress and completion tracking

## 📝 **Code Quality & Standards**

### **Linting & Formatting**
```javascript
// ESLint config (eslint.config.mjs)
export default tseslint.config(
  js.configs.recommended,
  ...tseslint.configs.recommended,
  reactPlugin.configs.flat.recommended,
  {
    rules: {
      'no-console': 'warn',
      'prefer-const': 'error',
      '@typescript-eslint/no-unused-vars': 'error'
    }
  }
);
```

### **Git Workflow**
- **Branches**: main (production), feature/, fix/, docs/
- **Commits**: Conventional commit format
- **Pre-commit**: Husky + lint-staged
- **CI/CD**: GitHub Actions with comprehensive testing

## 🛠️ **Development Tools**

### **Challenge Development**
- **Challenge Editor**: Web-based challenge creation (tools/challenge-editor/)
- **Helper Scripts**: CLI tools for challenge management
- **Validation**: Automated schema validation
- **Testing**: Solution validation and test coverage

### **Debugging**
- **VS Code**: Recommended extensions and settings
- **DevTools**: Redux DevTools integration
- **Logging**: Structured logging with correlation IDs
- **Monitoring**: Application health checks

## 🌐 **External Integrations**

### **Payment Processing**
- **Stripe**: Primary donation processing
- **PayPal**: Alternative payment method
- **Webhook Security**: Signature verification

### **Communication**
- **AWS SES**: Production email delivery
- **Nodemailer**: Development email testing
- **MailHog**: Local SMTP testing

### **Content Delivery**
- **CDN**: Static asset delivery
- **Challenge Tests**: CDN-delivered test suites
- **Image Optimization**: Automated image processing

## 📋 **Common Patterns & Conventions**

### **File Naming**
- **React Components**: PascalCase (`UserProfile.tsx`)
- **Utilities**: camelCase (`formatDate.js`)
- **Constants**: UPPER_SNAKE_CASE (`API_ENDPOINTS.ts`)
- **Challenges**: kebab-case (`learn-html-by-building-a-cat-photo-app.md`)

### **Import Patterns**
```typescript
// External libraries first
import React from 'react';
import { useSelector } from 'react-redux';

// Internal imports
import { Button } from '@freecodecamp/ui';
import { selectUser } from '../redux/selectors';
import { formatDate } from '../utils/date-utils';
```

### **Component Patterns**
```typescript
// Functional components with hooks
interface Props {
  userId: string;
  onSubmit: (data: FormData) => void;
}

const UserForm: React.FC<Props> = ({ userId, onSubmit }) => {
  const user = useSelector(selectUser);
  
  return (
    <form onSubmit={onSubmit}>
      {/* Component JSX */}
    </form>
  );
};
```

## 🎯 **Key Features for Copilot Understanding**

### **Challenge System**
- Interactive coding environment with Monaco Editor
- Real-time test execution in web workers
- Solution validation and progress tracking
- Multi-file challenge support

### **Certification Flow**
- Progressive skill building through 20+ certifications
- Project-based learning with portfolio building
- Automated certificate generation and verification
- Social sharing and LinkedIn integration

### **Community Features**
- User profiles and portfolios
- Forum integration for help and discussion
- Social authentication and networking
- Contribution tracking and recognition

This context provides Copilot with comprehensive understanding of the freeCodeCamp codebase architecture, patterns, and conventions for optimal code assistance and suggestions.