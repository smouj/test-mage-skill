name: Test Mage
description: A specialized skill that conjures comprehensive test suites from thin air, automating the creation of unit, integration, and end-to-end tests for various programming languages and frameworks.
tags: testing, automation, coding, magic
version: 1.0.0
author: SMOUJ
dependencies: pytest>=7.0.0, jest>=29.0.0, mocha>=10.0.0, selenium>=4.0.0, cypress>=12.0.0
environment_variables:
  - TEST_MAGE_API_KEY: Required API key for external test data generation (optional for local mode)
  - TEST_MAGE_MODE: Set to 'offline' for local-only generation without external dependencies
  - TEST_MAGE_TIMEOUT: Maximum time in seconds for test generation (default: 300)
requirements:
  - Node.js >= 16.0.0 or Python >= 3.8
  - Git repository with source code
  - Existing package.json or requirements.txt for dependency analysis
verification_steps:
  - Run 'test-mage verify' to check environment setup
  - Ensure no existing test files conflict with generated ones
  - Backup current test directory before execution
---

## Purpose

Test Mage is designed to eliminate the manual drudgery of writing test suites by magically generating high-quality, comprehensive tests based on code analysis. It analyzes source code, identifies functions, classes, and endpoints, and creates test cases that cover edge cases, error handling, and integration scenarios.

### Real Use Cases

- **Rapid Prototyping**: When developing a new feature in a React app, Test Mage generates Jest tests for components, hooks, and API calls, ensuring UI interactions and state changes are covered without writing a single test file.
- **Legacy Code Refactoring**: For a Python Flask API with undocumented functions, Test Mage scans the codebase and produces pytest test suites that validate existing behavior, preventing regressions during modernization.
- **CI/CD Pipeline Enhancement**: In a Node.js microservice, Test Mage creates Cypress end-to-end tests for critical user flows like login, checkout, and data submission, integrating seamlessly into GitHub Actions for automated testing.
- **Cross-Language Testing**: For a polyglot project with JavaScript frontend and Python backend, Test Mage generates matching test suites for both, ensuring API contracts are tested end-to-end.

## Scope

Test Mage operates on specific commands tailored to different testing scenarios. It supports the following CLI commands, each with flags for customization:

- `test-mage conjure <source-dir> --framework=<jest|pytest|mocha> --type=<unit|integration|e2e> --coverage=<80-100>`: Generates tests for the specified directory using the chosen framework and test type, targeting minimum coverage percentage.
- `test-mage analyze <file-path> --output=<json|yaml> --depth=<shallow|deep>`: Analyzes a single file or module, outputting a structured breakdown of testable components in JSON or YAML format.
- `test-mage integrate --ci=<github|gitlab|jenkins> --branch=<main|feature-branch>`: Adds generated tests to CI pipelines, configuring runners and triggers for the specified branch.
- `test-mage mock <api-endpoint> --method=<GET|POST> --response=<status-code>`: Creates mock services for external dependencies, simulating API responses for isolated testing.
- `test-mage verify <test-dir> --run=<true|false> --report=<html|json>`: Validates generated tests by running them and producing detailed reports.

Scope excludes manual test writing, performance benchmarking (use separate tools), and production deployment.

## Work Process

Test Mage follows a structured, iterative process to ensure reliable test generation:

1. **Code Scanning**: Analyze the source directory using AST parsing to identify functions, classes, imports, and dependencies. For example, in a TypeScript project, it detects React components and their props.

2. **Test Blueprint Creation**: Generate a blueprint JSON outlining test cases, including positive/negative scenarios, mocks, and assertions. This step considers existing test patterns in the codebase.

3. **Framework Adaptation**: Map the blueprint to the specified testing framework (e.g., convert to Jest describe/it blocks or pytest fixtures).

4. **Edge Case Injection**: Automatically add tests for null inputs, boundary values, and exception handling based on type hints or JSDoc.

