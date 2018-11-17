# VPN Orb
A CircleCI orb which allows a build to connect to MGM Resorts GlobalProtect VPN and pass subsequent steps to be taken.

https://circleci.com/orbs/registry/orb/mgmresorts/vpn

## Usage

You can import this orb in your `.cirlceci/config.yml` and provide the `context: vpn` to load all necessary environment variables for this orb in the MGMResorts CircleCI org.

```yml
version: 2.1

orbs: 
  vpn: mgmresorts/vpn@0.2.0

jobs:
  build:
    executor: vpn/aws
    steps:
      # Any steps passed here will be executed after the vpn connection is established.
      - vpn/with-vpn-connection:
          after-vpn-steps:
            - checkout
            - restore_cache:
                key: rooms-npm-cache-{{ checksum "package.json" }}
            - run:
                name: Diagnostics
                command: | 
                  echo NodeJS: `node -v`
                  echo NPM: `npm -v`
                  echo AWS CLI: `aws --version`
                  echo Docker: `docker -v`
                  echo Access Key: $AWS_ACCESS_KEY_ID
            - run: 
                name: Install NPM dependencies
                command: | 
                npm install
workflows:
  version: 2
  build_and_test:
    jobs:
      - verify-tests-pass
          context: vpn
```
