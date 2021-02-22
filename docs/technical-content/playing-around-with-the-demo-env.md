---
layout: default
title: Playing Around with the Demo Environment
parent: Technical Content
---
# Playing Around with the Demo Environment

## Getting Started

* Clone the [Police Data Manager Repository]()
* Clone the [Demo Instance Files Repository]()
* Replace the contents of the `src/instance-files` folder with the contents of the Demo Instance Files
* Start the application with `docker-compose up app` command
* Point your browser to `https://localhost/`


## Known Limitations

* As you bring up the application with this minimal setup, you will be missing some functionality of the real deployed version.
  1. Google Maps API integration
  2. Authentication using Auth0
  3. Storage of generated letters and attachment using AWS S3