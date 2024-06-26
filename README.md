name: Deploy Application

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          fetch-depth: '0'

      - name: Generate Git Tag
        id: generate_tag
        run: |
          # Find the latest tag if available
          LATEST_TAG=$(git describe --tags --abbrev=0 || echo "")
          
          if [ -z "$LATEST_TAG" ]; then
            # If no tags found, start with initial version and branch name
            NEW_TAG="V1.0.1_main"
          else
            # Extract the version number and branch name from the latest tag
            VERSION_BRANCH=$(echo "$LATEST_TAG" | cut -d'_' -f1)
            VERSION=$(echo "$VERSION_BRANCH" | grep -oE '[0-9]+\.[0-9]+\.[0-9]+')
            BRANCH_NAME=$(echo "$LATEST_TAG" | cut -d'_' -f2)
            
            # Increment the version number
            NEW_VERSION=$(echo "$VERSION" | awk -F'.' '{print $1 "." $2 "." $3 + 1}')
            
            # Construct the new tag
            NEW_TAG="V${NEW_VERSION}_${BRANCH_NAME}"
          fi
          
          echo "Generated new tag: $NEW_TAG"
          echo "NEW_TAG=$NEW_TAG" >> $GITHUB_ENV

      - name: Push Git Tag
        run: |
          git config user.name "Nithish@NetXD"
          git config user.email "nithish.t@netxd.com"
          git tag $NEW_TAGsdfsdfsdf
          git push origin $NEW_TAG
