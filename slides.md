---
theme: the-unnamed
highlighter: shiki
lineNumbers: true
transition: fade-out
# use UnoCSS
css: unocss
layout: section
---

# Question

<v-click>
    <p>Do you have one of the following...</p>
</v-click>

<ul>
    <v-click>
        <li><code>utils/</code></li>
    </v-click>
    <v-click>
        <li><code>extras/</code></li>
    </v-click>
    <v-click>
        <li><code>utils.js</code></li>
    </v-click>
    <v-click>
        <li><code>utils.ts</code></li>
    </v-click>
</ul>

---

### And are they copied from different projects?

---

## Then you might have a problem

---
layout: cover
---
# From Zero to Hero
## Building and Shipping Your First JavaScript Library

---
layout: about-me
helloMsg: Hello!
name: Mike Hartington
imageSrc: https://cdn.bsky.social/imgproxy/NLfAgX_EC-YWBNneVYGfTvaYOQ5mROH9kgFoEcXy2OY/rs:fill:1000:1000:1:0/plain/bafkreih5czye4acyme3j5ckt4bmwkypwqyqcq4mgecjq55g2lavdy573n4@jpeg
job: 'Director of DevRel @ ionic'
social1: '@mhartington'
social2: '@mhartington.io'
---

<!--  My Notes -->

---

# What is a JS Library?

<p v-click>Spoiler, it's complicated</p>

---

## According to npm:

> A package is a file or directory that is described by a package.json file. A package must contain a **package.json** file in order to be published to the npm registry.

<!--
Basically, a bit of code that you can publish and make available
-->

---

## Very Basic

As bare bones as it can be


```txt {all|3}
./my-lib
|-- package.json
|-- index.js
```

Package.json

```json {all|4}
{
  "name": "my-lib",
  "version": "1.0.0",
  "main": "index.js",
}
```

---

If it's that simple

Let's make our own package!

---

# Why Bother?

- Code isolation
- Sharing across projects
- Versioned seprate from everything else


---

## `To the Terminal!`

---

## Summary

---

## File Structure

```shell
.
├── index.js
├── node_modules/
├── package-lock.json
├── package.json
└── safe.js
```

---

## Requiring Dependecies

```js
const os = require('os');
const path = require('path');
const stream = require('stream');

const fs = require('fs-extra');

const statSafe = require('./safe').stat;
const readdirSafe = require('./safe').readdir;
```

---

## Exporting Functions

```js
const statSafe = require('./safe').stat;
const readdirSafe = require('./safe').readdir;
async function fileToString(filePath) {...}

module.exports = {
  statSafe,
  readdirSafe,
  fileToString,
};
```

---

## Exposing to Node

```json
{
  "name": "mh-file-utils",
  "version": "0.0.1",
  "main": "index.js",
  "dependencies": {
    "fs-extra": "^11.1.1"
  }
}
```

---

## Congrats

You just published an npm package 🎉

---

## But it's 2023

---

# The CJS-ESM Problem

---

## What is CJS?

- CommonJS: Package format from 2009
- Uses `requires` and `module.exports`
- Mainly only used in Node

---

## What is ESM?

- ES Modules: Package format from 2015
- Uses `import` and `export`
- Browser, Node, and beyond!

<!-- # Probably the most familiar format for web devs -->

---

- Both `ESM` and `CJS` are supported in Node*
- Up to package authors to configure
- It can be...confusing

---

## `To the Terminal!`

---

## Summary

---

## File Structure

```shell
.
├── index.mjs
├── node_modules
├── package-lock.json
├── package.json
└── safe.mjs
```

---

## Requiring Dependecies

```js
import { readFile } from 'fs/promises'
import 'fs-extra/esm';

import { stat as statSafe, readdir as readdirSafe } from './safe.mjs';
```

---

## Exporting Functions

```js
export default {
  statSafe,
  readdirSafe,
  fileToString,
};
```

---

## Exposing to Node

```json {4}
{
  "name": "mh-file-utils",
  "version": "0.0.1",
  "type": "module",
  "main": "index.js",
  "dependencies": {
    "fs-extra": "^11.1.1"
  }
}
```

---

## Congrats

It's about to get more complicated 🙃

---

# Main vs Module vs Exports


---

## `main`
`"main": "./index.cjs"`

- CJS was exposing the main entry
- Only supports one entry point
- Not deprecated, but specialized

---

## `module`
`"module": "./index.mjs"`

- Older ESM-older entry point
- Only one ESM entry point
- Not recommended (expect for TypeScript)

---

## `exports`


- Object for defining multiple entry points
- Mostly replaces older `module`
- Supports both ESM & CJS

---


## `To the Terminal!`

---

## Summary

---

- Use `exports` when possible
- ESM should be the default (it's 2023)
- [Dual Packages](https://nodejs.org/api/packages.html#dual-commonjses-module-packages) should be avoided

---

# Adding a build step

---

## Build Process
- Only if you need it
- Simplifies what you ship
- Can make dev more complex

---

## `tsup`
- `esbuild` based build system
- JavaScript, TypeScript, and more
- It's not webpack 🎉

---

## `To the Terminal!`

---

## Alternatives

- unbuild (unified not "un")
- Rebust ecosystem
- Used by vite (internall), and others

---

<Tweet id="1662254955007229952" />

---

# Automation

Making releasing this easy

---

## [Conventional Commits](https://www.conventionalcommits.org)
- Meaningful commits to drive versioning
- `chore()`, `feat(scope)`, `BREAKING`
- Makes your team write good messages

---

## Semantic Release
- Fully automated release process
- Commit-drive versioning
- Connect with CI to handle relese process

[semantic-release-cli](https://github.com/semantic-release/semantic-release/blob/master/docs/usage/getting-started.md#getting-started)


---

## `To the Terminal!`

---

## Summary

---

## Init `semantic-release`

```shell
npx semantic-release-cli setup
? What is your npm registry? 'https://registry.npmjs.org/'
? What is your npm username? 'mhartington'
? What is your npm password? '[hidden]'
? Provide a GitHub Personal Access Token? ''
? What CI are you using? 'Github Actions'
```

---

## Add a workflow

```yml
name: CI
on:
  push:
    branches:
      - stable
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm ci
      - run: npm build
      - name: Release
        env:
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npm run semantic-release
```

---

## Have meaningful commits

```shell
git commit -m 'feat(scope): add a new feature'
git commit -m 'chore(): update formatting'
git commit -m 'fix(scope): return correct error code'
```

---

### Congrats, you've shipped your fist Library!

---

## Wrapping up
- You do not need to do all of this
- A simple `package.json` + `index.js` is also valid
- JS Ecosystem is confusing 🙃
- But you got this

---

[mhartington/first-js-lib-demo](https://github.com/mhartington/first-js-lib-demo)

Branches for references
- `main`
- `esm-port`
- `esm-cjs`
- `esm-better`
- `automation`

---

# Questions?

<br />
<br />

## &lt;/html&gt;
