# Tilde CI

CI/CD configuration repository for the Tilde macOS app.

## Overview

This repository contains the continuous integration configuration for building and testing the [tilde-mac](https://github.com/carlinw/tilde-mac) project. By keeping the CI configuration separate from the production code, we maintain a clean separation of concerns.

## Structure

```
tilde-ci/
├── .github/
│   └── workflows/
│       └── build-and-test.yml    # Main CI workflow
├── fastlane/
│   ├── Fastfile                   # Fastlane configuration
│   └── README.md                  # Fastlane documentation
├── Gemfile                        # Ruby dependencies
└── README.md                      # This file
```

## Workflow

The CI workflow (`build-and-test.yml`) performs the following steps:

1. **Checkout Repositories**
   - Checks out this `tilde-ci` repository for CI configuration
   - Checks out `tilde-mac` repository for the actual source code

2. **Clean Build**
   - Runs clean.sh to remove any previous build artifacts

3. **Build**
   - Builds the Mac app using `build.sh`
   - Extracts and reports the build number

4. **Unit Tests**
   - Runs Swift unit tests in parallel
   - Tests are in the `Tests/TildeTests` directory

5. **UI Tests**
   - Runs UI tests using Fastlane
   - Tests are in the `TildeUITests` directory

6. **Report Results**
   - Uploads test reports and build logs as artifacts
   - Generates a test summary in the GitHub Actions UI

## Triggers

The workflow runs:
- On push to `main` or `master` branches
- On pull requests to `main` or `master` branches
- On manual trigger via GitHub Actions UI
- Daily at 2 AM UTC (scheduled run)

## Self-Hosted Runner

This workflow is designed to run on a self-hosted macOS runner with:
- macOS 14+ (Sonoma or later)
- Xcode installed at `/Applications/Xcode.app`
- Ruby and Bundler for Fastlane
- Access to clone the `tilde-mac` repository

## Running Locally

To run the tests locally from this repository:

```bash
# Install dependencies
bundle install

# Run UI tests (requires tilde-mac to be checked out at ../tilde-mac)
bundle exec fastlane test

# Or specify a custom path
PROJECT_DIR=/path/to/tilde-mac bundle exec fastlane test
```

## Setup

To set up this CI for your own fork:

1. Fork both `tilde-mac` and `tilde-ci` repositories
2. Set up a self-hosted macOS runner following [GitHub's guide](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners)
3. If the `tilde-mac` repository is private, create a Personal Access Token (PAT) with `repo` scope
4. Add the PAT as a secret named `TILDE_MAC_PAT` in the `tilde-ci` repository settings
5. Push to the `main` branch to trigger the first workflow run

## Artifacts

Test results and build logs are uploaded as artifacts and retained for 30 days. Each artifact is named with the run number and build version:
- `test-reports-{run_number}-{build_version}`

## Maintenance

- **Ruby Dependencies**: Update with `bundle update`
- **GitHub Actions**: Actions are set to specific versions (e.g., `@v4`) for stability
- **Xcode**: Ensure the runner has the required Xcode version installed

## License

This CI configuration is part of the Tilde project.
