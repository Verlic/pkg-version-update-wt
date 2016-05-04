# pkg-version-update-wt

Webtask/webhook to update internal modules dependencies when a new version of one of them is released.

## webtask configuration

* Add 2 secrets to your webtask `GITHUB_USER` and `GITHUB_API_KEY`:

  ```
  wt create index.js \
    --bundle \
    --no-parse \
    --name auth0challenge \
    --secret GITHUB_USER=$GITHUB_USER \
    --secret GITHUB_API_KEY=$GITHUB_API_KEY
  ```
* Add your `github organization name` parameter at the end of the webtask url https://webtask.it.auth0.com/api/run/:container_name/:webtask_name/:org_name

## Contribute

```bash
git clone https://github.com/ludohenin/pkg-version-update-wt.git
cd pkg-version-update-wt
npm install
npm run test -- -w
```

## Usage

To use this webtask, Git repositories in an organisation must be set up accordingly.

Repositories to be updated must include a `package.json` file and an `npm-shrinkwrap.json` file.

To create `package.json` run `npm init`.

Dependencies can be added to `package.json` using `npm install [package_name] --save` or `npm install [package_name] --save-dev` if the package is a development dependency.

When the dependencies are in a `package.json` file, `npm install; npm shrinkwrap` creates an `npm-shrinkwrap.json` file.

A GitHub hook must be set up to hook releases up to the webtask.
A hook can be attached to a single repository or to an organisation, in either the repository or organisation's settings > Webhooks.

The content type of the webhook must be `application/json` and the payload URL must be constructed from the webtask URL and the organisation name as per the above "webtask configuration" section.

The "Release" event triggers the update of `package.json` and `npm-shrinkwrap.json` files.
Repositories depending on the released repository gain or update a branch named `auto_version_update` and a PR is created or updated.

To quickly release a package, perform the following commands:

```
export VERSION=2.0.0 # semver version of choice
```

```
brew install hub # or platform specific alternative https://github.com/github/hub
npm version $VERSION
hub release create -m "New release" $VERSION
```
