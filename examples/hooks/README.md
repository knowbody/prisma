# YAML Structure

This example demonstrates `hooks` property available to us in `prisma.yml`.

## Get started

> **Note**: `prisma` is listed as a _development dependency_ and _script_ in this project's [`package.json`](./package.json). This means you can invoke the Prisma CLI without having it globally installed on your machine (by prefixing it with `yarn`), e.g. `yarn prisma deploy` or `yarn prisma playground`. If you have the Prisma CLI installed globally (which you can do with `npm install -g prisma`), you can omit the `yarn` prefix.

### 1. Download the example & install dependencies

Clone the Prisma monorepo and navigate to this directory or download _only_ this example with the following command:

```sh
curl https://codeload.github.com/graphcool/prisma/tar.gz/master | tar -xz --strip=2 prisma-master/examples/yaml-structure
```

Next, navigate into the downloaded folder and install the NPM dependencies:

```sh
cd yaml-structure
yarn install
```

### 2. Deploy the Prisma database service

You can now [deploy](https://www.prismagraphql.com/docs/reference/cli-command-reference/database-service/prisma-deploy-kee1iedaov) the Prisma service (note that this requires you to have [Docker](https://www.docker.com) installed on your machine - if that's not the case, follow the collapsed instructions below the code block):

```sh
yarn prisma deploy
```

<details>
 <summary><strong>I don't have <a href="https://www.docker.com">Docker</a> installed on my machine</strong></summary>

To deploy your service to a public cluster (rather than locally with Docker), you need to perform the following steps:

1.  Remove the `cluster` property from `prisma.yml`
1.  Run `yarn prisma deploy`
1.  When prompted by the CLI, select a public cluster (e.g. `prisma-eu1` or `prisma-us1`)
1.  Replace the [`endpoint`](./src/index.js#L23) in `index.js` with the HTTP endpoint that was printed after the previous command

</details>

### 3. `hooks` property of `prisma.yml`

The `hooks` property is used to define terminal commands which will be executed by the Prisma CLI before or after certain commands.

Currently, only `post-deploy` hook is available. In this example, we are using the `post-deploy` hook to perform the following tasks

```yml
hooks:
  post-deploy:
    - echo "Deployment finished"
    - graphql get-schema --project db
    - graphql prepare
```

* Printing "Deployment finished"

* Getting the latest schema

* Running `graphql prepare` to generate TS bindings

Note that these commands work closely in conjunction with `.graphqlconfig.yaml` file that looks like this and directs the output of generate schema and typescript bindings.

```yml
projects:
  db:
    schemaPath: generated-schema.graphql
    extensions:
      endpoints:
        default: 'http://localhost:4466/hooks'
      prisma: prisma.yml
      codegen:
        input:
          schema: generated-schema.graphql
        output:
          typings: generated-prisma.ts
        generator: typegen
        language: typescript
```
