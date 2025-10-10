# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2025-10-10

### Added
- Initial release of Wait for Docker Compose Action
- Health endpoint polling functionality with configurable retry logic
- Support for custom URLs, retry attempts, and wait intervals
- Working directory support for docker compose commands
- Comprehensive error handling with Docker Compose logs and container status
- Emoji-enhanced logging for better visibility
- GitHub Action branding with clock icon and blue color

### Features
- **Health Check Polling**: Continuously polls a specified URL until the application responds successfully
- **Configurable Retry Logic**: Customizable maximum attempts and wait intervals between checks
- **Error Diagnostics**: Automatically displays Docker Compose logs and container status on failure
- **Working Directory Support**: Run docker compose commands from any specified directory
- **Clear Logging**: Detailed status updates with emoji indicators for better readability

### Technical Details
- Uses composite action approach for flexibility
- Compatible with bash shell (Linux runners)
- Requires curl and docker compose to be available
- Handles HTTP response codes and connection failures gracefully