# CodeBeaver GitHub Action

This GitHub Action runs [CodeBeaver](https://github.com/codebeaver-ai/codebeaver-ai) tests in your CI/CD pipeline. CodeBeaver is an open-source testing automation tool that leverages AI to simplify the testing process.

## Features

- 🤖 Run end-to-end tests using natural language descriptions
- 🧪 Generate and maintain unit tests automatically for your codebase
- 🐛 Detect potential bugs and provide detailed fix explanations
- ⚡ Reduce testing overhead while improving code quality

## Usage

### Basic usage

```yaml
name: Run CodeBeaver Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Run CodeBeaver Tests
        uses: codebeaver-ai/codebeaver-action@v1
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

### Complete example with separate jobs for unit and E2E tests

```yaml
name: CodeBeaver Testing

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]
  # Optional: Run E2E tests on release
  release:
    types: [published]

jobs:
  unit-tests:
    name: Run Unit Tests
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run CodeBeaver Unit Tests
        uses: codebeaver-ai/codebeaver-action@v1
        with:
          action-type: "unit"
          max-files-to-test: "20"
          verbose: "true"
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}

  e2e-tests:
    name: Run E2E Tests
    runs-on: ubuntu-latest
    # Only run E2E tests on release or when explicitly requested
    if: github.event_name == 'release' || github.event_name == 'workflow_dispatch'
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run CodeBeaver E2E Tests
        uses: codebeaver-ai/codebeaver-action@v1
        with:
          action-type: "e2e"
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          STARTING_URL: "https://staging.example.com" # Optional: override URL from config
```

## Inputs

| Input               | Description                                     | Required | Default          |
| ------------------- | ----------------------------------------------- | -------- | ---------------- |
| `action-type`       | Type of tests to run (`unit`, `e2e`, or `both`) | No       | `both`           |
| `file-path`         | Path to the file to analyze for unit tests      | No       |                  |
| `config-file`       | Path to the YAML configuration file             | No       | `codebeaver.yml` |
| `template`          | Testing framework template (e.g., pytest, jest) | No       |                  |
| `max-files-to-test` | Maximum number of files to test                 | No       | `10`             |
| `verbose`           | Enable verbose logging output                   | No       | `false`          |

## Environment Variables

| Variable         | Description                         | Required |
| ---------------- | ----------------------------------- | -------- |
| `OPENAI_API_KEY` | Your OpenAI API key                 | Yes      |
| `STARTING_URL`   | Override the base URL for E2E tests | No       |

## Setup

### 1. Create a configuration file

Create a `codebeaver.yml` file in the root of your repository:

```yaml
# Unit test configuration
unit:
  from: pytest # The unit testing framework you want to use

# E2E test configuration
e2e:
  login-test: # Name of the test
    url: "https://your-app-url.com" # Starting URL
    steps:
      - "Navigate to the login page"
      - "Enter 'testuser' in the username field"
      - "Enter 'password123' in the password field"
      - "Click the 'Login' button"
      - "Check that the dashboard page is displayed"
```

### 2. Add your OpenAI API key

Add your OpenAI API key as a secret in your GitHub repository:

1. Go to your repository on GitHub
2. Click on "Settings" > "Secrets and variables" > "Actions"
3. Click on "New repository secret"
4. Name: `OPENAI_API_KEY`
5. Value: Your OpenAI API key
6. Click "Add secret"

## Supported Languages and Frameworks

CodeBeaver currently supports:

- Python with pytest
- TypeScript/JavaScript with jest or vitest

## Resources

- [CodeBeaver GitHub Repository](https://github.com/codebeaver-ai/codebeaver-ai)
- [CodeBeaver Documentation](https://docs.codebeaver.ai)
- [Discord Community](https://discord.gg/4QMwWdsMGt)

## License

This project is licensed under the AGPL-3.0 License - see the [LICENSE](LICENSE) file for details.
