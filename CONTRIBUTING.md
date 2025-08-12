# Contributing to Hella Site Base

Thank you for considering contributing to Hella Site Base! This document outlines the process for contributing to the project.

## Getting Started

1. Fork the repository
2. Clone your fork: `git clone https://github.com/hella-web3/hella-site-base.git`
3. Create a new branch: `git checkout -b feature/your-feature-name`

## Development Environment

Follow the setup instructions in the README.md to set up your local development environment:

```bash
# Install rbenv
brew install rbenv
rbenv init

# Install ruby
rbenv install 3.4.3
rbenv global 3.4.3

# Update bundler
gem install bundler -v 2.6.7

# Install dependencies and run the site locally
bundle install && bundle exec jekyll serve --livereload
```

## Pull Request Process

1. Ensure your code follows the project's style guidelines
2. Update documentation as needed
3. Make sure all tests pass
4. Submit a pull request with a clear description of the changes and any relevant issue numbers

## Code Style Guidelines

- Follow the existing code style in the project
- Use meaningful variable and function names
- Write clear comments for complex logic
- Keep functions small and focused on a single task

## Reporting Issues

When reporting issues, please include:

- A clear and descriptive title
- Steps to reproduce the issue
- Expected behavior
- Actual behavior
- Screenshots if applicable
- Your environment details (OS, browser, etc.)

## Community Guidelines

- Be respectful and inclusive
- Provide constructive feedback
- Help others when you can
- Follow the code of conduct

Thank you for contributing to Hella Site Base!