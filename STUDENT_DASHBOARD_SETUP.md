# Student Dashboard - Setup & Deployment Guide

## Quick Start

### 1. Environment Variables

Add these to your Vercel project's environment variables:

\`\`\`env
PAYSTACK_SECRET_KEY=sk_test_your_secret_key
NEXT_PUBLIC_APP_URL=http://localhost:3000
\`\`\`

### 2. Get Paystack Keys

1. Visit [paystack.com](https://paystack.com)
2. Sign up or log in
3. Go to Settings → API Keys & Webhooks
4. Copy your test keys
5. Add to environment variables above

### 3. Run Locally

\`\`\`bash
npm install
npm run dev
\`\`\`

Visit: http://localhost:3000/student/dashboard

## Pages Overview

| Page | URL | Purpose |
|------|-----|---------|
| Dashboard | `/student/dashboard` | Learning hub with summary |
| Courses | `/student/courses` | Browse & manage courses |
| Course Detail | `/student/courses/[courseId]` | Course view with enrollment |
| Assignments | `/student/assignments` | Track coursework |
| Videos | `/student/videos` | Video marketplace |

## Payment Testing

### Test Cards

Use these Paystack test cards:

**Successful Payment:**
- Card: `4084084084084081`
- Exp: `12/25`
- CVV: `123`

**Insufficient Funds:**
- Card: `4111111111111111`
- Exp: `12/25`
- CVV: `123`

### Test Flow

1. Navigate to `/student/courses`
2. Click a locked course (e.g., "Data Science Essentials")
3. Click "Enroll Now"
4. Enter email and click "Proceed to Payment"
5. Use test card above
6. Complete the payment
7. Verify webhook handling in console

## Architecture

### Request Flow

\`\`\`
Student Page
    ↓
Client-side (React)
    ↓
Mock API (services/)
    ↓
Mock Data (mock-data.ts)
\`\`\`

### Payment Flow

\`\`\`
Enrollment Dialog
    ↓
initiatePayment()
    ↓
Paystack API (initialize transaction)
    ↓
Paystack Checkout Page
    ↓
Post-payment verification
    ↓
Success/Failure handling
\`\`\`

## API Reference

### Student API

\`\`\`typescript
// Get dashboard summary
const summary = await getStudentDashboardSummary()
// Returns: { student, enrolledCoursesCount, upcomingAssignments, ... }

// Get learning stats
const stats = await getStudentLearningStats()
// Returns: { totalHoursSpent, lessonsCompleted, monthlyProgress, ... }
\`\`\`

### Courses API

\`\`\`typescript
// Get all courses
const courses = await getCourses()

// Filter by status
const enrolled = await getCourses('enrolled')

// Get single course
const course = await getCourseById('course_001')

// Get course with lessons
const full = await getCourseWithLessons('course_001')

// Search courses
const results = await searchCourses('React')

// Enroll in course
await enrollCourse('course_001')
\`\`\`

### Payments API

\`\`\`typescript
// Initiate payment
const payment = await initiatePayment(
  email,
  amount, // in Naira
  itemType, // 'course' or 'video'
  itemId,
  itemName
)
// Returns: { authorizationUrl, accessCode, reference }

// Check payment status
const status = await checkPaymentStatus(reference)
// Returns: { status: 'success'|'failed'|'pending', amount, customer, ... }
\`\`\`

### Assignments API

\`\`\`typescript
// Get all assignments
const assignments = await getAssignments()

// Filter by status
const pending = await getAssignmentsByStatus('pending')

// Submit assignment
await submitAssignment('assign_001', {
  content: 'My solution...',
  fileUrl: 'https://...'
})

// Get feedback
const feedback = await getSubmissionFeedback('assign_001')
\`\`\`

### Videos API

\`\`\`typescript
// Get all videos
const videos = await getVideos()

// Get purchased videos
const purchased = await getVideos('purchased')

// Get single video
const video = await getVideoById('video_001')

// Purchase video
await purchaseVideo('video_001')

// Search videos
const results = await searchVideos('JavaScript')
\`\`\`

## Webhook Setup

Paystack webhooks are currently mocked in `/app/api/paystack/webhook/route.ts`.

### To Use Real Webhooks:

1. Go to Paystack Dashboard → Settings → Webhooks
2. Add webhook URL: `https://yourdomain.com/api/paystack/webhook`
3. Enable events: `charge.success`, `charge.failed`
4. Verify signature before processing

