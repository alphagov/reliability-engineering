## Pool resource

The [pool-resource](https://github.com/concourse/pool-resource) can be used for
locking resources or environments between separate pipelines.

### Examples

#### Locking an environment for testing

If you had two apps deployed to a test environment by separate pipelines, and
you wanted to run functional tests in these pipelines, without running two sets
of functional tests at the same time.

#### Preventing concurrent deploys

If you had several microservices deployed by separate pipelines, but you want
to ensure that only a single microservice could be deployed at one time.

#### Limiting concurrency

If you have several pipelines which use the same environment, but you want to
ensure that 3 deployments can be active at the same time.

### How it works

The pool-resource is backed by a git repository, where each pool is a directory
with two subdirectories:

- claimed
- unclaimed

where unclaimed locks are files inside the `unclaimed` directory, and `claimed`
directory contains files representing claimed locks.

```
.
├── pool-1
│   ├── claimed
│   └── unclaimed
├── pool-2
│   ├── claimed
│   └── unclaimed
└── pool-3
    ├── claimed
    │   └── f3cb3823-a45a-49e8-ab41-e43268494205
    └── unclaimed
```

### Setting up the pool-resource

You should create a pipeline to set up and manage your team's pool-resource.

The following pipeline sets up three pools:

- pool-1
- pool-2
- pool-3

⚠️ Re-running the `init-pool` job will reset the state of any locks.

<pre><code>---
resources:
  - name: pool-repo
    type: git
    icon: github
    source:
      branch: pool
      uri: ((readonly_codecommit_pool_uri))
      private_key: ((readonly_codecommit_private_key))

jobs:
  - name: init-pool
    serial: true
    plan:
      - task: init-pool
        config:
          platform: linux
          image_resource:
            type: registry-image
            source:
              repository: governmentpaas/git-ssh
              tag: latest
          outputs:
          - name: repo
          run:
            dir: repo
            path: sh
            args:
            - -euo
            - pipefail
            - -c
            - |
              git init .
              for pool in pool-1 pool-2 pool-3; do
                mkdir -p "$pool/claimed"
                mkdir -p "$pool/unclaimed"
                touch "$pool/claimed/.keep"
                touch "$pool/unclaimed/.keep"
                git add "$pool"
                git commit -m "setup $pool"
              done

      - put: pool-repo
        params:
          repository: pool
          force: true
</code></pre>

### Using the pool-resource

Once you have set up a pool-resource, you can use it to claim and release
locks.

<pre><code>---
resource_types:
  - name: pool
    type: registry-image
    source:
      repository: concourse/pool-resource
      tag: 1.1.3

resources:
  - name: pool
    type: pool
    icon: pool
    source:
      branch: pool
      uri: ((readonly_codecommit_pool_uri))
      private_key: ((readonly_codecommit_private_key))
      pool: pool-1

  - name: my-lock-config
    type: mock
    source:
      create_files:
        name: my-lock
        metadata: ''

jobs:
  - name: add-my-lock
    serial: true
    plan:
      - get: my-lock-config

      - put: pool
        params:
          add: my-lock-config

  - name: use-my-lock
    serial: true
    plan:
      - put: pool
        params:
          claim: my-lock

      - task: do-a-thing-with-my-lock
        config: # omitted for brevity

      - put: pool
        params:
          release: pool
</code></pre>
