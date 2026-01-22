---
name: salt-ui-testing
description: Guide for testing React components with Salt Design System using Testing Library and Playwright. Use this when creating or debugging UI tests.
---

# Salt Design System UI Testing Skill

This skill helps you create comprehensive tests for React components built with Salt Design System.

## When to Use This Skill

- Creating tests for Salt components
- Testing accessible UI interactions
- Debugging failing component tests
- Setting up Playwright e2e tests for Salt-based UIs

## Testing Setup

### Configuration for Salt Components

```typescript
// test/setup.ts
import { afterEach } from "vitest";
import { cleanup } from "@testing-library/react";
import "@testing-library/jest-dom/vitest";

afterEach(() => {
  cleanup();
});
```

### Salt Provider Wrapper

All Salt components require `SaltProvider`. Create a test utility:

```typescript
// test/utils.tsx
import { FC, ReactNode } from "react";
import { render, RenderOptions } from "@testing-library/react";
import { SaltProvider } from "@salt-ds/core";

interface WrapperProps {
  children: ReactNode;
}

const AllTheProviders: FC<WrapperProps> = ({ children }) => {
  return (
    <SaltProvider mode="light" density="medium">
      {children}
    </SaltProvider>
  );
};

const customRender = (
  ui: React.ReactElement,
  options?: Omit<RenderOptions, "wrapper">
) => render(ui, { wrapper: AllTheProviders, ...options });

export * from "@testing-library/react";
export { customRender as render };
```

## Component Testing Patterns

### Testing Salt Button

```typescript
import { render, screen } from "../test/utils";
import userEvent from "@testing-library/user-event";
import { Button } from "@salt-ds/core";

describe("Button interactions", () => {
  it("handles click events", async () => {
    const handleClick = vi.fn();
    const user = userEvent.setup();
    
    render(<Button onClick={handleClick}>Submit</Button>);
    
    await user.click(screen.getByRole("button", { name: /submit/i }));
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it("respects disabled state", async () => {
    const handleClick = vi.fn();
    const user = userEvent.setup();
    
    render(<Button disabled onClick={handleClick}>Submit</Button>);
    
    await user.click(screen.getByRole("button", { name: /submit/i }));
    
    expect(handleClick).not.toHaveBeenCalled();
    expect(screen.getByRole("button")).toBeDisabled();
  });
});
```

### Testing Salt Form Fields

```typescript
import { render, screen } from "../test/utils";
import userEvent from "@testing-library/user-event";
import { FormField, FormFieldLabel, Input } from "@salt-ds/core";

describe("FormField with Input", () => {
  it("associates label with input", () => {
    render(
      <FormField>
        <FormFieldLabel>Email</FormFieldLabel>
        <Input />
      </FormField>
    );
    
    // Input should be accessible by its label
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
  });

  it("displays validation errors", async () => {
    render(
      <FormField validationStatus="error">
        <FormFieldLabel>Email</FormFieldLabel>
        <Input />
        <span>Email is required</span>
      </FormField>
    );
    
    expect(screen.getByText(/email is required/i)).toBeInTheDocument();
  });

  it("handles user input", async () => {
    const user = userEvent.setup();
    const handleChange = vi.fn();
    
    render(
      <FormField>
        <FormFieldLabel>Email</FormFieldLabel>
        <Input onChange={handleChange} />
      </FormField>
    );
    
    await user.type(screen.getByLabelText(/email/i), "test@example.com");
    
    expect(handleChange).toHaveBeenCalled();
    expect(screen.getByLabelText(/email/i)).toHaveValue("test@example.com");
  });
});
```

### Testing Salt Dropdown

```typescript
import { render, screen, within } from "../test/utils";
import userEvent from "@testing-library/user-event";
import { Dropdown, Option } from "@salt-ds/core";

describe("Dropdown", () => {
  const options = [
    { value: "1", label: "Option 1" },
    { value: "2", label: "Option 2" },
    { value: "3", label: "Option 3" },
  ];

  it("opens on click and selects option", async () => {
    const user = userEvent.setup();
    const handleSelect = vi.fn();
    
    render(
      <Dropdown onSelectionChange={handleSelect}>
        {options.map((opt) => (
          <Option key={opt.value} value={opt.value}>
            {opt.label}
          </Option>
        ))}
      </Dropdown>
    );
    
    // Open dropdown
    await user.click(screen.getByRole("combobox"));
    
    // Select option
    await user.click(screen.getByRole("option", { name: /option 2/i }));
    
    expect(handleSelect).toHaveBeenCalledWith(
      expect.anything(),
      expect.arrayContaining(["2"])
    );
  });

  it("supports keyboard navigation", async () => {
    const user = userEvent.setup();
    
    render(
      <Dropdown>
        {options.map((opt) => (
          <Option key={opt.value} value={opt.value}>
            {opt.label}
          </Option>
        ))}
      </Dropdown>
    );
    
    const combobox = screen.getByRole("combobox");
    await user.click(combobox);
    await user.keyboard("{ArrowDown}");
    await user.keyboard("{ArrowDown}");
    await user.keyboard("{Enter}");
    
    expect(combobox).toHaveTextContent("Option 2");
  });
});
```

## Playwright E2E Tests

### Page Object for Salt Components

```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator } from "@playwright/test";

export class LoginPage {
  private page: Page;
  private emailInput: Locator;
  private passwordInput: Locator;
  private submitButton: Locator;

  constructor(page: Page) {
    this.page = page;
    // Use accessible selectors
    this.emailInput = page.getByLabel(/email/i);
    this.passwordInput = page.getByLabel(/password/i);
    this.submitButton = page.getByRole("button", { name: /sign in/i });
  }

  async goto() {
    await this.page.goto("/login");
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.submitButton.click();
  }

  async getValidationError() {
    return this.page.getByRole("alert").textContent();
  }
}
```

### E2E Test Example

```typescript
// e2e/login.spec.ts
import { test, expect } from "@playwright/test";
import { LoginPage } from "./pages/LoginPage";

test.describe("Login Flow", () => {
  test("successful login redirects to dashboard", async ({ page }) => {
    const loginPage = new LoginPage(page);
    
    await loginPage.goto();
    await loginPage.login("user@example.com", "password123");
    
    await expect(page).toHaveURL("/dashboard");
  });

  test("shows validation error for invalid email", async ({ page }) => {
    const loginPage = new LoginPage(page);
    
    await loginPage.goto();
    await loginPage.login("invalid-email", "password123");
    
    const error = await loginPage.getValidationError();
    expect(error).toContain("Invalid email");
  });
});
```

## Running Tests

```bash
# Run unit/component tests
npm run test

# Run with coverage
npm run test:coverage

# Run e2e tests
npx playwright test

# Run e2e with UI
npx playwright test --ui

# Debug specific test
npx playwright test --debug login.spec.ts
```

## Best Practices

1. **Query by role first** - Use `getByRole` for accessible queries
2. **Avoid test IDs when possible** - Prefer accessible selectors
3. **Test user behavior** - Not implementation details
4. **Always wrap with SaltProvider** - Salt components require theme context
5. **Use userEvent over fireEvent** - More realistic user interaction
6. **Test keyboard navigation** - Salt components are keyboard accessible
