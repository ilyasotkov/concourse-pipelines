# Concourse Pipelines

## GitLab Flow branching / SemVer versioning / Kubernetes release pipeline

Read the official introduction to the GitLab Flow workflow: <https://about.gitlab.com/2014/09/29/gitlab-flow/>

### Features

- Application deployed onto a Kubernetes cluster as a Helm release
- Two branches: `master` (released to a staging environment) and `production` (released to a production environment)
- Automated semantic versioning and tagging
- Ability to include unit and integration tests
- Ability to manually bump application version: patch, minor, major

### Example applications

- [forms-app](https://github.com/ilyasotkov/forms-app)
- [rails-react-boilerplate](https://github.com/ilyasotkov/rails-react-boilerplate)

### Pipeline parameters / credentials

Example parameters can be found in [params/gitlab-flow-semver.example.yml](/params/gitlab-flow-semver.example.yml)

You can also use Kubernetes Secrets as the credential manager for the pipeline: [params/kubernetes.example](/params/kubernetes.example)

Read more about how Concourse sources values from Kubernetes Secrets: <https://github.com/kubernetes/charts/tree/master/stable/concourse#kubernetes-secrets>

### Initialize GitLab Flow in an application repo

Add the [`gitlab-flow-init`](/gitlab-flow-init) script to your `$PATH` and run the script in every new application repository to initialize the branching model.

- The `master` branch version is stored in the `version` file on the `rc-version` branch of the application repo, and always has the `x.y.z-rc.n` form
- The `production` branch version is stored on the `final-version` branch and has the `x.y.z` form

### Add pipelines to your Concourse server

#### Pipeline that manages all other pipelines

The easiest way to create all of your pipelines is to have a pipeline that will set and update all other pipelines:

```sh
fly --target ci \
        set-pipeline \
        --config pipelines/concourse-pipelines.yml \
        --pipeline apps-pipelines
```

The pipeline will read the Kubernetes Secret that we would create beforehand ([example](https://github.com/ilyasotkov/concourse-pipelines/blob/master/params/kubernetes.example/apps-pipelines.yaml)) and create all the pipelines listed in the [pipeline manifest file](/params/apps-pipelines-manifest.example.yml) for us.

#### Manually update pipelines

```sh
fly --target ci \
        set-pipeline \
        --config pipelines/gitlab-flow-semver.yml \
        --pipeline forms-app
```

```sh
fly --target ci \
        destroy-pipeline \
        --pipeline forms-app
```
