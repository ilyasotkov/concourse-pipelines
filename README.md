# Concourse Pipelines

## GitLab Flow Pipeline with SemVer

Read the official introduction to GitLab Flow: <https://about.gitlab.com/2014/09/29/gitlab-flow/>

### Features

- Application deployed as a Helm release
- Automatic semantic versioning
- Two branches: `master` (released to a staging environment) and `production` (released to a production environment)

### Parameters

Example parameters can be found in [params/gitlab-flow-semver.example.yml](/params/gitlab-flow-semver.example.yml)

### Add pipelines via Fly CLI

### rails-react-boilerplate-pipeline

```sh
fly --target ci \
        set-pipeline \
        --pipeline rails-react-boilerplate-pipeline \
        --config pipelines/gitlab-flow-semver/pipeline.yml \
        --load-vars-from params/rails-react-boilerplate.yml \
        --var "kube_config=$(cat ../exekube-alpha/config/kube/config)"
```

```sh
fly --target ci \
        destroy-pipeline \
        --pipeline rails-react-boilerplate-pipeline
```
