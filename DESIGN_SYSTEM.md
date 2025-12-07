# DigiSqull Design System

A comprehensive, token-driven design system for the DigiSqull EdTech ecosystem. Built with React 18, TypeScript, Tailwind CSS v4, and shadcn/ui components.

## Overview

The design system provides a unified visual language across all platforms (web, mobile, desktop) with:

- **Color tokens** with light/dark theme support
- **Typography scale** using Poppins font family
- **Spacing system** based on a 4px grid
- **Reusable components** from primitives to patterns
- **Theme provider** for runtime theme switching
- **Accessibility-first** design with WCAG AA compliance

## Color Palette

### Primary Brand Colors

- **Primary**: #0066CC (Vivid Blue) → #00A8E8 (Teal in dark mode)
- **Secondary**: #FF6B35 (Coral Orange) → #FFB700 (Golden in dark mode)
- **Accent**: #00A8E8 (Teal) → #FF6B35 (Orange in dark mode)

### Status Colors

- **Success**: #10B981 (Green)
- **Warning**: #F59E0B (Amber)
- **Error**: #EF4444 (Red)
- **Info**: #3B82F6 (Blue)

### Neutrals

Light mode:
- Background: #FFFFFF
- Foreground: #0F172A (Dark slate)
- Card: #F8FAFC
- Muted: #E2E8F0

Dark mode:
- Background: #0F172A (Deep navy)
- Foreground: #F1F5F9 (Near white)
- Card: #1E293B
- Muted: #334155

## Typography

**Font Family**: Poppins (primary), Monaco (monospace)

**Font Sizes**:
- XS: 12px / 16px
- SM: 14px / 20px
- Base: 16px / 24px
- LG: 18px / 28px
- XL: 20px / 28px
- 2XL: 24px / 32px
- 3XL: 30px / 36px
- 4XL: 36px / 40px
- 5XL: 48px / 1

**Font Weights**:
- Light: 300
- Normal: 400
- Medium: 500
- Semibold: 600
- Bold: 700
- Extrabold: 800

## Spacing

Based on 4px grid:
- 0: 0
- 1: 4px
- 2: 8px
- 3: 12px
- 4: 16px
- 5: 20px
- 6: 24px
- 8: 32px
- 12: 48px
- 16: 64px

## Components

### Primitives (from shadcn/ui)

- Button
- Input
- Card
- Badge
- Avatar
- Tooltip
- Dropdown Menu
- Dialog
- Select
- Textarea
- Progress
- Skeleton
- Toast

### Layouts

- **Header**: Top navigation with title, subtitle, and actions
- **PageShell**: Main layout with optional sidebar
- **Grid**: Responsive grid component with configurable columns

### Patterns

- **CourseCard**: Course listing with progress tracking
- **VideoCard**: Video marketplace card with playback info
- **RouteCard**: Transport route with scheduling and availability
- **LeaderboardItem**: Ranking display with points and badges
- **GamificationBadge**: Achievement badges with rarity levels
- **StudentHeader**: Contextual header for student dashboards

## Usage

### Basic Component Usage

\`\`\`tsx
import { Button } from "@/components/ui/button"
import { Card } from "@/components/ui/card"
import { CourseCard } from "@/components/patterns/course-card"

export function MyComponent() {
  return (
    <div className="space-y-4">
      <Card>
        <h2 className="text-2xl font-bold">My Courses</h2>
      </Card>

      <CourseCard
        id="1"
        title="React Fundamentals"
        instructor="John Doe"
        progress={50}
        level="beginner"
      />

      <Button onClick={() => alert("Clicked!")}>Enroll Now</Button>
    </div>
  )
}
\`\`\`

### Theme Switching

\`\`\`tsx
import { useTheme } from "@/lib/theme-context"

export function ThemeToggle() {
  const { theme, setTheme, resolvedTheme } = useTheme()

  return (
    <button
      onClick={() => setTheme(resolvedTheme === "dark" ? "light" : "dark")}
    >
      Toggle {resolvedTheme === "dark" ? "Light" : "Dark"} Mode
    </button>
  )
}
\`\`\`

### Token Access

Design tokens are available as CSS variables:

\`\`\`css
.custom-component {
  color: var(--primary);
  background: var(--background);
  border: 1px solid var(--border);
}
\`\`\`

## Storybook

Run Storybook to browse all components and patterns:

\`\`\`bash
npm run storybook
\`\`\`

This opens a development environment at `http://localhost:6006` where you can:
- View all components in isolation
- Test different states and variations
- Read documentation for each component
- See accessibility annotations

## React Native Implementation

For React Native projects, use the design tokens JSON file:

\`\`\`tsx
// React Native using NativeWind or Tamagui
import designTokens from "@/lib/design-tokens.json"

const colors = {
  primary: designTokens.colors.primary,
  secondary: designTokens.colors.secondary,
  // ... map remaining tokens
}
\`\`\`

See `lib/rn-theme-mapping.ts` for a complete React Native theme implementation.

## Accessibility

All components follow WCAG AA guidelines:

- **Color Contrast**: All text/background combinations meet 4.5:1 ratio
- **Keyboard Navigation**: All interactive elements are keyboard accessible
- **Screen Reader Support**: ARIA labels and semantic HTML throughout
- **Focus Management**: Clear focus indicators with `--ring` color

## Theming Integration

### Using in API Gateway/Microservices

Export token values for API responses:

\`\`\`ts
import { designTokens } from "@/lib/design-tokens"

export const themeConfig = {
  brand: designTokens.colors.primary,
  palette: designTokens.colors,
  spacing: designTokens.spacing,
}
\`\`\`

### Adapters for External Services

Create mock adapters in `lib/adapters/`:

- `payment-adapter.ts`: Stripe, M-Pesa, Flutterwave placeholders
- `video-adapter.ts`: MinIO, HLS streaming placeholders
- `storage-adapter.ts`: File upload service wrappers

See `Architecture` section for more details.

## Design Principles

1. **Consistency**: Use tokens, not magic numbers
2. **Accessibility**: WCAG AA minimum, keyboard-first
3. **Performance**: Minimal animations, optimized images
4. **Clarity**: Clear hierarchy, semantic components
5. **Flexibility**: Dark mode, responsive, customizable

## Next Steps

After design system approval:

1. **Web Dashboard** (Student or Teacher)
2. **Mobile Apps** (React Native with theme mapping)
3. **Backend Services** (Microservices with Dockercompose)
4. **Real Integrations** (Stripe, Firebase, MinIO)

---

**Version**: 1.0  
**Last Updated**: December 2024  
**Maintained by**: DigiSqull Design System Team
