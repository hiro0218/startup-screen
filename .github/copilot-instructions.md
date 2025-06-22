# GitHub Copilot Instructions - Web Memo App

## Project Overview
This is a web-based memo application with URL shortcuts functionality, similar to Chrome's start page. The app combines note-taking with bookmark management.

## Tech Stack
- **Language**: TypeScript (strict mode)
- **Framework**: React 19 with functional components and hooks
- **Styling**: Panda CSS (template literal syntax only)
- **Build Tool**: Vite
- **Testing**: Vitest
- **Linting**: Biome
- **Formatting**: Prettier
- **Git Hooks**: Husky + nano-staged (pre-commit linting/formatting)
- **Deployment**: GitHub Pages via GitHub Actions
- **Storage**: localStorage for client-side data persistence

## Architecture Patterns
- Use custom hooks for data management (`useLocalStorage`, `useMemo`, `useShortcuts`)
- Implement compound components for complex UI sections
- Use TypeScript interfaces for all data structures
- Follow React best practices: memo, useMemo, useCallback for optimization

## Code Style Preferences
- **Type Definitions**: Always use `type` instead of `interface`
- Use descriptive variable names in Japanese context when appropriate
- Prefer `const` over `let`, avoid `var`
- Use optional chaining (`?.`) and nullish coalescing (`??`)
- Implement proper error boundaries
- Use TypeScript strict mode with proper type annotations
- Follow Biome linting rules
- Code will be auto-formatted with Prettier on pre-commit

## Component Structure
```typescript
// Standard component template - use 'type' not 'interface'
type ComponentProps = {
  // Define all props with proper types
  children?: React.ReactNode;
  className?: string;
};

export const Component: React.FC<ComponentProps> = ({
  // Destructure props
}) => {
  // Hooks first
  // Event handlers
  // Render logic
  
  const containerClass = css`
    display: flex;
    flex-direction: column;
    gap: var(--spacing-4);
  `;
  
  return (
    <div className={containerClass}>
      {/* JSX content */}
    </div>
  );
};
```

## Data Models
```typescript
type Memo = {
  id: string;
  content: string;
  updatedAt: Date;
  createdAt: Date;
};

type Shortcut = {
  id: string;
  name: string;
  url: string;
  favicon?: string;
  category?: string;
  order: number;
  createdAt: Date;
};

type AppState = {
  activeTab: 'memo' | 'shortcuts';
  memos: Memo[];
  shortcuts: Shortcut[];
  isLoading: boolean;
};
```

## Feature Requirements

### Core Memo Features
- Large textarea with auto-resize
- Real-time auto-save (debounced)
- Character count display
- Clear functionality with confirmation
- Restore from localStorage on load

### Shortcut Management
- Add/edit/delete shortcuts
- Drag & drop reordering
- Favicon fetching: `https://www.google.com/s2/favicons?domain=${domain}`
- Grid layout (responsive: 4-6 items per row)
- Category grouping (optional)
- Search/filter functionality

### UI/UX Guidelines
- Clean, minimal design
- Smooth transitions and animations
- Mobile-first responsive design
- Accessible (ARIA labels, keyboard navigation)
- Loading states and error handling
- Toast notifications for user feedback

## Panda CSS Usage
**IMPORTANT**: Always use template literal syntax with `css` function, not object syntax.

```typescript
import { css } from '../styled-system/css';

// ✅ Correct: Template literal syntax
const containerClass = css`
  max-width: 1200px;
  margin: 0 auto;
  padding: var(--spacing-4);
`;

const gridClass = css`
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: var(--spacing-4);
  
  @media (min-width: 768px) {
    grid-template-columns: repeat(4, 1fr);
  }
  
  @media (min-width: 1024px) {
    grid-template-columns: repeat(6, 1fr);
  }
`;

// ❌ Incorrect: Object syntax (don't use this)
// const wrongStyle = css({ maxWidth: '1200px' });
```

### Template Literal Patterns:
```typescript
// Conditional styling
const buttonClass = css`
  background: var(--colors-blue-500);
  padding: var(--spacing-2) var(--spacing-4);
  border-radius: var(--radii-md);
  
  &:hover {
    background: var(--colors-blue-600);
  }
  
  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
