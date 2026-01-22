---
applyTo: "**/*.tsx,**/*.ts"
description: "React and TypeScript coding standards with Salt Design System"
name: "React/Salt Standards"
---

# React TypeScript Standards with Salt Design System

## TypeScript Rules

### Type Definitions

```typescript
// ✅ Correct - Use interface for props
interface UserFormProps {
  initialData?: UserData;
  onSubmit: (data: UserData) => Promise<void>;
  onCancel: () => void;
}

// ✅ Correct - Use type for unions/intersections
type ButtonVariant = "primary" | "secondary" | "cta";
type WithChildren<T> = T & { children?: ReactNode };

// ❌ Avoid - any type
const handleData = (data: any) => { ... };

// ❌ Avoid - generic Object type
const config: Object = { ... };
```

### Enum vs Union Types

```typescript
// ✅ Prefer - Union types (better tree-shaking)
type Status = "pending" | "active" | "completed";

// ⚠️ Use sparingly - Enums (only when runtime value iteration needed)
enum UserRole {
  Admin = "admin",
  User = "user",
  Guest = "guest",
}
```

---

## React Component Patterns

### Function Component

```typescript
import { FC } from "react";
import { Button, Card, Text } from "@salt-ds/core";

interface ActionCardProps {
  title: string;
  description: string;
  onAction: () => void;
  disabled?: boolean;
}

export const ActionCard: FC<ActionCardProps> = ({
  title,
  description,
  onAction,
  disabled = false,
}) => {
  return (
    <Card>
      <Text styleAs="h3">{title}</Text>
      <Text>{description}</Text>
      <Button onClick={onAction} disabled={disabled}>
        Take Action
      </Button>
    </Card>
  );
};
```

### Custom Hook

```typescript
import { useState, useCallback } from "react";

interface UseToggleReturn {
  isOpen: boolean;
  open: () => void;
  close: () => void;
  toggle: () => void;
}

export function useToggle(initialState = false): UseToggleReturn {
  const [isOpen, setIsOpen] = useState(initialState);

  const open = useCallback(() => setIsOpen(true), []);
  const close = useCallback(() => setIsOpen(false), []);
  const toggle = useCallback(() => setIsOpen((prev) => !prev), []);

  return { isOpen, open, close, toggle };
}
```

---

## Salt Component Patterns

### Form with Validation

```typescript
import { FormField, FormFieldLabel, Input, Button } from "@salt-ds/core";
import { useForm, SubmitHandler } from "react-hook-form";

interface LoginFormData {
  email: string;
  password: string;
}

export const LoginForm: FC = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<LoginFormData>();

  const onSubmit: SubmitHandler<LoginFormData> = (data) => {
    // Handle submission
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FormField validationStatus={errors.email ? "error" : undefined}>
        <FormFieldLabel>Email</FormFieldLabel>
        <Input
          {...register("email", { required: "Email is required" })}
          type="email"
        />
        {errors.email && <span>{errors.email.message}</span>}
      </FormField>

      <FormField validationStatus={errors.password ? "error" : undefined}>
        <FormFieldLabel>Password</FormFieldLabel>
        <Input
          {...register("password", { required: "Password is required" })}
          type="password"
        />
      </FormField>

      <Button type="submit" variant="cta">
        Login
      </Button>
    </form>
  );
};
```

### Layout Patterns

```typescript
import { FlexLayout, GridLayout, StackLayout, Card } from "@salt-ds/core";

// ✅ Use FlexLayout for flexible arrangements
<FlexLayout direction="row" gap={2} align="center" justify="space-between">
  <Logo />
  <Navigation />
  <UserMenu />
</FlexLayout>

// ✅ Use GridLayout for grid-based layouts
<GridLayout columns={3} gap={3}>
  {items.map((item) => (
    <Card key={item.id}>{item.name}</Card>
  ))}
</GridLayout>

// ✅ Use StackLayout for simple stacking
<StackLayout gap={1}>
  <Text styleAs="h2">Title</Text>
  <Text>Description text here</Text>
</StackLayout>
```

---

## Event Handling

```typescript
import { ChangeEvent, FormEvent, MouseEvent } from "react";

// ✅ Type event handlers properly
const handleClick = (event: MouseEvent<HTMLButtonElement>) => {
  event.preventDefault();
};

const handleChange = (event: ChangeEvent<HTMLInputElement>) => {
  setValue(event.target.value);
};

const handleSubmit = (event: FormEvent<HTMLFormElement>) => {
  event.preventDefault();
};
```

---

## State Management

### Component State

```typescript
// ✅ Use useState for simple state
const [count, setCount] = useState(0);

// ✅ Use useReducer for complex state
const [state, dispatch] = useReducer(reducer, initialState);

// ✅ Use useMemo for expensive computations
const sortedItems = useMemo(() => 
  items.sort((a, b) => a.name.localeCompare(b.name)),
  [items]
);

// ✅ Use useCallback for stable references
const handleDelete = useCallback((id: string) => {
  deleteItem(id);
}, [deleteItem]);
```

### Global State with Zustand

```typescript
import { create } from "zustand";

interface UserStore {
  user: User | null;
  setUser: (user: User) => void;
  clearUser: () => void;
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null }),
}));
```

---

## Import Order

Follow this consistent import order:

```typescript
// 1. React imports
import { FC, useState, useEffect } from "react";

// 2. Third-party libraries
import { useQuery } from "@tanstack/react-query";
import { useForm } from "react-hook-form";

// 3. Salt Design System
import { Button, Card, FormField, Input } from "@salt-ds/core";
import { DatePicker } from "@salt-ds/lab";

// 4. Local components
import { UserAvatar } from "../components/UserAvatar";

// 5. Hooks
import { useUsers } from "../hooks/useUsers";

// 6. Services/utilities
import { formatDate } from "../utils/formatDate";

// 7. Types
import type { User } from "../types/user";

// 8. Styles
import styles from "./Component.module.css";
```
