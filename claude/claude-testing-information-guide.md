# TESTING INFORMATION SECTION

## 🧪 TESTING INFORMATION - Comprehensive TDD Testing Guide - CRITICAL (2025-10-01)

**Test Status**: ✅ GUIDE COMPLETE

**Context**: Comprehensive guide for Test-Driven Development covering all test types, database strategies, and best practices

**Reference**: `docs/dev/TDD-Workflow-Enhancement-Plan-FINAL.md`

**Key Test Cases**: This entry provides complete testing patterns for all scenarios

**Implementation Details**: Frameworks, tools, and specific patterns for each test type

### **Test Type Guidelines by Component**:

#### **Unit Tests** (tests/unit/) - Vitest ⚡

**Purpose**: Test individual functions/classes in complete isolation  
**Speed**: Fast (< 1ms per test)  
**Scope**: Pure functions, utilities, services (mocked dependencies)

**What to Unit Test**:

- ✅ Utility functions (format, validation, helpers)
- ✅ Business logic functions
- ✅ Service classes with mocked dependencies
- ✅ Svelte stores (state management)
- ✅ Data transformations
- ✅ Algorithms and calculations
- ✅ Validation schemas (Zod)

**What NOT to Unit Test**:

- ❌ Database queries (use integration tests)
- ❌ API endpoints (use integration tests)
- ❌ External service calls (use integration tests with mocks)
- ❌ File I/O operations (use integration tests)

**Location**: `tests/unit/`  
**Naming**: `*.test.ts`

**Pattern Example**:

```typescript
import { describe, it, expect } from 'vitest';
import { formatDate } from '$lib/utils/format';

describe('formatDate', () => {
	it('formats date with default format', () => {
		const date = new Date('2024-01-15T10:30:00Z');
		expect(formatDate(date)).toBe('January 15, 2024');
	});

	it('handles null dates gracefully', () => {
		expect(formatDate(null)).toBe('—');
	});
});
```

---

#### **Integration Tests** (tests/integration/) - Vitest 🔗

**Purpose**: Test how components work together (API + DB, Service + Cache, etc)  
**Speed**: Medium (10-100ms per test)  
**Scope**: API endpoints, database queries, external service integration

**What to Integration Test**:

- ✅ API endpoints with real database (test database)
- ✅ Authentication flows (login, logout, session validation)
- ✅ Database queries and transactions
- ✅ File upload/download with storage
- ✅ Email sending (with mock SMTP server)
- ✅ Cache interactions (Redis)
- ✅ External API calls (with mocked responses)
- ✅ Repository pattern implementations

**What NOT to Integration Test**:

- ❌ Pure utility functions (use unit tests)
- ❌ Complete user workflows across pages (use E2E tests)
- ❌ Browser-specific behavior (use E2E tests)

**Location**: `tests/integration/`  
**Naming**: `*.integration.test.ts`

---

### **Database Strategy**:

**Use separate test database** (`savvy_next_test`) - NEVER production!

**Two Database Testing Approaches**:

#### **Approach 1: Transaction Rollback (Recommended for Unit-like Integration Tests)**

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { db } from '$lib/server/db';

let transaction;

beforeEach(async () => {
	transaction = await db.transaction();
});

afterEach(async () => {
	await transaction.rollback(); // Automatic cleanup!
});

describe('Course CRUD Operations', () => {
	it('creates a new course', async () => {
		const course = await createCourse({
			title: 'Test Course',
			description: 'Test Description'
		});
		expect(course.id).toBeDefined();
	});
	// Data automatically cleaned up by rollback!
});
```

#### **Approach 2: Full Database Reset (For True Integration Tests)**

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { resetTestDatabase, seedTestDatabase } from '../../helpers/db-helpers';

describe('POST /api/auth/login', () => {
	beforeEach(async () => {
		await resetTestDatabase(); // Reset entire database to clean state
		await seedTestDatabase(); // Seed with known test data
	});

	afterEach(async () => {
		await cleanupTestDatabase();
	});

	it('logs in user with valid credentials', async () => {
		const response = await fetch('http://localhost:5173/api/auth/login', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({
				email: 'test@example.com',
				password: 'Test123!'
			})
		});

		expect(response.ok).toBe(true);
		const data = await response.json();
		expect(data.success).toBe(true);
	});
});
```

