# ♿ Accessibility Testing Suite

[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-View%20Workflows-2088FF?logo=githubactions)](https://github.com/Pragati4566/Accessibility-Test-Strategy/actions)
[![WCAG 2.1 AA](https://img.shields.io/badge/Accessibility-WCAG%202.1%20AA-blue?logo=w3c)](https://www.w3.org/WAI/WCAG21/quickref/)
[![axe-core](https://img.shields.io/badge/Testing-axe--core-purple)](https://github.com/dequelabs/axe-core)
[![Playwright](https://img.shields.io/badge/Browser%20Testing-Playwright-green?logo=playwright)](https://playwright.dev/)
[![TypeScript](https://img.shields.io/badge/Language-TypeScript-3178c6?logo=typescript)](https://www.typescriptlang.org/)

An automated accessibility testing framework for identifying common accessibility barriers in an ecommerce application. It combines component-level checks, browser-based accessibility tests, command-line audits, and continuous integration to help detect issues early in the development lifecycle.

The project focuses on accessible forms, keyboard navigation, meaningful page structure, color contrast, and support for users who rely on assistive technologies.

## Overview

Accessibility should be considered throughout development rather than treated as a final testing step. This project demonstrates a layered testing approach for finding common issues such as missing labels, incorrect ARIA attributes, low color contrast, and inaccessible navigation.

### Key Capabilities

- Automated accessibility checks for reusable UI components.
- Browser-based accessibility scans across application pages.
- Keyboard navigation and form interaction testing.
- Command-line audits with structured reports.
- CI checks that can prevent a pull request from passing when required tests fail.
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
accessibility-test-strategy/
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

> Adjust this tree to match the files and folders that are actually present in your repository.

## Getting Started

### Prerequisites

- Node.js 18 or later
- npm 9 or later

### Installation

Clone your repository and navigate to the project directory. Then install the project dependencies and browser binaries:

```bash
npm install
npx playwright install chromium firefox
```

### Running the Tests

Run component-level tests:

```bash
npm test
```

Start the static application server in one terminal:

```bash
npx serve -s public -l 3000
```

In another terminal, run browser-based accessibility tests:

```bash
npm run test:e2e
```

Run the command-line accessibility audit:

```bash
npm run test:pa11y
```

Run the complete test suite, if the script is configured in `package.json`:

```bash
npm run test:all
```

These commands depend on the scripts and dependencies defined in your `package.json`.

## Testing Strategy

### 1. Component-Level Testing

Component tests use `jest-axe` to check rendered React components for detectable accessibility violations.

Coverage can include:

- Accessible names and form labels.
- Label-to-input associations.
- ARIA attributes and validation descriptions.
- Semantic HTML and appropriate roles.
- Default and validation-error states.

Example:

```tsx
it('should have no detected accessibility violations', async () => {
  // Render the component before running the accessibility scan.
  const { container } = render(<CheckoutForm />);

  // Analyse the rendered component.
  const results = await axe(container);

  // Fail the test if axe reports any violations.
  expect(results).toHaveNoViolations();
});
```

### 2. End-to-End Accessibility Testing

Playwright tests run in a real browser. The axe integration checks selected WCAG rule groups across important pages and user journeys.

Coverage can include:

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

// Fail the test when axe reports accessibility violations.
expect(results.violations).toEqual([]);
```

### 3. Command-Line Accessibility Auditing

Pa11y provides an additional auditing layer for configured application pages. Depending on the script configuration, it can:

- Scan selected application URLs.
- Generate JSON reports.
- Separate errors, warnings, and notices.
- Return a non-zero exit code when the configured failure threshold is exceeded.

### 4. Storybook Accessibility Checks

Storybook provides an isolated environment for inspecting individual UI components. When configured with the accessibility addon, component stories can be checked for common issues without navigating through the whole application.

This is useful when developing reusable components and testing their different states.

## Accessibility Practices

### Accessible Forms

- Associate each input with a meaningful label.
- Identify required fields programmatically.
- Link validation messages to their related inputs.
- Communicate invalid input states.
- Group related controls with `fieldset` and `legend` where appropriate.
- Use suitable `autocomplete` values for supported personal-data fields.

### Accessible Navigation

- Provide a skip-to-content link.
- Use semantic navigation landmarks.
- Identify the current page where appropriate.
- Expose expandable menu state with suitable ARIA attributes.
- Ensure interactive elements have visible keyboard focus.

### Interactive Components

- Give buttons and controls accessible names.
- Represent unavailable or disabled actions correctly.
- Announce relevant status and validation updates.
- Keep keyboard focus visible.
- Do not rely on color alone to communicate important information.

### Color Contrast

Color contrast should be checked using the actual foreground and background colors in the application. WCAG AA generally requires a contrast ratio of at least **4.5:1 for normal text** and **3:1 for large text**.

## Continuous Integration

The GitHub Actions workflow can run accessibility-related checks automatically on pushes and pull requests when configured in the repository.

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

The pipeline can be configured to fail when required checks fail. Depending on the workflow configuration, reports may include test coverage, browser output, screenshots, and Pa11y JSON results.

## Reports and Debugging

When a test fails, review the reported rule, the affected HTML element, and the suggested remediation.

1. Identify the failing accessibility rule.
2. Inspect the affected component or page.
3. Fix the underlying semantic or usability issue.
4. Rerun the relevant test.
5. Run the broader suite to check for regressions.

## Limitations

Automated accessibility testing does not guarantee complete WCAG conformance. Some issues require human judgement or testing with assistive technologies. Manual validation should complement automated scans, including keyboard-only navigation and screen-reader testing with tools such as NVDA, JAWS, or VoiceOver.

Describe results in terms of the checks actually performed rather than claiming complete accessibility compliance based only on automated tests.

## References

- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/)
- [axe-core Documentation](https://github.com/dequelabs/axe-core)
- [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/)
- [Playwright Accessibility Testing](https://playwright.dev/docs/accessibility-testing)
- [Pa11y Documentation](https://pa11y.org/)
- [jest-axe Documentation](https://github.com/nickcolley/jest-axe)