Example handler:

\`\`\`typescript
const hash = crypto.createHmac('sha512', PAYSTACK_SECRET)
  .update(JSON.stringify(body))
  .digest('hex')

if (hash !== signature) {
  return Response.json({ error: 'Invalid signature' }, { status: 401 })
}

// Process webhook
\`\`\`

## Database Integration (Next Steps)

When ready to connect to a real database:

### 1. Update Student API

Replace mock data with database queries:

\`\`\`typescript
// Before
export async function getCourses() {
  return mockCourses
}

// After
export async function getCourses() {
  const { data } = await supabase
    .from('enrollments')
    .select('courses(*)')
    .eq('student_id', userId)
  return data
}
\`\`\`

### 2. Database Schema Required

\`\`\`sql
-- Users
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE,
  full_name TEXT,
  created_at TIMESTAMP
)

-- Courses
CREATE TABLE courses (
  id UUID PRIMARY KEY,
  title TEXT,
  description TEXT,
  instructor_id UUID REFERENCES users,
  price INTEGER,
  created_at TIMESTAMP
)

-- Enrollments
CREATE TABLE enrollments (
  id UUID PRIMARY KEY,
  student_id UUID REFERENCES users,
  course_id UUID REFERENCES courses,
  enrolled_at TIMESTAMP,
  progress REAL DEFAULT 0
)

-- Payments
CREATE TABLE payments (
  id UUID PRIMARY KEY,
  reference TEXT UNIQUE,
  student_id UUID REFERENCES users,
  amount INTEGER,
  item_type TEXT,
  item_id UUID,
  status TEXT,
  created_at TIMESTAMP
)

-- Assignments
CREATE TABLE assignments (
  id UUID PRIMARY KEY,
  course_id UUID REFERENCES courses,
  title TEXT,
  description TEXT,
  due_date TIMESTAMP,
  points INTEGER
)

-- Submissions
CREATE TABLE submissions (
  id UUID PRIMARY KEY,
  assignment_id UUID REFERENCES assignments,
  student_id UUID REFERENCES users,
  content TEXT,
  submitted_at TIMESTAMP,
  grade REAL,
  feedback TEXT
)
\`\`\`

## Deployment

### To Vercel:

1. Push code to GitHub
2. Connect repo to Vercel
3. Add environment variables in Vercel dashboard
4. Deploy

### First Deploy Checklist:

- [ ] Paystack keys added to env
- [ ] NEXT_PUBLIC_APP_URL set correctly
- [ ] All pages load without errors
- [ ] Paystack payment flow works
- [ ] Webhooks configured (if using real)
- [ ] Database connected (if ready)

## Performance Optimization

### Current (Mock)

- All APIs have simulated delays (200-500ms)
- Pages use React Server Components
- Images optimized with Next.js Image component
- Pagination limits data loads to 6 items per page

### For Production

- Add caching with revalidateTag()
- Implement SWR for client-side fetching
- Use CDN for images and videos
- Add database indexes on frequently queried fields
- Implement rate limiting on API routes

## Troubleshooting

### "Payment failed to initialize"

1. Check Paystack keys in environment
2. Verify NEXT_PUBLIC_APP_URL is correct
3. Check browser console for errors
4. Verify email is valid

### "Webhook not working"

1. Check Paystack webhook configuration
2. Verify signature verification logic
3. Check server logs for errors
4. Test with Paystack CLI: `paystack-cli webhook test`

### "Mock data not loading"

1. Restart dev server: `npm run dev`
2. Clear browser cache: `Ctrl+Shift+Delete`
3. Check console for import errors
4. Verify mock-data.ts exists

## Next Development Phases

### Phase 2: Teacher Dashboard
- Class management
- Assignment grading interface
- Student analytics
- Revenue dashboard

### Phase 3: Parent Portal
- Student progress tracking
- Grade reports
- Communication interface

### Phase 4: Admin Panel
- User management
- Course management
- Payment settlements
- System analytics

### Phase 5: Real Backend
- Microservices architecture
- Real database
- Authentication system
- Real payment processing

---

**Need Help?**
- Check `/STUDENT_DASHBOARD.md` for detailed documentation
- Review `/DESIGN_SYSTEM.md` for component usage
- Check test cards section for payment testing
- Email support or file issues on GitHub
\`\`\`

Now let me create an integration checklist:
