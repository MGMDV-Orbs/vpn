# VPN Orb
A CircleCI orb which allows a build to connect to MGM Resorts GlobalProtect VPN and pass subsequent steps to be taken.

https://circleci.com/orbs/registry/orb/mgmresorts/vpn

## Setup
Add the following environment variables to your build

- $VPN_HOST
- $VPN_SERVER
- $VPN_USER
- $VPN_PASSWORD
- $VPN_GATEWAY_IP
- $CONTAINER_REGISTRY_URL
  - Value should be `705869507755.dkr.ecr.us-west-2.amazonaws.com/vpn-open-connect`

Alternatively, you can also pass these is in as parameters to the orb. Please see `vpn.yml` for parameter names.

## Usage

You can import this orb in your `.cirlceci/config.yml`

```yml
version: 2.1

orbs: 
  vpn: mgmresorts/vpn@0.1.0

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
```
