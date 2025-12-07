# DigiSqull - Design System & Platform Architecture

A comprehensive, production-grade EdTech ecosystem built with modern web technologies and designed for African schools.

## Project Overview

DigiSqull is an AI-powered learning management system that includes:

- **Learning Management**: Video marketplace, courses, assignments, attendance, grades
- **Gamification**: Points, badges, streaks, leaderboards
- **Transport Management**: Real-time GPS tracking, route optimization, driver management
- **Financial Management**: Fee collection, earnings tracking, payment integration
- **Communication**: Real-time chat, notifications, announcements
- **AI Services**: Recommendations, content analysis, career suggestions

## Technology Stack

### Frontend (Web)

- **Framework**: Next.js 16 with App Router
- **Language**: TypeScript
- **Styling**: Tailwind CSS v4
- **Components**: shadcn/ui
- **State Management**: React Hooks + Context
- **Theme**: Custom theme provider with light/dark mode
- **Build Tool**: Turbopack (default in Next.js 16)

### Frontend (Mobile)

- **Framework**: React Native
- **Navigation**: React Navigation
- **State**: Redux Toolkit
- **Offline**: AsyncStorage + Background Sync
- **UI**: NativeWind or Tamagui for theming

### Backend

- **Runtime**: Node.js v18+
- **Framework**: Express.js
- **Language**: TypeScript
- **Database**: PostgreSQL with Drizzle ORM
- **Authentication**: JWT + OAuth2 + MFA
- **Real-time**: Socket.io + Redis
- **Storage**: MinIO (S3-compatible)
- **Cache**: Redis
- **API Gateway**: Nginx

### Infrastructure

- **Containerization**: Docker & Docker Compose
- **Orchestration**: Kubernetes (future)
- **CI/CD**: GitHub Actions
- **Monitoring**: TBD (Grafana, Prometheus)

## Project Structure

\`\`\`
digisquall/
├── apps/
│   ├── web/                 # Next.js web application
│   │   ├── app/            # App Router pages
│   │   ├── components/     # React components
│   │   ├── lib/            # Utilities and hooks
│   │   └── public/         # Static assets
│   ├── mobile/             # React Native app
│   │   ├── src/
│   │   ├── screens/
│   │   └── navigation/
│   └── storybook/          # Component library docs
├── packages/
│   ├── design-system/      # Shared design tokens
│   ├── database/           # Drizzle schemas & migrations
│   └── shared-types/       # TypeScript types
├── services/               # Microservices
│   ├── auth-service/       # Authentication & authorization
│   ├── user-service/       # User management
│   ├── video-service/      # Video hosting & streaming
│   ├── course-service/     # Course management
│   ├── payment-service/    # Payment processing
│   ├── transport-service/  # GPS & route tracking
│   ├── ai-service/         # AI/ML endpoints
│   ├── socket-service/     # Real-time communications
│   └── moderation-service/ # Content moderation
├── infrastructure/
│   ├── docker-compose.yml  # Local development stack
│   ├── nginx.conf          # API Gateway
│   └── kubernetes/         # K8s manifests (future)
└── docs/                   # Documentation

\`\`\`

## Getting Started

### Prerequisites

- Node.js 18+ and npm/pnpm
- Docker & Docker Compose
- PostgreSQL 14+ (or use Docker)
- Redis 7+ (or use Docker)

### Web Development Setup

\`\`\`bash
# Clone repository
git clone <repo-url>
cd digisquall/apps/web

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Run Storybook for component development
npm run storybook
\`\`\`

Visit `http://localhost:3000` for the web app and `http://localhost:6006` for Storybook.

### Backend Setup

\`\`\`bash
cd services

# Start all microservices with Docker Compose
docker-compose up -d

# Run database migrations
npm run migrate

# Seed sample data
npm run seed
\`\`\`

Services will be available at:
- API Gateway: `http://localhost:8080`
- Auth Service: `http://localhost:3001`
- User Service: `http://localhost:3002`
- Video Service: `http://localhost:3003`
- Payment Service: `http://localhost:3004`
- Transport Service: `http://localhost:3005`
- Socket Service: `http://localhost:3006`

## Design System

The design system provides a complete, token-driven approach to styling:

### Key Features

- **Color Tokens**: Brand colors (blue-teal primary, orange-red secondary) with light/dark themes
- **Typography**: Poppins font family with comprehensive scale
- **Spacing**: 4px base grid system
- **Components**: 50+ reusable primitives and patterns
- **Themes**: Built-in light/dark mode with system preference detection
- **Accessibility**: WCAG AA compliance throughout

### Using Components

