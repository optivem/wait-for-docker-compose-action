# Wait for Docker Compose Action

[![Example Usage](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/example.yml/badge.svg)](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/example.yml)
[![Test Action](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/test.yml/badge.svg)](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/test.yml)
[![Release](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/release.yml/badge.svg)](https://github.com/optivem/wait-for-docker-compose-action/actions/workflows/release.yml)

[![GitHub release](https://img.shields.io/github/release/optivem/wait-for-docker-compose-action.svg)](https://github.com/optivem/wait-for-docker-compose-action/releases)

A GitHub Action that waits for a Docker Compose application to be ready by polling a health endpoint. This action is useful in CI/CD workflows where you need to ensure your Docker Compose services are fully started and healthy before running tests or other dependent steps.

## Features

- ⏳ Polls a specified health endpoint until the application is ready
- 🔧 Configurable retry attempts and wait intervals
- 📝 Detailed logging with status updates and emoji indicators
- 🐳 Automatic Docker Compose logs and container status on failure
- 📁 Support for custom working directories

## Usage

```yaml
- name: Wait for Docker Compose Application
  uses: optivem/wait-for-docker-compose-action@v1
  with:
    url: 'http://localhost:8080/health'
    max-attempts: 30
    wait-seconds: 10
    working-directory: './docker'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `url` | The URL to check for application readiness (e.g., http://localhost:8080/) | Yes | `http://localhost:8080/` |
| `max-attempts` | Maximum number of attempts before giving up | No | `30` |
| `wait-seconds` | Seconds to wait between attempts | No | `10` |
| `working-directory` | Working directory for docker compose commands | No | `.` |

## Example Workflows

### Basic Usage

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Start Docker Compose
        run: docker compose up -d
      
      - name: Wait for application to be ready
        uses: optivem/wait-for-docker-compose-action@v1
        with:
          url: 'http://localhost:8080/health'
      
      - name: Run tests
        run: npm test
      
      - name: Stop Docker Compose
        run: docker compose down
```

### Advanced Usage with Custom Configuration

```yaml
name: Integration Tests
on: [push, pull_request]

jobs:
  integration-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Start services
        run: docker compose -f docker-compose.test.yml up -d
        working-directory: ./infrastructure
      
      - name: Wait for API to be ready
        uses: optivem/wait-for-docker-compose-action@v1
        with:
          url: 'http://localhost:3000/api/health'
          max-attempts: 60
          wait-seconds: 5
          working-directory: './infrastructure'
      
      - name: Wait for database to be ready
        uses: optivem/wait-for-docker-compose-action@v1
        with:
          url: 'http://localhost:5432'
          max-attempts: 20
          wait-seconds: 3
          working-directory: './infrastructure'
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Cleanup
        if: always()
        run: docker compose -f docker-compose.test.yml down -v
        working-directory: ./infrastructure
```

## How It Works

1. The action polls the specified URL using `curl`
2. If the URL responds successfully (HTTP 2xx), the action succeeds
3. If the URL is not ready, it waits for the specified interval and tries again
4. After the maximum number of attempts, if the URL is still not ready:
   - The action fails
   - Docker Compose logs are displayed (last 50 lines with timestamps)
   - Docker Compose container status is shown

## Requirements

- The action runs on Linux runners (uses bash shell)
- `curl` must be available (pre-installed on GitHub-hosted runners)
- `docker compose` must be available and services must be running

## Error Handling

When the action fails to detect a ready application, it provides helpful debugging information:

- **Logs**: Shows the last 50 lines of Docker Compose logs with timestamps
- **Status**: Displays the current status of all Docker Compose containers
- **Clear messaging**: Indicates exactly what went wrong and after how many attempts

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

### v1.0.0
- Initial release
- Basic health endpoint polling functionality
- Configurable retry logic
- Docker Compose logs and status on failure