---

### **Database Helper Utilities**:

```typescript
// tests/helpers/db-helpers.ts
import { supabase } from '$lib/server/db/client';

export async function resetTestDatabase() {
	// Truncate all tables in correct order due to foreign keys
	await supabase.from('lesson_progress').delete().neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase
		.from('course_enrollments')
		.delete()
		.neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase.from('quizzes').delete().neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase.from('lessons').delete().neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase.from('courses').delete().neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase.from('profiles').delete().neq('id', '00000000-0000-0000-0000-000000000000');
	await supabase.from('users').delete().neq('id', '00000000-0000-0000-0000-000000000000');
}

export async function seedTestDatabase() {
	// Import and run seed script
	await seedData();
}

export async function cleanupTestDatabase() {
	await resetTestDatabase();
}
```

---

### **Environment Configuration**:

```bash
# .env.test
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/savvy_next_test"
SUPABASE_URL="http://localhost:54321"  # Local Supabase
SUPABASE_ANON_KEY="..."
SUPABASE_SERVICE_KEY="..."
```

---

### **Setup Test Database**:

```bash
# Run PowerShell script to set up test database
.\scripts\setup-test-db.ps1

# Or reset existing test database
.\scripts\setup-test-db.ps1 -Reset
```

The script runs migrations in explicit order: 01 → 02 → 03 → 04

---

#### **Svelte Component Tests** (tests/unit/lib/components/) - Vitest + Testing Library 🎨

**Purpose**: Test component logic, rendering, and user interactions  
**Speed**: Fast to Medium (5-50ms per test)  
**Scope**: Component behavior, props, events, slots, conditional rendering

**What to Component Test**:

- ✅ Component renders correctly with different props
- ✅ User interactions (clicks, input changes, form submissions)
- ✅ Conditional rendering based on props/state
- ✅ Event emission and handling
- ✅ Form validation messages
- ✅ Accessibility (ARIA attributes, keyboard navigation)
- ✅ Slot rendering

**What NOT to Component Test**:

- ❌ Visual appearance (use Storybook + visual regression)
- ❌ API calls (mock these)
- ❌ Database operations (mock these)
- ❌ Complete user flows (use E2E tests)

**Location**: `tests/unit/lib/components/`  
**Naming**: `*.test.ts` with companion `.test.svelte` wrapper

**Pattern Example**:

```typescript
import { describe, it, expect, vi } from 'vitest';
import { render, fireEvent } from '@testing-library/svelte';
import Button from '$lib/components/ui/button/button.svelte';

describe('Button Component', () => {
	it('handles click events', async () => {
		const handleClick = vi.fn();
		const { getByRole } = render(Button, {
			props: { children: 'Click me', onclick: handleClick }
		});

		await fireEvent.click(getByRole('button'));
		expect(handleClick).toHaveBeenCalledOnce();
	});

	it('disables button when loading', () => {
		const { getByRole } = render(Button, {
			props: { children: 'Loading', loading: true }
		});

		const button = getByRole('button');
		expect(button).toBeDisabled();
	});
});
```

---

#### **Storybook Stories** (src/lib/components/\*_/_.stories.ts) - NOT TESTS 📚

**Purpose**: Visual component documentation and manual testing  
**Speed**: N/A (not automated tests)  
**Scope**: Component variants, states, and usage examples

**What to Document in Storybook**:

- ✅ All UI components (buttons, inputs, cards, modals, etc.)
- ✅ Different variants (primary, secondary, destructive, outline)
- ✅ Different sizes (sm, md, lg, xl)
- ✅ Different states (default, hover, active, disabled, loading, error)
- ✅ Edge cases (long text, no data, empty states, errors)
- ✅ Accessibility features and keyboard navigation
- ✅ Responsive behavior
- ✅ Dark mode variations

**When to Create Stories**:

- ⏰ Create stories AFTER tests pass (part of refactor/documentation phase)
- Stories are living documentation, NOT tests
- Useful for designers to see component variants
- Useful for developers to understand component usage
- Can be used for visual regression testing with Chromatic (optional)

**Location**: Co-located with component: `src/lib/components/ui/button/button.stories.ts`

---

#### **E2E Tests** (tests/e2e/) - Playwright 🌐

