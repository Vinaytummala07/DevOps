# DevOps

name: Enforce Tagging Criteria

on:
  push:
    tags:
      - '*'  # Trigger on any tag push

jobs:
  check-tag-criteria:
    runs-on: ubuntu-latest
    steps:
      - name: Check branch and tag name
        run: |
          branch_name=$(git symbolic-ref --short HEAD)
          tag_name=$(echo $GITHUB_REF | sed 's/refs\/tags\///')

          # Ensure tags are only created on main or release branches
          if [[ "$branch_name" != "main" && ! "$branch_name" =~ ^release/ ]]; then
            echo "Tags can only be created on main or release branches."
            exit 1
          fi

          # Check tag name follows semantic versioning
          if [[ ! $tag_name =~ ^v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
            echo "Invalid tag name: $tag_name. Tags must follow semantic versioning (vX.X.X)."
            exit 1
          fi
