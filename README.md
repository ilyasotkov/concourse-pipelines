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

Initialize GitLab Flow:

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

### Add pipeline to Concourse server

```sh
fly --target ci \
        set-pipeline \
        --pipeline rails-react-boilerplate-pipeline \
        --config pipelines/gitlab-flow-semver.yml \
        --load-vars-from params/rails-react-boilerplate.yml \
        --var "gcloud-auth=$(cat ../exekube-alpha/live/prod/owner-key.json)"
```