**Purpose**: Test complete user workflows across multiple pages in real browser  
**Speed**: Slow (1-10 seconds per test)  
**Scope**: Critical user journeys, multi-page flows, real browser interactions

**What to E2E Test** (Only Critical Paths!):

- ✅ User registration → email verification → login → dashboard
- ✅ Login → browse courses → enroll → view lesson → take quiz → certificate
- ✅ Admin creates course → publishes → student enrolls → completes
- ✅ Password reset complete flow
- ✅ Payment/checkout flows (if applicable)
- ✅ Critical business workflows that involve multiple features

**What NOT to E2E Test**:

- ❌ Individual component behavior (use component tests)
- ❌ API endpoints directly (use integration tests)
- ❌ Every single page/feature (too slow and brittle)
- ❌ Edge cases and error handling (use unit/integration tests)

**Location**: `tests/e2e/`  
**Naming**: `*.spec.ts`

**Pattern Example**:

```typescript
import { test, expect } from '@playwright/test';

test.describe('Authentication Flow', () => {
	test('user can register, login, and logout', async ({ page }) => {
		// Registration
		await page.goto('/register');
		await page.fill('input[name="email"]', 'newuser@example.com');
		await page.fill('input[name="password"]', 'Test123!');
		await page.click('button[type="submit"]');

		// Should redirect to login
		await expect(page).toHaveURL('/login');

		// Login
		await page.fill('input[name="email"]', 'newuser@example.com');
		await page.fill('input[name="password"]', 'Test123!');
		await page.click('button[type="submit"]');

		// Should be on dashboard
		await expect(page).toHaveURL('/dashboard');
		await expect(page.locator('[data-testid="user-menu"]')).toBeVisible();

		// Logout
		await page.click('[data-testid="user-menu"]');
		await page.click('text=Logout');
		await expect(page).toHaveURL('/login');
	});
});
```

**E2E Best Practices**:

1. Use `data-testid` attributes for stable selectors
2. Use page object pattern for complex flows
3. Use fixtures for authentication state
4. Keep tests independent - one test shouldn't depend on another
5. Clean up test data after each test
6. Use realistic test data
7. Test only critical user paths

---

### **Minimum Viable Tests (MVT) Approach**:

For complex steps, prioritize tests using the MVT methodology:

#### **Tier 1: MUST-HAVE (Create First - MVT)**

1. ✅ Happy path unit tests for core business logic
2. ✅ Critical API endpoint integration tests (CRUD operations)
3. ✅ ONE E2E test for the primary user workflow
4. ✅ Database integration tests for critical queries

#### **Tier 2: SHOULD-HAVE (Add After MVT Passing)**

1. ⭐ Edge case unit tests (null, empty, boundary values)
2. ⭐ Error handling integration tests
3. ⭐ Authentication/authorization tests
4. ⭐ Additional E2E tests for secondary workflows

#### **Tier 3: NICE-TO-HAVE (Add If Time Permits)**

1. 💎 Performance tests
2. 💎 Visual regression tests
3. 💎 Accessibility tests
4. 💎 Load/stress tests
5. 💎 Compatibility tests (different browsers/devices)

---

### **Exploratory Prototyping Exception**:

TDD is mandatory for production code, but exploratory prototyping is allowed under strict conditions:

**When Prototyping is Acceptable**:

- ✅ Exploring new libraries or unfamiliar technologies
- ✅ Proof-of-concept for complex/uncertain features
- ✅ UI design experimentation and iteration
- ✅ Performance testing different implementation approaches
- ✅ Spike solutions to validate technical feasibility

**Strict Rules for Prototyping**:

1. **Separate Branch**: Work in `prototype-*` branch only
2. **Clear Commits**: Mark ALL commits with `prototype:` prefix
3. **Time-Boxed**: Set time limit (e.g., 2 hours max)
4. **Document Learnings**: Write summary of what you learned
5. **Delete Prototype**: Once approach validated, DELETE ALL prototype code
6. **Start Fresh with TDD**: Begin new branch with tests first, using lessons learned
7. **NEVER Merge**: Prototype code NEVER merged to main - it's throwaway!

**Prototyping Pattern**:

