# Concourse Pipelines

## GitLab Flow Pipeline with SemVer

Read the official introduction to GitLab Flow: <https://about.gitlab.com/2014/09/29/gitlab-flow/>

### Features

- Application deployed as a Helm release
- Automatic semantic versioning
- Two branches: `master` (released to a staging environment) and `production` (released to a production environment)

### Parameters

Example parameters can be found in [params/gitlab-flow-semver.example.yml](/params/gitlab-flow-semver.example.yml)

You can also use Kubernetes Secrets as the credential manager for Concourse: [params/kubernetes.example](/params/kubernetes.example)

### Initialize GitLab Flow in an application repo

```bash
#!/bin/bash
set -ex

branches=("rc-version" "final-version")
inital_version="0.1.0"

git init
git add .
git commit -m "Initial commit"

for branch in "${branches[@]}"; do
    git checkout --orphan $branch
    git rm --cached -r .
    cat <<EOF > .gitignore
*
!.gitignore
!version
EOF

    echo "$inital_version" > version
    git add .
    git commit -m "Add initial $branch file"
    git checkout master
done
```

### Add pipelines to your Concourse server

#### apps-pipelines pipeline

```sh
fly --target ci \
        set-pipeline \
        --config pipelines/concourse-pipelines.yml \
        --pipeline apps-pipelines
```

#### forms-app pipeline

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

#### rails-react-boilerplate pipeline

```sh
fly --target ci \
        set-pipeline \
        --config pipelines/gitlab-flow-semver.yml \
        --pipeline rails-react-boilerplate
```

```sh
fly --target ci \
        destroy-pipeline \
        --pipeline rails-react-boilerplate
```
