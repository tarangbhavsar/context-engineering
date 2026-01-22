# React + Salt Design System Project Guidelines

> **Copilot Instructions for React Applications using Salt Design System**

This project follows React best practices and utilizes J.P. Morgan's Salt Design System for UI components.

## Project Context

Refer to the following documentation:
- [Salt Design System](https://www.saltdesignsystem.com)
- [Salt Components](https://www.saltdesignsystem.com/salt/components/index)
- [Project Architecture](docs/ARCHITECTURE.md)

---

## Technology Stack

- **Framework**: React 18+ with TypeScript
- **Design System**: Salt Design System (`@salt-ds/core`, `@salt-ds/lab`)
- **State Management**: React Query + Zustand
- **Styling**: Salt CSS + CSS Modules
- **Testing**: Vitest + React Testing Library + Playwright
- **Build Tool**: Vite

---

## Coding Standards

### File Naming

- **Components**: PascalCase (`UserProfile.tsx`, `OrderList.tsx`)
- **Hooks**: camelCase with `use` prefix (`useUserData.ts`)
- **Utilities**: camelCase (`formatDate.ts`)
- **Styles**: `ComponentName.module.css`
- **Tests**: `ComponentName.test.tsx`

### Component Structure

```
src/
├── components/           # Reusable UI components
│   ├── UserCard/
│   │   ├── UserCard.tsx
│   │   ├── UserCard.module.css
│   │   ├── UserCard.test.tsx
│   │   └── index.ts
│   └── index.ts
├── features/            # Feature-specific components
│   ├── users/
│   └── orders/
├── hooks/               # Custom hooks
├── services/            # API services
├── stores/              # Zustand stores
├── types/               # TypeScript types
└── utils/               # Utility functions
```

---

## Salt Design System Usage

### Import Pattern

```typescript
// ✅ Correct - Import from package root
import { Button, Card, FormField, Input } from "@salt-ds/core";
import { Dropdown, StepperInput } from "@salt-ds/lab";

// ❌ Avoid - Deep imports
import { Button } from "@salt-ds/core/Button";
```

### Available Stable Components (@salt-ds/core)

Core components for production use:
- **Layout**: Card, FlexLayout, GridLayout, StackLayout, SplitLayout
- **Navigation**: NavigationItem, Breadcrumb, Link
- **Forms**: Button, Checkbox, RadioButton, FormField, Input, Switch
- **Data Display**: Avatar, Badge, Tooltip, Text
- **Feedback**: Banner, Toast, Dialog, Spinner
- **Overlay**: Menu, Dropdown, Panel

### Lab Components (@salt-ds/lab)

Use with caution - under active development:
- StepperInput
- DatePicker
- Calendar
- Drawer
- Tree

### Theme and Density

```typescript
// Always wrap app with SaltProvider
import { SaltProvider } from "@salt-ds/core";

function App() {
  return (
    <SaltProvider mode="light" density="medium">
      <YourApp />
    </SaltProvider>
  );
}

// Use density prop on components
<FlexLayout density="compact">
  <Button>Compact Button</Button>
</FlexLayout>
```

### Accessibility Requirements

1. All Salt components include built-in accessibility
2. Use semantic HTML with Salt components
3. Provide proper labels for form fields
4. Test with keyboard navigation
5. Don't override ARIA attributes set by Salt

---

## React Patterns

### Component Pattern

```typescript
import { FC, ReactNode } from "react";
import { Card, FlexLayout, Text } from "@salt-ds/core";
import styles from "./UserCard.module.css";

interface UserCardProps {
  name: string;
  email: string;
  role?: string;
  children?: ReactNode;
}

export const UserCard: FC<UserCardProps> = ({
  name,
  email,
  role = "Member",
  children,
}) => {
  return (
    <Card className={styles.userCard}>
      <FlexLayout direction="column" gap={1}>
        <Text styleAs="h3">{name}</Text>
        <Text color="secondary">{email}</Text>
        <Text styleAs="label">{role}</Text>
        {children}
      </FlexLayout>
    </Card>
  );
};
```

### Hook Pattern

```typescript
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { userService } from "../services/userService";

export function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: userService.getAll,
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: userService.create,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });
}
```

---

## Code Generation Guidelines

When generating React components:

1. **Always include**:
   - TypeScript types for all props
   - Proper prop destructuring with defaults
   - Accessibility attributes when needed
   - Salt components over custom HTML

2. **Use these patterns**:
   - Functional components with FC type
   - Destructured props with TypeScript interface
   - Custom hooks for data fetching
   - CSS Modules for custom styling

3. **Avoid**:
   - Class components
   - Inline styles (use CSS Modules)
   - `any` type in TypeScript
   - Direct DOM manipulation
   - Reinventing Salt components

---

## Testing Guidelines

- Use React Testing Library for component tests
- Test user interactions, not implementation
- Use Playwright for e2e tests
- Mock API calls with MSW