```bash
# Prototyping phase
git checkout -b prototype-video-player-evaluation
git commit --allow-empty -m "prototype: start - evaluating video.js vs plyr.io"

# Experiment and document decision
git commit -m "prototype: decision - use plyr.io (better mobile support)"

# Once decided, THROW AWAY prototype
git checkout main
git branch -D prototype-video-player-evaluation  # DELETE PROTOTYPE!

# Start fresh with TDD
git checkout -b phase-6.2.1-video-player
# Write tests FIRST based on prototype learnings
# Then implement with chosen solution
```

**What Prototyping Is NOT**:

- ❌ NOT a way to skip TDD
- ❌ NOT production code
- ❌ NOT refactoring (that's part of TDD)
- ❌ NOT a way to "save time" (you'll waste time later)

---

### **What Breaks Without TDD**:

- ❌ **No safety net** for refactoring - fear of breaking things paralyzes development
- ❌ **Tests written after** code follow implementation biases - they test what you built, not what you need
- ❌ **Missing edge cases** - only test happy path because that's what you implemented
- ❌ **Poor API design** - don't consider testability or usability during design
- ❌ **Regressions** go undetected until production - manual testing misses things
- ❌ **Hard to understand** code - tests are living documentation, without them code is opaque
- ❌ **Hard to maintain** - changing code breaks things in unexpected ways
- ❌ **No confidence** in deployments - afraid to release because uncertain if things work
- ❌ **Technical debt** accumulates - untested code becomes "scary" to touch
- ❌ **Debugging takes longer** - without tests, must manually reproduce issues

---

### **Benefits of TDD**:

- ✅ **Immediate feedback** - know instantly if code works
- ✅ **Better design** - testable code is well-designed code
- ✅ **Living documentation** - tests show how code should be used
- ✅ **Fearless refactoring** - tests catch regressions immediately
- ✅ **Fewer bugs** - catch issues before they reach production
- ✅ **Faster debugging** - failing test shows exactly what broke
- ✅ **Confidence** - deploy knowing everything works
- ✅ **Reduced stress** - no fear of breaking things

---

### **Verification Checklist** (Before Completing Any Step):

- [ ] Tests existed BEFORE implementation (Red)
- [ ] Tests failed initially - proved they test something
- [ ] All tests pass after implementation (Green)
- [ ] Code refactored with passing tests (Refactor)
- [ ] Code coverage meets threshold (≥80% for new code)
- [ ] Zero TypeScript errors (get_diagnostics_code)
- [ ] Zero ESLint warnings (pnpm lint)
- [ ] Prettier formatting applied (pnpm format)
- [ ] All quality checks pass
- [ ] Storybook stories created for UI components (if applicable)
- [ ] MVP Task Plan checkboxes updated

---

**Lessons Learned**:

- TDD is not optional - it's mandatory for all production code
- Tests written first lead to better API design
- MVT approach balances speed with quality
- Database strategy must be chosen based on test type
- Prototyping is allowed but code must be deleted
- Comprehensive testing prevents production bugs

**Files**: See `docs/dev/TDD-Workflow-Enhancement-Plan-FINAL.md` for complete details and examples

**Priority Levels**: 🚨 CRITICAL - This guide is essential for maintaining code quality

---

## 🔧 TOOL USAGE PATTERN - Vitest + Svelte Component Testing - Complete Reference Pattern (2025-10-01)

**Mandatory Pattern**: Use proper Vitest configuration with Svelte plugins and follow official testing patterns

**Key Requirements**:

- Vitest config must have `extends: true` in unit test project to inherit root Svelte plugins
- Use @testing-library/svelte for component testing (not raw mount() unless necessary)
- Create wrapper components for advanced Svelte 5 features (context, bindings, snippets)
- Use `flushSync()` for synchronous state updates in tests
- Use `$effect.root()` when testing code with effects outside components

**Why This Pattern**:

- Vitest projects with custom plugins override root plugins unless `extends: true` is set
- Testing Library provides better abstractions and query methods than raw Svelte API
- Wrapper components handle Svelte features that have no programmatic API
- Without proper plugin configuration, Svelte files fail to parse with Vite errors

**Vitest Configuration Pattern**:

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import { sveltekit } from '@sveltejs/kit/vite';
import { svelteTesting } from '@testing-library/svelte/vite';

const sveltekitPlugins = await sveltekit();

export default defineConfig({
	// Root plugins - available to all projects that use extends: true
	plugins: [svelteTesting(), ...sveltekitPlugins],

	test: {
		include: ['tests/unit/**/*.{test,spec}.{js,ts}'],
		exclude: ['tests/e2e/**/*'],
		globals: true,
		environment: 'jsdom',
		setupFiles: ['./tests/setup.ts'],

		projects: [
			// Unit test project
			{
				extends: true, // CRITICAL: Inherits root plugins!
				test: {
					name: 'unit',
					include: ['tests/unit/**/*.{test,spec}.{js,ts}'],
					environment: 'jsdom'
				}
			},
			// Storybook project with its own plugins
			{
				extends: true, // Still needs extends for base config
				plugins: [storybookTest()], // Additional plugins
				test: {
					name: 'storybook',
					browser: { enabled: true }
				}
			}
		]
	}
});
```

**Basic Component Testing Pattern (Simple Components)**:

```typescript
// tests/unit/lib/components/Button.test.ts
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/svelte';
import userEvent from '@testing-library/user-event';
import Button from '$lib/components/ui/button/button.svelte';

describe('Button Component', () => {
	it('renders with text', () => {
		render(Button, { props: { children: 'Click me' } });

		expect(screen.getByRole('button')).toHaveTextContent('Click me');
	});

	it('handles click events', async () => {
		const user = userEvent.setup();
		const handleClick = vi.fn();

		render(Button, { props: { children: 'Click me', onclick: handleClick } });

		await user.click(screen.getByRole('button'));
		expect(handleClick).toHaveBeenCalledOnce();
	});

	it('disables when loading', () => {
		render(Button, { props: { children: 'Loading', loading: true } });

		const button = screen.getByRole('button');
		expect(button).toBeDisabled();
	});

	it('changes props reactively', async () => {
		const { component } = render(Button, { props: { children: 'Initial' } });

		await component.$set({ children: 'Updated' });
		expect(screen.getByRole('button')).toHaveTextContent('Updated');
	});
});
```

**Advanced Pattern: Wrapper Component for Context/Bindings/Snippets**:

When testing components that use Svelte 5 features like context, two-way bindings, or snippets, create a wrapper component:

```svelte
<!-- tests/unit/lib/components/ChildComponent.test.svelte -->
<script>
	import { setContext } from 'svelte';
	import ChildComponent from '$lib/components/ChildComponent.svelte';

	// Props to configure the test
	let { contextKey, contextValue, bindingValue = $bindable() } = $props();

	// Set up context for child
	setContext(contextKey, contextValue);
</script>

<ChildComponent bind:value={bindingValue} />
```

```typescript
// tests/unit/lib/components/ChildComponent.test.ts
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/svelte';
import ChildComponentWrapper from './ChildComponent.test.svelte';

describe('ChildComponent with Context', () => {
	it('reads context value', () => {
		render(ChildComponentWrapper, {
			props: {
				contextKey: 'theme',
				contextValue: { mode: 'dark' }
			}
		});

		// Assert component uses context correctly
		expect(screen.getByTestId('theme-display')).toHaveTextContent('dark');
	});

	it('handles two-way binding', async () => {
		const { component } = render(ChildComponentWrapper, {
			props: {
				contextKey: 'theme',
				contextValue: { mode: 'light' },
				bindingValue: 'initial'
			}
		});

		// Component updates the binding
		await userEvent.click(screen.getByRole('button', { name: 'Update' }));

		// Check binding was updated
		expect(component.bindingValue).toBe('updated');
	});
});
```

**Testing Slots**:

```svelte
<!-- tests/unit/lib/components/Card.test.svelte -->
<script>
	import Card from '$lib/components/Card.svelte';
	let { Component } = $props();
</script>

<svelte:component this={Component}>
	<h1 data-testid="slotted-content">Test Content</h1>
</svelte:component>
```

```typescript
import { render } from '@testing-library/svelte';
import CardWrapper from './Card.test.svelte';
import Card from '$lib/components/Card.svelte';

it('renders slotted content', () => {
	const { getByTestId } = render(CardWrapper, {
		props: { Component: Card }
	});

	expect(getByTestId('slotted-content')).toBeInTheDocument();
});
```

**Testing Runes in Test Files**:

You can use runes directly in `.svelte.test.ts` files:

```typescript
// multiplier.svelte.test.ts
import { flushSync } from 'svelte';
import { test, expect } from 'vitest';
import { multiplier } from './multiplier.svelte.js';

test('Multiplier with runes', () => {
	let count = $state(0);
	let double = multiplier(() => count, 2);

	expect(double.value).toEqual(0);

	count = 5;
	flushSync(); // Flush state updates synchronously
	expect(double.value).toEqual(10);
});
```

**Testing Effects**:

Wrap tests that use `$effect` in `$effect.root()`:

```typescript
// logger.svelte.test.ts
import { flushSync } from 'svelte';
import { test, expect } from 'vitest';
import { logger } from './logger.svelte.js';

test('Effect tracking', () => {
	const cleanup = $effect.root(() => {
		let count = $state(0);
		let log = logger(() => count);

		flushSync();
		expect(log).toEqual([0]);

		count = 1;
		flushSync();
		expect(log).toEqual([0, 1]);
	});

	cleanup(); // Clean up the effect root
});
```

**Query Priority (from Testing Library)**:

Use queries in this priority order:

1. **getByRole** - Most accessible (buttons, links, inputs)
2. **getByLabelText** - Form fields with labels
3. **getByPlaceholderText** - Form fields with placeholders
4. **getByText** - Non-form text content
5. **getByDisplayValue** - Form fields with values
6. **getByAltText** - Images with alt text
7. **getByTitle** - Elements with title attribute
8. **getByTestId** - Last resort with data-testid

**Common Mistakes**:

❌ **WRONG** - Missing `extends: true` in projects:

```typescript
projects: [
	{
		test: { name: 'unit' } // Won't inherit root plugins!
	}
];
```

❌ **WRONG** - Using raw mount() for simple tests:

```typescript
import { mount } from 'svelte';
const component = mount(Component, { target: document.body });
// Harder to query and assert
```

❌ **WRONG** - Not using flushSync for state updates:

```typescript
count = 5;
expect(double.value).toEqual(10); // May fail - not flushed!
```

❌ **WRONG** - Testing complex features without wrapper:

```typescript
// Trying to test context without wrapper - won't work!
render(ChildComponent); // No context set!
```

✅ **CORRECT** - All patterns above:

- Extends root config
- Uses Testing Library
- Uses flushSync
- Uses wrapper for advanced features

**Example Usage from Our Project**:

```typescript
// tests/unit/routes/page.test.ts
import { describe, expect, it } from 'vitest';
import { render, screen } from '@testing-library/svelte';
import Page from '../../../src/routes/+page.svelte';

describe('+page.svelte', () => {
	it('renders welcome heading', () => {
		render(Page);

		const heading = screen.getByRole('heading', { level: 1 });
		expect(heading).toBeInTheDocument();
	});

	it('renders call-to-action buttons', () => {
		render(Page);

		const getStartedButton = screen.getByRole('link', { name: /get started/i });
		expect(getStartedButton).toHaveAttribute('href', '/login');
	});
});
```

**What Breaks Without This**:

- Missing `extends: true`: Vite parsing errors like "Failed to parse source for import analysis"
- No wrapper for context: Child component can't access context, tests fail
- No flushSync: Race conditions in tests, intermittent failures
- Using wrong queries: Tests break with minor HTML changes
- Not using Testing Library: Tests are brittle and harder to maintain

**Verification**:

1. Run `pnpm test:unit --run` - all tests pass
2. Check vitest.config.ts - unit project has `extends: true`
3. Tests use `screen` queries from Testing Library
4. Complex components have wrapper test files (.test.svelte)
5. State updates use `flushSync()` where needed

**Reference Documentation**:

- Official Svelte Testing: https://svelte.dev/docs/svelte/testing
- Testing Library Svelte: https://testing-library.com/docs/svelte-testing-library/example/
- Svelte Society Recipes: https://www.sveltesociety.dev/recipes/testing-and-debugging/unit-testing-svelte-component

**Key Takeaways**:

1. **Configuration First**: Always ensure `extends: true` in Vitest projects
2. **Use Testing Library**: Better abstractions than raw Svelte API
3. **Wrapper Components**: Required for context, bindings, snippets
4. **flushSync Everywhere**: Svelte updates are async, flush for deterministic tests
5. **Query Wisely**: Prefer accessible queries (getByRole) over test IDs

---