5. **Integration Setup**: If requested, configure test runners (e.g., add Cypress configuration for e2e tests) and inject into CI scripts.

6. **Validation Run**: Execute a subset of tests to verify they pass against the current code, flagging any immediate failures.

7. **Final Output**: Write test files to a designated directory (default: `tests/generated/`), with clear naming conventions like `test-component.spec.js`.

Dependencies are installed automatically if missing, and environment variables are checked at startup.

## Golden Rules

- **No Overwrite Without Backup**: Never modify existing test files; always generate new ones in isolated directories to prevent data loss.
- **Coverage Thresholds**: Enforce minimum 85% coverage for unit tests; warn if below, but don't fail generation.
- **Language Parity**: Maintain consistent test quality across languages—e.g., Python tests use fixtures similarly to Jest's beforeEach.
- **Isolation First**: Prioritize unit tests over integration; generate mocks for external calls to avoid flaky tests.
- **Version Pinning**: Use specific framework versions (e.g., jest@29.5.0) to ensure reproducibility.
- **Error Transparency**: Log all generation decisions in a `test-mage.log` file, including why certain tests were skipped.
- **Incremental Mode**: When re-running on updated code, diff against previous blueprints to add only new tests, avoiding duplication.

## Examples

### Example 1: Generating Unit Tests for a React Component
**User Input**: `test-mage conjure src/components/Button --framework=jest --type=unit --coverage=90`

**Output**:
- Generates `tests/generated/Button.spec.js` with tests for prop variations, click handlers, and accessibility attributes.
- Sample Generated Code:
  ```javascript
  describe('Button Component', () => {
    it('renders with default props', () => {
      render(<Button />);
      expect(screen.getByRole('button')).toBeInTheDocument();
    });
    it('handles onClick callback', () => {
      const mockOnClick = jest.fn();
      render(<Button onClick={mockOnClick} />);
      fireEvent.click(screen.getByRole('button'));
      expect(mockOnClick).toHaveBeenCalledTimes(1);
    });
  });
  ```
- Verification: Run `npm test tests/generated/Button.spec.js` to ensure 90% coverage.

### Example 2: End-to-End Tests for an API Endpoint
**User Input**: `test-mage conjure routes/auth.js --framework=cypress --type=e2e --coverage=95`

**Output**:
- Creates `tests/generated/auth.cy.js` with login flow tests.
- Sample Generated Code:
  ```javascript
  describe('Authentication Flow', () => {
    it('logs in successfully', () => {
      cy.visit('/login');
      cy.get('[data-cy=email]').type('test@example.com');
      cy.get('[data-cy=password]').type('password123');
      cy.get('[data-cy=submit]').click();
      cy.url().should('include', '/dashboard');
    });
  });
  ```
- Troubleshooting: If tests fail due to network issues, use `test-mage mock /api/login --method=POST --response=200` to create mocks.

### Example 3: Analyzing a Python Module
**User Input**: `test-mage analyze utils/calculator.py --output=json --depth=deep`

**Output**:
- JSON file detailing functions like `add(a, b)`, `divide(a, b)`, with test case suggestions.
- Sample Output Snippet:
  ```json
  {
    "functions": [
      {
        "name": "add",
        "params": ["a", "b"],
        "test_cases": [
          {"input": [1, 2], "expected": 3},
          {"input": [-1, 1], "expected": 0}
        ]
      }
    ]
  }
  ```

## Rollback Commands

- `test-mage rollback <test-dir>`: Removes all generated test files in the specified directory, restoring from a pre-generation backup.
- `test-mage undo --commit=<hash>`: Reverts the last commit if it included generated tests, using `git reset --hard <hash>`.
- `test-mage clean --framework=<jest|pytest>`: Deletes framework-specific configs and dependencies added during generation.
- `test-mage reset`: Clears all caches and logs, returning Test Mage to a clean state for fresh runs.