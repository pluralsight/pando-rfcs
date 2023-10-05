- Start Date: 2023-09-07
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it
is a Markdown file so all examples are _theory_ and **have not been prototyped**.
You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not
nit-picking the "gotchas" in code examples. :heart:

# Summary

This RFC proposes a new CLI for Pando. The CLI will be a single binary that
will be used to interact with projects that use Pando. The CLI will be written in
Node.js and will be published to NPM.

# Basic example

```bash
pnpx pando setup
```

# Motivation

## Why are we doing this?

As Pando grows, we need a way to ensure that all projects that use Pando are setup
correctly in a way that requires minimal effort from the developer. This CLI will
be the primary way to interact with Pando.

## What use cases does it support?

- Setup a project to use Pando
- Update a project to use the latest version of Pando

## What is the expected outcome?

The outcome should be a CLI that makes it easy to setup and update projects that
use Pando. This means it should ultimately relieve the developer from having to
manually configure/update their project to use Pando.

The CLI should also be a delightful experience to use. This means that it should
be easy to use and should provide helpful feedback when something goes wrong. The
feedback should be both visual and written (colors, emojis, animations, etc.).

# Detailed design

## CLI commands

### `setup`

The `setup` command will be used to setup a project to use Pando. It should do the
following:

#### 1. Welcome the user to Pando CLI

This should be a delightful welcome message that includes emoji visuals at the least.

#### 2. Install required dependencies

The CLI should install the following dependencies based on local PM of choice (bun, npm, yarn, pnpm):

- `@pluralsight/{react,icons}`

#### 3. Install required devDependencies

The CLI should install the following dependencies based on local PM of choice (bun, npm, yarn, pnpm):

- `@pluralsight/panda-preset`
- `@pandacss/dev`
- `postcss` (if not already installed)
- `autoprefixer` (if not already installed)

#### 4. Setup Panda & PostCSS

The CLI should follow the steps outlined in the [Panda documentation](https://panda-css.com/docs/installation/postcss) for using PostCSS.

Additionally, the CLI should add `autoprefixer` to the postcss config file.

#### 5. Ask the user some Panda configuration questions

The CLI should ask the user the following questions to help fill in any remaining Panda configuration gaps needed:

- **What files would you like to include?** This should use a placeholder that matches step 3 in the PostCSS setup documentation.
- **What files would you like to exclude?** This should use a placeholder that matches step 3 in the PostCSS setup documentation.
- **What framework would you like to use?** This should use a placeholder of `react`. [Available Options](https://panda-css.com/docs/references/config#jsxframework)

The config file should include the comments that using `panda init` generally provides.

#### 6. Create `pando.entry.css` file

The CLI should create a `pando.entry.css` file in the `src` directory of the project. This file should be the main CSS file for the project.

For the file contents, the CLI should write Step 5 of the PostCSS setup documentation to the file along with some custom font setup:

```css
@font-face {
  font-display: swap;
  font-family: 'PS Commons';
  font-style: normal;
  font-weight: 500 800;
  src: url('https://fonts.pluralsight.com/ps-tt-commons/v1/ps-tt-commons-variable-roman.woff2')
    format('woff-variations');
}

@font-face {
  font-display: swap;
  font-family: 'DM Mono';
  font-style: normal;
  font-weight: 400;
  src: url('https://fonts.pluralsight.com/dm-mono/v1/dm-mono-regular.ttf')
    format('truetype');
}

:root {
  --font-pando: 'PS Commons', 'Gotham SSm A', 'Gotham SSm B', Arial, sans-serif;
  --font-pando-mono: 'DM Mono', 'Fira Code', 'Operator Mono', 'Source Code Pro', monospace;
}
```

#### 7. Update the index.html `head` tag

The CLI should update the `head` of the `index.html` file to include the following:

_NOTE: This should be added **before** any other tag in the `head`._

```html
<link
  rel="preload"
  href="https://fonts.pluralsight.com/ps-tt-commons/v1/ps-tt-commons-variable-roman.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>
```

> Note: Some frameworks like Next.js will have font helpers to preload fonts. This should be used instead of the above. See the [Panda documentation](https://panda-css.com/docs/guides/fonts#setup) for more details.

#### 7. Update the scripts in the `package.json` file

Following the step 4 of the PostCSS setup documentation, the CLI should update the `package.json` file to include the `panda:generate`, `prepare`, and `build:css` scripts.

- `panda:generate` should be set to `panda generate`
- `build:css` should be set to `postcss src/pando.entry.css -o dist/index.css` or whatever the main CSS file is

#### Typescript

TS projects should also be able to extend their `tsconfig.json` file to include the `tsconfig.pando.json`
file from the `@pluralsight/panda-preset` package. This config would simply mirror the TS config
used in the Pando React project.

# Drawbacks

## Why should we _not_ do this?

### Implementation cost, both in term of code size and complexity

Since this is a Node CLI which has a lot of tooling already available, the
implementation cost should be relatively low. The CLI will be written in Typescript.

### Whether the proposed feature can be implemented in user space

It's a CLI so it will be implemented in user space.

### The impact on teaching people a new tool

The CLI would be added to the Pando documentation and would be taught as part of
the Pando setup process. So this should be much easier than what we have today.

### Integration of this feature with other existing and planned features

This CLI will be the primary way to interact with Pando. It will be used to setup
and update projects that use Pando so there isn't much integration with other
features.

### Cost of migrating existing Pando applications (is it a breaking change?)

There shouldn't be any breaking changes or costs to migrating existing Pando
applications. The CLI will be used to update existing projects to use the latest
version of Pando and that's it.

# Alternatives

## What other designs have been considered?

We could continue to use the manual way that is currently used to setup
projects to use Pando. This would mean that the developer would have to manually
add the `panda.config.ts` file, `postcss.config.cjs` file, and update the
`package.json` file to include the `panda:generate` and `prepare` scripts.

### What is the impact of not doing this?

See above.

# Adoption strategy

The adoption strategy would be to add the CLI to the Pando documentation and
teach it as part of the Pando setup process.

# How we teach this

- Add the CLI to the Pando documentation
- Teach the CLI as part of the Pando setup process
- Teach the CLI as part of the update process

# Unresolved questions

TBD