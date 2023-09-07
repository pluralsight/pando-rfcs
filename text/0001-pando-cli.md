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

- Welcome the user to Pando CLI
- Install required dependencies based on local PM of choice (npm, yarn, pnpm)
- Add a `panda.config.ts` file to the project root that includes the `@pluralsight/pando-panda-preset` preset
- Add a `postcss.config.cjs` file to the project (if it doesn't already exist) root that includes the `autoprefixer` and `pandacss` plugin
- Update the `package.json` file to include the `panda:generate` and `prepare` scripts

#### Typescript

TS projects should also be able to extend their `tsconfig.json` file to include the `tsconfig.pando.json`
file from the `@pluralsight/pando-panda-preset` package. This config would simply mirror the TS config
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