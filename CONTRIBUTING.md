# How to contribute

Thank you for investing your time in contributing to our project!

In this guide you will get an overview of the contribution workflow from opening an issue, creating a PR, reviewing, and merging the PR.

## Report an issue

Before raising an issue to this project,
please check if a similar issue already exists or has been closed before.

Then, please search in the issue tracker for similar entries
before submitting your own,
there's a good chance somebody else had the same issue or feature proposal.

If you believe you found a bug, and it's likely possible,
please indicate a way to reproduce it,
what you are seeing and what you would expect to see.

For proposing a new feature, please explain the use case you have in mind,
and create a discussion in GitHub with the label `enhancement`.
You can also create a pull request with the new feature,
and a poll where each telecom operator can vote for it.

## New contributor guide

* [Set up GIT](https://docs.github.com/en/get-started/quickstart/set-up-git)
* [Set up Docker](https://docs.docker.com/desktop/)
* [Collaborating with pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests)

## Coding conventions

Start reading our code and you'll get the hang of it. We optimize for readability:

* We indent using two spaces (soft tabs)
* We use Unix line endings (LF)
* We have configured [VsCode](https://code.visualstudio.com/):
  * to format the code on save
  * to propose needed extensions
* We use [INTEROP common schema](https://github.com/before-interop/common/tree/main/common/schemas) as much as possible by `$ref` in our schemas
* We use the OpenAPI 3.1 specification to describe our API

## How to test your changes

You can easily test your changes with docker by running the following command:

```bash
docker run --name interop -v ${PWD}:/usr/share/nginx/html:ro -d -p 8800:80 nginx:alpine
```

Then open your browser at <http://localhost:8800>

Notes:

* you can stop the container with `docker stop interop` and remove it with `docker rm interop`
* you may need to run `docker system prune` to free some space on your disk
* you may need to deactivate browser cache to see your changes

## Git Practices

* Pull Request cannot be accepted if they contain merge commits.

    Always do a rebase on `upstream/main` before pushing your branch to GitHub.

* Commits should be atomic and semantic.

    Please properly squash your pull requests before submitting them.
    Fixup commits can be used temporarily during the review process
    but things should be squashed at the end to have meaningful commits.
