## Example pipeline

### Deploy an application to GOV.UK PaaS

This pipeline clones a public open source git repository, and deploys it to GOV.UK PaaS

#### Requirements

There must be the following variables set:

- `paas-username`
- `paas-password`

#### Pipeline

<pre><code>---
resources:
  - name: my-git-repo
    type: git
    source:
      branch: master
      uri: https://github.com/alphagov/my-repo.git
  
  - name: my-paas-app
    type: cf
    source:
      api: https://api.london.cloud.service.gov.uk
      organization: my-service-team
      space: my-space
      username: ((paas-username))
      password: ((paas-password))

jobs:
  - name: test-and-build
    public: false
    plan:
      - get: my-git-repo
        trigger: true
  
      - task: build
        config:
          platform: linux
  
          image_resource:
            type: docker-image
            source:
              repository: ruby
              tag: 2.7

          inputs:
            - name: my-git-repo
          outputs:
            - name: my-git-repo

          run:
            path: sh
            dir: my-git-repo
            args:
              - -exc
              - |
                bundle install
                bundle exec middleman build
  
      - put: deploy-to-paas
        params:
          manifest: my-git-repo/manifest.yml
          path: my-git-repo
</code></pre>
