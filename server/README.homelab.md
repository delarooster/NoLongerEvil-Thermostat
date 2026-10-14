# Homelab TypeScript server

This branch retains the TypeScript server from upstream commit `3f41dc2`. Upstream's current `main` and `open-source-prototype` branches no longer contain `server/`. Keep this branch as the base for local server maintenance rather than merging it into the fork's upstream-synced `main`.

The source changes here cover the deployed Nest Away/Eco reporting and schedule repair, plus the availability repair that treats valid subscribe requests as device activity and waits ten minutes before declaring the device offline.

The availability image is an overlay on the exact previously deployed Away/Eco repair image. It replaces only the compiled HTTP handler and watchdog modules, so other uncommitted local source changes are not included in the running image.

```bash
cd server
npm ci
npm run type-check
npm run build
docker build --platform linux/amd64 -f Dockerfile.availability \
  -t ghcr.io/delarooster/nolongerevil:availability-heartbeat-20261014 .
```

The overlay build context is restricted by `Dockerfile.availability.dockerignore`. The Kubernetes deployment is maintained separately in `dlr-cluster_homelab/manifests/nolongerevil/deployment.yaml` and pins the resulting amd64 image digest. TLS keys, environment files, and SQLite data stay outside this fork.
