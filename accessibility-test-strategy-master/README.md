# ♿ Accessibility Testing Suite

[![WCAG 2.1 AA](https://img.shields.io/badge/Accessibility-WCAG%202.1%20AA-blue?logo=w3c)](https://www.w3.org/WAI/WCAG21/quickref/)
[![axe-core](https://img.shields.io/badge/Testing-axe--core-purple)](https://github.com/dequelabs/axe-core)
[![Playwright](https://img.shields.io/badge/Browser%20Testing-Playwright-green?logo=playwright)](https://playwright.dev/)
[![TypeScript](https://img.shields.io/badge/Language-TypeScript-3178c6?logo=typescript)](https://www.typescriptlang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

An automated accessibility testing framework designed to identify usability barriers in an ecommerce application. It combines component-level checks, browser-based accessibility tests, command-line audits, and continuous integration to help detect accessibility issues early in the development lifecycle.

The framework focuses on making web interfaces more accessible to users with different abilities, including people who rely on keyboards, screen readers, and other assistive technologies.

## Overview

Accessibility should be validated throughout development rather than treated as a final testing step.

This project demonstrates a layered testing approach that helps identify common accessibility problems, including missing labels, incorrect ARIA attributes, insufficient color contrast, and inaccessible navigation.

### Key Capabilities

- Automated accessibility checks for reusable UI components.
- Browser-level accessibility scans across application pages.
- Keyboard navigation and form interaction testing.
- Standalone page audits with structured reports.
- Automated accessibility checks in a CI pipeline.
- Reusable accessibility patterns for forms, navigation, and interactive elements.

## Technology Stack

| Technology | Purpose |
|---|---|
| axe-core | Accessibility rule engine |
| jest-axe | Accessibility assertions for component tests |
| Playwright | Browser automation and end-to-end testing |
| @axe-core/playwright | Accessibility scans within browser tests |
| Pa11y | Command-line accessibility auditing |
| Storybook and addon-a11y | Component-level accessibility inspection |
| GitHub Actions | Automated testing and CI checks |
| TypeScript | Type-safe test and application code |
| React | Reusable interface components |

## Project Structure

```text
accessibility-test-suite/
├── .github/
│   └── workflows/
│       └── accessibility-ci.yml
├── .storybook/
│   ├── main.ts
│   └── preview.ts
├── public/
│   ├── index.html
│   ├── products.html
│   ├── checkout.html
│   ├── checkout.js
│   └── styles.css
├── scripts/
│   └── pa11y-audit.ts
├── src/
│   └── components/
│       ├── CheckoutForm/
│       │   ├── CheckoutForm.tsx
│       │   ├── CheckoutForm.stories.tsx
│       │   └── index.ts
│       ├── ProductCard/
│       │   ├── ProductCard.tsx
│       │   ├── ProductCard.stories.tsx
│       │   └── index.ts
│       └── NavigationMenu/
│           ├── NavigationMenu.tsx
│           ├── NavigationMenu.stories.tsx
│           └── index.ts
├── tests/
│   ├── __mocks__/
│   │   └── styleMock.ts
│   ├── setup.ts
│   ├── unit/
│   │   ├── CheckoutForm.a11y.test.tsx
│   │   ├── ProductCard.a11y.test.tsx
│   │   └── NavigationMenu.a11y.test.tsx
│   └── e2e/
│       ├── checkout.a11y.spec.ts
│       └── product-listing.a11y.spec.ts
├── types/
├── jest.config.ts
├── playwright.config.ts
├── tsconfig.json
├── tsconfig.test.json
└── package.json
```

## Getting Started

### Prerequisites

- Node.js 18 or later
- npm 9 or later

### Installation

Clone your repository and navigate to the project directory.

```bash
npm install
npx playwright install chromium firefox
```

### Running the Tests

Run component-level accessibility tests:

```bash
npm test
```

Start the application using a static server:

```bash
npx serve -s public -l 3000
```

In a separate terminal, run browser-based accessibility tests:

```bash
npm run test:e2e
```

Run the standalone accessibility audit:

```bash
npm run test:pa11y
```

Run the complete test suite:

```bash
npm run test:all
```

These commands assume the corresponding scripts are defined in `package.json`.

## Testing Strategy

### 1. Component-Level Testing

Component tests use `jest-axe` to check rendered React components for detectable accessibility violations.

The test suite can validate:

- Accessible names and form labels.
- Label-to-input associations.
- ARIA properties and error descriptions.
- Semantic HTML and appropriate roles.
- Accessibility across default and validation-error states.

Example:

```tsx
it('should have no detected accessibility violations', async () => {
  // Render the component before performing the accessibility scan.
  const { container } = render(<CheckoutForm />);

  // Analyse the rendered component.
  const results = await axe(container);

  // Fail the test if axe reports any violations.
  expect(results).toHaveNoViolations();
});
```

### 2. End-to-End Accessibility Testing

Playwright tests evaluate accessibility within a running browser. The axe integration checks selected WCAG rules on complete pages and during important user journeys.

Coverage includes:

- Checkout and product-listing workflows.
- Keyboard navigation and form submission.
- Heading and landmark structure.
- Image alternative text.
- Form validation and accessible error announcements.

Example:

```typescript
const results = await new AxeBuilder({ page })
  // Select the WCAG rule groups covered by this test.
  .withTags(['wcag2a', 'wcag2aa', 'wcag21aa'])
  // Analyse the current page.
  .analyze();

// Fail the test when accessibility violations are detected.
expect(results.violations).toEqual([]);
```

### 3. Command-Line Accessibility Auditing

Pa11y provides an additional auditing layer for application pages.

The audit workflow can:

- Scan configured application URLs.
- Generate machine-readable JSON reports.
- Separate errors, warnings, and notices.
- Return a non-zero exit code when configured error thresholds are exceeded.

This makes accessibility findings easier to review and incorporate into a development workflow.

### 4. Storybook Accessibility Checks

Storybook provides an isolated environment for testing individual UI components.

When configured with the accessibility addon, component stories can be inspected for common accessibility issues without navigating through the complete application.

This is particularly useful when developing reusable components and reviewing their different states.

## Accessibility Practices

### Accessible Forms

The checkout interface follows common form-accessibility practices:

- Associate each input with a meaningful label.
- Identify required fields programmatically.
- Connect validation messages to their related inputs.
- Communicate invalid input states.
- Use fieldsets and legends to group related controls.
- Provide suitable autocomplete attributes for supported personal-data fields.

### Accessible Navigation

Navigation components are designed around semantic HTML and keyboard access.

- Provide a skip-to-content link.
- Use meaningful navigation landmarks.
- Indicate the current page where appropriate.
- Expose expandable menu state using suitable ARIA attributes.
- Ensure interactive elements have visible keyboard focus.

### Interactive Components

Interactive controls should communicate their purpose and state clearly.

- Provide accessible names for buttons and controls.
- Represent unavailable or disabled actions appropriately.
- Announce relevant status and validation updates.
- Maintain a visible focus indicator.
- Avoid relying on color alone to communicate important information.

### Color Contrast

Color contrast is an important part of visual accessibility.

For typical text, WCAG AA generally requires a contrast ratio of at least **4.5:1**. Large text has a lower minimum requirement.

Contrast should be validated using the actual foreground and background colors in the application rather than inferred from appearance alone.

## Continuous Integration

The GitHub Actions workflow can run accessibility-related checks automatically when configured for pushes and pull requests.

A typical validation sequence is:

```text
Type checking
     |
     v
Component accessibility tests
     |
     +------ Browser accessibility tests
     |
     +------ Pa11y audits
     |
     v
Combined CI result
```

The pipeline can be configured to fail when required checks fail, helping prevent known accessibility violations from reaching the main branch.

Depending on the workflow configuration, CI artifacts may include test coverage data, browser reports, screenshots, and Pa11y JSON reports.

## Reports and Debugging

When a test fails, review the reported rule, affected HTML element, and suggested remediation.

A useful debugging process is to:

1. Identify the failing accessibility rule.
2. Inspect the affected component or page.
3. Fix the underlying semantic or usability issue.
4. Rerun the relevant test.
5. Run the broader suite to check for regressions.

This approach helps distinguish genuine application defects from test configuration issues.

## Limitations

Automated accessibility testing is valuable, but it does not guarantee complete WCAG conformance.

Some issues require human judgement or interaction with assistive technology. Manual testing should therefore complement automated scans, including keyboard-only navigation and screen-reader testing with appropriate tools such as NVDA, JAWS, or VoiceOver.

The results should be described as the accessibility checks performed by this framework, rather than as proof of complete accessibility compliance.

## References

- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/)
- [axe-core Documentation](https://github.com/dequelabs/axe-core)
- [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/)
- [Playwright Documentation](https://playwright.dev/docs/accessibility-testing)
- [Pa11y Documentation](https://pa11y.org/)
- [jest-axe Documentation](https://github.com/nickcolley/jest-axe)

