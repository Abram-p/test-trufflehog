# test-trufflehog
Just testing some trufflehog stuff

The flow control for this is based on the Pull Request being opened, as seen in the .github/workflows/sscanner.yaml file. 
This means that the secret scanner runs the second a pull request is opened. Also were trying to drive this secret scanning and security closer to the 
developers which means we will also use pre-commit to catch secrets before they are committed to the repo in any way so that we dont have to purge them from 
any branches or history or anything like that. Follow the steps below to get this stuff set up.

Setup
Install trufflehog
# MacOS users
brew install trufflesecurity/trufflehog/trufflehog

# Docker
docker run --rm -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/trufflesecurity/test_keys

# Docker for M1 and M2 Mac
docker run --platform linux/arm64 --rm -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/trufflesecurity/test_keys

# Binary releases
Download and unpack from https://github.com/trufflesecurity/trufflehog/releases

# Compile from source
git clone https://github.com/trufflesecurity/trufflehog.git
cd trufflehog; go install


Install pre-commit(requires python)

'''
pip install pre-commit
'''

If it does not exist already create a .pre-commit-config.yaml file in the root of the project
and add the following information

repos:
- repo: local
  hooks:
    - id: trufflehog
      name: TruffleHog
      description: Detect secrets in your data.
      entry: bash -c 'trufflehog git file://. --since-commit HEAD --only-verified --fail --max-depth 1'
      # For running trufflehog in docker, use the following entry instead:
      # entry: bash -c 'docker run --rm -v "$(pwd):/workdir" -i --rm trufflesecurity/trufflehog:latest git file:///workdir --since-commit HEAD --only-verified --fail'
      language: system
      stages: ["commit", "push"]

Install the pre-commit config hooks, from the root of the directory run
'''
pre-commit install
'''