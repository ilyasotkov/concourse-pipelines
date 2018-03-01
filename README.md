# Concourse Pipelines

## GitLab Flow Pipeline with SemVer

Read the official introduction to the GitLab Flow workflow: <https://about.gitlab.com/2014/09/29/gitlab-flow/>

### Features

- Application deployed onto a GKE cluster as a Helm release
- Automated semantic versioning
- Manual version bumps: patch, minor, major
- Two branches: `master` (released to a staging environment) and `production` (released to a production environment)

### Example applications

- [forms-app](https://github.com/ilyasotkov/forms-app)
- [rails-react-boilerplate](https://github.com/ilyasotkov/rails-react-boilerplate)

### Pipeline parameters / credentials

Example parameters can be found in [params/gitlab-flow-semver.example.yml](/params/gitlab-flow-semver.example.yml)

You can also use Kubernetes Secrets as the credential manager for the pipeline: [params/kubernetes.example](/params/kubernetes.example)

Read more about how Concourse sources values from Kubernetes Secrets: <https://github.com/kubernetes/charts/tree/master/stable/concourse#kubernetes-secrets>

### Initialize GitLab Flow in an application repo

This setup stores the application version number on separate branches:

- The `master` branch version is stored on the `rc-version` branch
- The `production` branch version is stored on the `final-version` branch

For every new application, you can add the [`gitlab-flow-init`](/gitlab-flow-init) script to your `$PATH` and to initialize the setup.

### Add pipelines to your Concourse server

#### apps-pipelines pipeline

The easiest way to create all of your pipelines is to have a pipeline that will set and update all other pipelines:

```sh
fly --target ci \
        set-pipeline \
        --config pipelines/concourse-pipelines.yml \
        --pipeline apps-pipelines
```

The pipeline will read the Kubernetes Secret that we would create beforehand ([example](https://github.com/ilyasotkov/concourse-pipelines/blob/master/params/kubernetes.example/apps-pipelines.yaml)) and create all application pipelines listed in the [pipeline manifest file](/params/apps-pipelines-manifest.example.yml) for us.
