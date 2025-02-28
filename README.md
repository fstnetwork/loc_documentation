# LOC Documentation

[![Build and Deployment](https://github.com/fstnetwork/loc-documentation/actions/workflows/build-and-deploy.yml/badge.svg?branch=main)](https://github.com/fstnetwork/loc-documentation/actions/workflows/build-and-deploy.yml)

FST Network's [**LOC Documentation**](https://documentation.loc.fst.network/), built with React-based [Docusaurus 3.x](https://docusaurus.io/).

---

## Development Guide

### Download and Open Locally

> Prerequisites: [Node.js](https://nodejs.org/en/download/) (latest LTS version), [Git](https://git-scm.com/downloads) and [VS Code](https://code.visualstudio.com/Download)

Upgrade `npm` and install `yarn`:

```bash
npm i -g npm@latest yarn@latest
```

Download the repository, install dependencies and open the folder in VS code (VS Code may prompt for installing a few extensions):

```bash
git clone https://github.com/fstnetwork/loc-documentation.git
cd loc-documentation
code .
```

### Open in CodeSpace

> Prerequisites: Github

On the Github repository, click **<> Code** -> **Codespaces** -> **Create codespace on main**. This will start a web version of VS Code with the repository loaded.

### Install Dependencies

Open a terminal in VS Code and run

```bash
yarn
```

If you open the project in CodeSpace, it will automatically run this command as part of the setup.

### Start Development Server

Build and run a development server, which will hot-reload any changes you've made to most of the files. Be warned that the development server may not throw some build errors.

```bash
yarn start
# run dev server at http://localhost:3000
```

### Build and Run Local Production

Build a production and serve it locally. The production (under `./build`) can be served by any static web server.

Be noted that the build process may fail **due to broken links/anchors in the docs or incompatible upgraded packages**. (Warnings can be ignored. You can override these behaviors in `docusaurus.config.ts`.)

```bash
yarn build
yarn serve
# serve production at http://localhost:3000
```

### Build and Run as Local Docker Container

> Prerequisite: [Docker](https://www.docker.com/products/docker-desktop/)

```bash
# build docker image
docker build . -f ./dev-support/containers/local/Dockerfile -t loc-documentation
# run docker container at http://localhost:8080
docker run -d -p 8080:8080 --rm loc-documentation
```

### Commit Changes

To commit any changes to the repository:

```bash
yarn commit
```

This action will run format for all docs, sync to the Github repo then push local changes to the repository.

**it is recommended to run a build test first** if you've made major changes in docs or have upgraded packages.

### Upgrade Packages

This repository has a Dependabot configuration that will create pull-requests for upgradable versions of packages.

Upgrade locally and commit:

```bash
yarn upgrade {package}@{version}
# or
yarn upgrade {package}@latest # upgrade to latest
# then
yarn commit
```

Or simply merge pull-requests on Github then update the local version:

```bash
git pull origin main && yarn
```

## Docs Maintenance

### Create or Update Docs

All docs are written in `.mdx` format, which supports both Markdown and React/HTML syntax.

Refer to:

-   [Docusaurus Guides](https://docusaurus.io/docs/category/guides)
-   [React](https://react.dev/learn)

### Docs Config

Most of the docs config can be found in [`docusaurus.config.ts`](https://github.com/fstnetwork/loc-documentation/blob/main/docusaurus.config.ts).

Refer to:

-   [Docusaurus API](https://docusaurus.io/docs/api/docusaurus-config)

### Thumbnail

If there are major changes in the index page or navbar items, a new thumbnail photo (`/static/img/thumbnail.jpg`) should be created.

### Spelling Checker

The repo will prompt installing a spelling checker in your VS Code environment, which will generate warnings for unrecognized spellings while viewing `.md` or `.mdx` files.

### Docs and Versioning

Due to the requirement of [multiple versioning in LOC](https://funderstoken.atlassian.net/wiki/spaces/LOCI/pages/1653964803/LOC+Versioning), LOC-Doc current serve the following docs:

| Docs Folder        | Root Routing Path | Docs ID      | Docs                              | [Versioning](https://docusaurus.io/docs/versioning) |
| ------------------ | ----------------- | ------------ | --------------------------------- | --------------------------------------------------- |
| `/src/pages`       | `/`               |              | General pages without sidebars    | No                                                  |
| `/docs`            | `/docs`           | `default`    | General docs have sidebars        | No                                                  |
| `/docs-main`       | `/main`           | `main`       | LOC (Core, Studio, CLI) main docs | Yes                                                 |
| `/docs-sdk-ts`     | `/sdk-ts`         | `sdk-ts`     | SDK for JS/TS docs                | Yes                                                 |
| `/docs-sdk-csharp` | `/sdk-csharp`     | `sdk-csharp` | SDK for C# docs                   | Yes                                                 |
| `/docs-legacy`     | `/legacy/intro`   | `legacy`     | Legacy docs                       | Yes (will not add new versions)                     |

Versioned docs will have the root path of `/{path}/{version}/...`.

Links between docs should _always pointing to the current (latest) docs_. If versioned docs contain links no longer pointing to valid path and/or anchor (which throws error during build), remove the older link.

> `/docs-draft` are for unused old materials, which will be excluded from the site building process.

#### Add a General Page

Create a `.md` or `.mdx` file under `/src/pages` and it will be accessible via `/{page-name}`.

#### Add a New Docs

To create a new docs whether or noe it will be versioned:

1. Create a new root folder `docs-{new-docs-id}`. The new ID must be unique from other docs.

2. Copy `/sidebars/sidebars.ts` and create a new file named `/sidebars/sidebars-{new-docs-id}.ts`

3. Add a docs plugin in `docusaurus.config.ts`:

```typescript
[
    "@docusaurus/plugin-content-docs",
    {
        id: "{new-docs-id}",
        path: "docs-{new-docs-id}",
        routeBasePath: "{new-docs-id}",
        sidebarPath: "./sidebars/sidebars-{new-docs-id}.ts",
        showLastUpdateAuthor: true,
        showLastUpdateTime: true,
        includeCurrentVersion: true,
        lastVersion: "current",
        onlyIncludeVersions: ["current"],
        versions: {
            current: {
                label: "{docs name}",
                path: "",
            },
        },
    },
],
```

The `routeBasePath` is the final routing path on the website, and the pages can be linked via `/{new-docs-id}/{page-name}`.

4. Add the docs link `/{new-docs-id}` in `themeConfig.navbar` and `themeConfig.footer` as well as `/docs/index.mdx`.

5. Update `docsRouteBasePath` and `docsDir` paths in the search plugin `@easyops-cn/docusaurus-search-local` to index the new docs.

6. Complete the docs.

#### Create a Versioned Docs

Versioned docs are _older_ docs preserved for version progressing purposes.

For major or significant minor updates, a new versioned docs should be created:

1. Create a versioned docs from the current docs and marked as the previous version:

```bash
yarn docusaurus docs:version:{id} {previous-version-number}
```

Versioned docs will have the path `/{id}_versioned_docs/version-{previous-version-number}/`.

2. Edit `docusaurus.config.ts` to include the new previous version under `onlyIncludeVersions` and `versions` in the corresponding docs plugin.

3. Modify the current docs (including the release notes, the displayed sidebar version, etc.) according to the latest release.

4. The latest (current) docs also should update its release notes to include the link of all versioned docs.

5. For the main docs (`/docs-main`), update `onlyIncludeVersions` and `versions` in the docs plugin to include versioned docs (which will update the drop down menu). Use `docs-legacy` as the reference. For SDK docs, only the latest versions need to be included (there is no room for extra drop down menus).

#### Create a New SDK Docs

In case of a new SDK docs is required, use one of the SDK docs as template and create all the necessary docs/configurations.