\`\`\`tsx
import { Button } from "@/components/ui/button"
import { CourseCard } from "@/components/patterns/course-card"
import { Header } from "@/components/layouts/header"

export function Dashboard() {
  return (
    <>
      <Header title="My Courses" />
      <div className="grid grid-cols-3 gap-4 p-6">
        <CourseCard
          id="1"
          title="React Fundamentals"
          instructor="John Doe"
          progress={50}
          level="beginner"
        />
      </div>
    </>
  )
}
\`\`\`

### Theme Switching

\`\`\`tsx
import { useTheme } from "@/lib/theme-context"

export function ThemeToggle() {
  const { setTheme, resolvedTheme } = useTheme()
  return (
    <button onClick={() => setTheme(resolvedTheme === "dark" ? "light" : "dark")}>
      Toggle Theme
    </button>
  )
}
\`\`\`

## API Reference

### Authentication Service

\`\`\`typescript
POST /auth/register        // Create account
POST /auth/login          // Login
POST /auth/refresh        // Refresh token
POST /auth/logout         // Logout
POST /auth/mfa/setup      // Enable MFA
POST /auth/mfa/verify     // Verify MFA code
\`\`\`

### User Service

\`\`\`typescript
GET    /users/:id         // Get user profile
PUT    /users/:id         // Update profile
GET    /users/:id/stats   // Get user statistics
\`\`\`

### Course Service

\`\`\`typescript
GET    /courses           // List courses
GET    /courses/:id       // Get course details
POST   /courses/:id/enroll   // Enroll in course
GET    /courses/:id/progress // Get progress
\`\`\`

### Payment Service

\`\`\`typescript
POST   /payments/initiate    // Initiate payment
GET    /payments/:id         // Get payment status
POST   /payments/:id/verify  // Verify payment
\`\`\`

### Transport Service

\`\`\`typescript
GET    /routes           // Get available routes
POST   /bookings         // Book a route
GET    /vehicles/:id/location  // Real-time GPS
\`\`\`

See `docs/API.md` for complete API documentation.

## Architecture Patterns

### Microservices Pattern

Each service is independently deployable:

\`\`\`typescript
// services/auth-service/src/index.ts
import express from "express"
const app = express()

app.post("/auth/register", registerHandler)
app.post("/auth/login", loginHandler)

app.listen(3001, () => console.log("Auth service running"))
\`\`\`

### Adapter Pattern for Integrations

\`\`\`typescript
// lib/adapters/payment-adapter.ts
export interface PaymentAdapter {
  charge(amount: number): Promise<{ transactionId: string }>
  verify(transactionId: string): Promise<boolean>
}

export class StripeAdapter implements PaymentAdapter {
  // Stripe implementation
}

export class MpesaAdapter implements PaymentAdapter {
  // M-Pesa implementation
}
\`\`\`

### Scalable State Management

\`\`\`typescript
// Use React Context for UI state
import { useTheme } from "@/lib/theme-context"

// Use Hooks for data fetching
import useSWR from "swr"

const { data: courses } = useSWR("/api/courses", fetch)
\`\`\`

## Database Schema

### Core Tables

- **users**: Account information and credentials
- **students**: Student-specific data
- **teachers**: Teacher profiles and qualifications
- **classes**: Class groupings and schedules
- **courses**: Course metadata and content
- **videos**: Video streaming metadata
- **assignments**: Assignment details and submissions
- **grades**: Student grades and rubrics
- **points**: Gamification points ledger
- **badges**: Achievement badges system
- **routes**: Transport routes
- **bookings**: Route bookings

See `packages/database/schema.ts` for full schema.

## Integration Examples

### Swap Payment Adapter

\`\`\`typescript
// services/payment-service/.env
PAYMENT_PROVIDER=mpesa  # or 'stripe', 'flutterwave', 'paystack'

// services/payment-service/src/adapter.ts
const PaymentProvider = PAYMENT_PROVIDER === "mpesa"
  ? new MpesaAdapter(process.env.MPESA_API_KEY)
  : new StripeAdapter(process.env.STRIPE_SECRET_KEY)
\`\`\`

### Video Streaming Setup

Replace MinIO stub with S3, Azure Blob, or GCS:

\`\`\`typescript
// lib/adapters/video-adapter.ts
const storage = process.env.STORAGE_TYPE === "s3"
  ? new S3VideoAdapter(awsConfig)
  : new MinIOAdapter(minioConfig)
\`\`\`

## Deployment

### Docker Compose (Local Development)

\`\`\`bash
cd infrastructure
docker-compose up -d
\`\`\`

### Kubernetes (Production)

\`\`\`bash
kubectl apply -f infrastructure/kubernetes/
\`\`\`

### Vercel (Web App)

\`\`\`bash
vercel deploy
\`\`\`

See `docs/DEPLOYMENT.md` for detailed deployment guides.

## Testing

\`\`\`bash
# Unit tests
npm run test

# Integration tests
npm run test:integration

# E2E tests (Playwright)
npm run test:e2e

# Coverage report
npm run test:coverage
\`\`\`

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see LICENSE file for details

## Support

- Documentation: `docs/`
- Issues: GitHub Issues
- Email: support@digisquall.com
- Community: Discord Server (link TBD)

## Roadmap

### Phase 1 (Current Sprint) ✓
- Design system complete
- Web dashboard scaffold
- Core API endpoints

### Phase 2
- Mobile app development
- Real-time features (Socket.io)
- Payment integrations

### Phase 3
- AI recommendation engine
- Advanced analytics
- Mobile app launch

### Phase 4
- Kubernetes deployment
- Scaling optimization
- Enterprise features

---

**Version**: 1.0.0  
**Last Updated**: December 2024  
**Status**: Design System Complete - Ready for Web Dashboard Development
