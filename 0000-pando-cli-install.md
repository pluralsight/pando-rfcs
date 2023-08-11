- Start Date: 2023-08-10
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it
is a Markdown file so all examples are _theory_ and **have not been prototyped**.
You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not
nit-picking the "gotchas" in code examples. :heart:

# Summary

A cli ([command line interface](https://www.w3schools.com/whatis/whatis_cli.asp)) tool to easily scaffold a Pando project

# Motivation

One of the most difficult aspects of pattern adoption is the initial hurdle - getting folks to actually try it out. There could be a variety of reasons for this. Some possible scenarios: 
1. Time constraints can make it difficult to try out a new tool if it does not have proven value
1. Documentation can be difficult to parse
1. Some people just learn better by doing than reading

A cli would allow the user to enter a command into a terminal and immediately interact with the design system. This could be used as a tool to onboard and learn Pando, or include and use in an existing project.

The expected outcome would be that there would be an increased adoption and usage of Pando. This could lead to more positive feedback overall, or at least feedback directly related to Pando rather than how implementation details. Because the user would have the opportunity to focus more on the actual usage of Pando, potential negative feelings associated with troubleshooting configuration just to get something to show up on the screen would be alleviated. 

Another possible outcome is that time in supporting installation issues would be decreased. Hypothetically the purpose of the cli would be to remove as many steps as possible to getting an instance of Pando up and running. If the cli flow used clear language and interactive step-by-step instruction, there is less room for user error as well as more opportunity to inject personality into messaging.

# Detailed design

There could be multiple ways to approach making a scaffolding tool available.

## An npm package

The basic function of an npm package would be 
1. Run a command
1. Command executes a script
1. Script clones a git project into a named directory

This would allow the user to enter a command like `[npm/yarn/pnpm] create pando-design@1 [project-name]` to materialize a project structure similarly to the experience of `create-react-app`. The project structure (abbreviated) would be as follows:

```
pando-design
  --| public
  --| src
    [initial app content]
  --| bin
    --| scaffold-app.js
  package.json
  README.md
```

The package.json would contain a bin field which would run `scaffold-app.js`, which would contain the script to execute the scaffold. This would look like:

```
"bin": {
    "scaffold-pando": "./scaffold-app.js"
  }
```

The script itself would clone a git repository into a directory that the user names. Each different framework that Pando supports would need to have it's own individually maintained repository. Inside the script, a prompt would be included to allow the user to input which framework they prefer to use, which would clone a different repository into their directory.

## Yeoman

The tool [yeoman](https://yeoman.io/) exists to make it easy to generate an app. Yeoman is a widely adopted and trusted tool that makes creating a scaffolding package straightfoward. There are many scaffolded projects available on yeoman that get a lot of engagement.

Yeoman even has a (generator-generator)[https://github.com/yeoman/generator-generator] that walks through the steps of creating a generator. The drawback would be that to scaffold a project with Pando would require that the user download Yeoman, which introduces a dependency.

## Migrating an Existing Codebase

In this scenario I would recommend going the route of a codemod. Codemods are scripts that make larger changes in a codebase based on a set of rules. I'd recommend looking into a library such as [jscodeshift](https://github.com/facebook/jscodeshift) to implement this. 

The way in which I would approach this initially would actually be to follow the Pando documentation in [How to Add Pando to a Website](https://design.pluralsight.com/docs/learn/get-started/installation/add-to-website/) and include the steps in codemods.

# Drawbacks

1. A cli tool would require additional maintenance. The possibility of supporting multiple frameworks would make it necessary for the maintainers of the cli to be knowledgeable in all of those frameworks, rather than putting the responsibility on the user.
1. Users may not trust a cli to make changes on their local machine.
1. In the case of a codemod, it would be necessary to have consistent parallels between previous and new patterns to be successful. 

# Alternatives

* Documentation on implementation of Pando for multiple different frameworks with examples
* Linking to a collection of custom Pando templates on github to fork, clone, and customize from the Pando docs

# Adoption strategy

This would not be a breaking change. The initial implementation of a cli tool would not impact Pando users at all, as it could be centered entirely around starting a project or including Pando in an existing project. The adoption strategy initially would simply be including the cli tool in the Pando documentation to educate developers on its existence, as well as promoting it throughout the Pando user community.

# How we teach this

This idea would be best presented through the Pando documentation itself as well as alerting Pando developers to its existence through inclusion in the Pando docs, blog posts, and email or slack notifications.

It would be necessary to update the Pando documentation to include the Pando cli - both make its existence known as well as allow users to do a deeper dive into how it works. It would be important to include documentation for different frameworks it supports as well as whether it is solely for a new projects or includes the ability to assist the migration of an existing project.

As the purpose of this feature is to simplify the installation and usage of Pando, it should not require manual teaching for Pando developers. The cli would include step-by-step instructions in installation as well as usage, including links to documentation for futher knowledge. 

# Unresolved questions

1. Will the Pando cli be solely for initial install, or would it also support migration to future Pando versions?
1. Which frameworks & tools will Pando support?
  1. My initial recommendation would be React, but Solid JS, Vue, and others would be helpful to include as well. This selection would become available in the prompts as additional frameworks become available.