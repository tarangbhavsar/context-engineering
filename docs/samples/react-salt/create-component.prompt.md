---
description: "Generate a new React component using Salt Design System"
name: "Create Salt Component"
agent: "agent"
model: "Claude Sonnet 4"
tools: ['codebase', 'terminal', 'editFiles']
---

# Generate React Component with Salt Design System

Create a new React component using Salt Design System components and patterns.

## Input Parameters

- **Component Name**: ${input:componentName:Enter component name (e.g., UserCard, OrderList)}
- **Component Type**: ${input:componentType:Enter type (display|form|layout)}
- **Feature Area**: ${input:feature:Enter feature area (e.g., users, orders)}

## Requirements

### File Structure

Create the following files:
```
src/features/${input:feature}/${input:componentName}/
├── ${input:componentName}.tsx          # Main component
├── ${input:componentName}.module.css   # Styles
├── ${input:componentName}.test.tsx     # Tests
└── index.ts                            # Barrel export
```

### Component Template

```typescript
import { FC } from "react";
import { Card, FlexLayout, Text, Button } from "@salt-ds/core";
import styles from "./${input:componentName}.module.css";

interface ${input:componentName}Props {
  // Define props based on component type
}

export const ${input:componentName}: FC<${input:componentName}Props> = (props) => {
  return (
    <Card className={styles.container}>
      {/* Use Salt components for layout and UI */}
      <FlexLayout direction="column" gap={2}>
        {/* Component content */}
      </FlexLayout>
    </Card>
  );
};
```

### Salt Components to Consider

Based on component type "${input:componentType}":

#### Display Components
- `Card`, `Panel` - Containers
- `Text` - Typography with styleAs prop
- `Avatar`, `Badge` - Visual indicators
- `FlexLayout`, `StackLayout` - Layout

#### Form Components
- `FormField`, `FormFieldLabel` - Form structure
- `Input`, `TextArea` - Text input
- `Checkbox`, `RadioButton`, `Switch` - Selection
- `Button` - Actions
- `Dropdown` - Selection lists

#### Layout Components
- `GridLayout` - Grid-based layouts
- `FlexLayout` - Flexible arrangements
- `SplitLayout` - Two-panel layouts
- `StackLayout` - Vertical stacking

### Styling Guidelines

```css
/* Use CSS variables from Salt theme */
.container {
  padding: var(--salt-spacing-200);
  background: var(--salt-container-primary-background);
}

/* Responsive patterns */
@media (max-width: 600px) {
  .container {
    padding: var(--salt-spacing-100);
  }
}
```

### Test Template

```typescript
import { render, screen } from "@testing-library/react";
import { SaltProvider } from "@salt-ds/core";
import { ${input:componentName} } from "./${input:componentName}";

const renderWithSalt = (ui: React.ReactElement) => {
  return render(<SaltProvider>{ui}</SaltProvider>);
};

describe("${input:componentName}", () => {
  it("renders without crashing", () => {
    renderWithSalt(<${input:componentName} />);
    // Add assertions
  });

  it("handles user interaction", async () => {
    renderWithSalt(<${input:componentName} />);
    // Add interaction tests
  });
});
```

### Export Barrel

```typescript
// index.ts
export { ${input:componentName} } from "./${input:componentName}";
export type { ${input:componentName}Props } from "./${input:componentName}";
```

## Additional Requirements

1. Follow [React/Salt coding standards](react.instructions.md)
2. Use TypeScript with proper type definitions
3. Include JSDoc comments for the component
4. Ensure accessibility with proper ARIA attributes
5. Make component responsive using Salt breakpoints

After generation, verify with:
```bash
npm run typecheck
npm run test -- ${input:componentName}
```