`;

// Responsive design
const responsiveClass = css`
  font-size: var(--fontSizes-sm);
  
  @media (min-width: 640px) {
    font-size: var(--fontSizes-md);
  }
  
  @media (min-width: 1024px) {
    font-size: var(--fontSizes-lg);
  }
`;
```

## File Organization
```
project-root/
├── .github/workflows/deploy.yml
├── .husky/                 # Git hooks
│   └── pre-commit
├── src/
│   ├── components/
│   │   ├── common/         # Reusable UI components
│   │   ├── memo/          # Memo-related components
│   │   └── shortcuts/     # Shortcut-related components
│   ├── hooks/             # Custom hooks
│   ├── utils/             # Utility functions
│   ├── types/             # TypeScript type definitions
│   ├── constants/         # App constants
│   └── styled-system/     # Generated Panda CSS files
├── __tests__/             # Vitest test files
├── biome.json             # Biome configuration
├── .prettierrc            # Prettier configuration
├── panda.config.ts
├── vite.config.ts
├── vitest.config.ts
├── package.json
└── README.md
```

## Development Guidelines

### When implementing features:
1. Start with TypeScript type definitions (use `type`, not `interface`)
2. Create custom hooks for data logic
3. Build components with proper error handling
4. Add loading states and user feedback
5. Implement keyboard shortcuts where appropriate
6. Ensure mobile responsiveness
7. Write Vitest tests for components and hooks
8. Follow Biome linting rules (will be enforced on pre-commit)

### Error Handling Pattern:
```typescript
type ErrorState = {
  message: string;
  code?: string;
};

const [error, setError] = useState<ErrorState | null>(null);

try {
  // Operation
} catch (err) {
  setError({
    message: err instanceof Error ? err.message : 'Unknown error',
    code: 'OPERATION_FAILED'
  });
  console.error('Operation failed:', err);
}
```

### Local Storage Pattern:
```typescript
type UseLocalStorageReturn<T> = [T, (value: T) => void, () => void];

const useLocalStorage = <T>(key: string, initialValue: T): UseLocalStorageReturn<T> => {
  // Implement with proper error handling and serialization
};
```

## GitHub Actions & Deployment
- Auto-deploy on push to main branch
- Build with `npm run build`
- Deploy to GitHub Pages from `/dist` folder
- Set proper `base` in vite.config.ts for GitHub Pages

## Performance Considerations
- Debounce auto-save (300ms)
- Virtualize long lists if needed
- Lazy load favicon images
- Use React.memo for expensive components
- Implement proper loading states

## Testing Strategy
- **Framework**: Vitest for unit and integration tests
- Unit tests for utilities and hooks
- Component testing with React Testing Library
- E2E tests for critical user flows
- Test localStorage functionality
- Test responsive behavior
- Aim for 80%+ test coverage

### Test File Patterns:
```typescript
// __tests__/components/MemoApp.test.tsx
import { describe, it, expect, beforeEach } from 'vitest';
import { render, screen } from '@testing-library/react';
import { MemoApp } from '../src/components/MemoApp';

describe('MemoApp', () => {
  beforeEach(() => {
    localStorage.clear();
  });

  it('should render memo textarea', () => {
    render(<MemoApp />);
    expect(screen.getByRole('textbox')).toBeInTheDocument();
  });
});
```

## Accessibility Requirements
- Proper ARIA labels
- Keyboard navigation support
- Screen reader compatibility
- Sufficient color contrast
- Focus management

## Browser Compatibility
- **Target**: Modern browsers only (ES2022+)
- Chrome/Edge 108+
- Firefox 108+
- Safari 16+
- Mobile Safari iOS 16+
- **No support for**: Internet Explorer, legacy browsers

## Common Patterns to Suggest
- Debounced input handlers
- Optimistic UI updates
- Compound components for complex forms
- Custom hooks for localStorage operations
- Error boundary components
- Loading skeleton components

## Don't Suggest
- **`interface` declarations** (always use `type`)
- Class components
- Object syntax for Panda CSS (always use template literals)
- Inline styles (use Panda CSS template literals)
- Direct DOM manipulation
- Synchronous localStorage operations
- Hardcoded strings (use constants)
- Manual formatting (Prettier handles this)
- Linting rule violations (Biome will catch these)
- Any external dependencies not in tech stack
- Legacy browser polyfills or workarounds
