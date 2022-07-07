# [.NET Core Web API Starter](https://github.com/dalbitresb12/aspnet-starter)

This [template](https://github.com/dalbitresb12/aspnet-starter) should help get you started developing a Web API using .NET Core 6. It includes opinionated configurations for JetBrains Rider, Visual Studio, [commitlint](https://commitlint.js.org/) with commit hooks (powered by [Husky.Net](https://alirezanet.github.io/Husky.Net/)).

This project was generated using Rider's .NET Core Web API template using version 2022.1.1.

## Recommended IDE Setup

[JetBrains Rider](https://www.jetbrains.com/rider/) or [Microsoft Visual Studio 2022](https://visualstudio.microsoft.com/)

## Repository Setup

You should start by replacing almost all the references to the `Starter.API` project. This is a non-exhaustive list of possible changes you'll have to make (use your IDEs refactor option so that it helps you):

- C# namespaces in projects
- Root namespaces in `.csproj` files (see the `RootNamespace` property in it)
- Project filenames: `Starter.API.csproj` and `Starter.API.Tests.csproj`
- After renaming projects: `InternalsVisibleTo` and `ProjectReference` properties in `.csproj` files
- Migration files (they include the `Starter.API.Migrations` namespace)
- `Starter.API` and `Starter.API.Tests` folders should be renamed to your new root namespace
- Project references and folder names in `Starter.API.sln`
- GitHub Actions workflow files
  - `dotnet.yml`: change the `PROJECT_NAME` environment variable
  - `reports.yml`: change the `PROJECT_NAME` environment variable (its in two different places)
- In case you plan on using/supporting JetBrains Rider: rename the folder inside the `.idea` folder that has the project name
  - Use your IDE search in files option inside this folder
  - `applicationhost.config`: if you have this file, it is auto-generated. Don't edit it manually
- Update the README file

### GitHub Actions

The workflow files for GitHub Actions are setup to use [Cloudflare Pages](https://pages.dev/) for deploying the [LivingDoc](https://docs.specflow.org/projects/specflow-livingdoc/en/latest/LivingDocGenerator/Generating-Documentation.html) generated by [SpecFlow](https://specflow.org/). Deployments using the new [Direct Upload](https://developers.cloudflare.com/pages/platform/direct-upload/) feature with [Wrangler](https://developers.cloudflare.com/pages/platform/direct-upload/#wrangler-cli) and the new [Action](https://github.com/cloudflare/pages-action).

Some of the changes you should make:

- `dotnet.yml`
  - Change the `PROJECT_NAME` and `CLOUDFLARE_PROJECT_NAME` environment variable
  - If you setup any other long-lived branches, add them to the push branches list
- `reports.yml`
  - Change the `PROJECT_NAME` environment variable (its in 2 different places)
  - Change the `CLOUDFLARE_PROJECT_NAME` environment variable (its in 3 different places)
- If you're not going to be using Cloudflare Pages: remove all the references to it

Secrets you should set on your repository:

- `CLOUDFLARE_ACCOUNT_ID`: Your Cloudflare account ID
- `CLOUDFLARE_API_TOKEN`: API Token with "Cloudflare Pages - Edit" permission

You can find documentation about the Direct Upload with GitHub Actions feature in the [docs](https://developers.cloudflare.com/pages/how-to/use-direct-upload-with-continuous-integration/#use-github-actions).

### Comments Bot

This repository also uses a custom machine account, `dalbitresb12-bot`, to post comments on PRs and a couple of other things.

If you want to continue using the custom machine account, register a new account and then create a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

If you don't want to create a new account, replace any references to the variable `BOT_PAT` with the `GITHUB_TOKEN` and replace the `comment-author` option of the "Find previous comment on pull request" step in `reports.yml` with `github-actions[bot]`.

Secrets you should set on your repository:

- `BOT_PAT`: Create a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with the scope `public_repo` (or `repo` if your repository is private)

### Additional notes

- Any files that are Git ignored **should not be modified manually**. Regenerate them as needed.
- You should probably also change the data in the [LICENSE](LICENSE) file.

## Project Setup

You should also have [Node.js](https://nodejs.org/) and [NPM](https://npmjs.com/) installed for commitlint to work (any supported LTS version should be fine).

If you don't have Node.js installed, it is highly encouraged that you install it using a version manager:

- Linux/MacOS: use [nvm](https://github.com/nvm-sh/nvm)
- Windows: use [nvm-windows](https://github.com/coreybutler/nvm-windows)

After that, open the project on your preferred IDE from the [list above](#recommended-ide-setup).

Your IDE should automatically restore all the tools needed (Husky.Net, `dotnet-ef` and npm dependencies) during project restore. If this doesn't happen run `dotnet restore` in the terminal.

### 🚨 **IMPORTANT** 🚨

JetBrains Rider might prompt you to install EF Core tools automatically with a pop-up when you open the project. You can safely ignore this prompt, as the tool is already included in the projects manifest and should be installed during project restore. To verify, run the following command:

```sh
# The output should be the one below the command
$ dotnet ef

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 6.0.5

<Usage documentation follows, not shown.>
```

## Database Connection

This project is configured to use MySQL as the database, using [Pomelo's MySQL connector](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql). The connection string will be read from the Secret Manager tool. You can read more about it [here](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0).

To allow the app to connect to the database, set the connection string as a secret using the .NET CLI:

```sh
# Initialize the secrets storage for this project
$ dotnet user-secrets init --project Starter.API
# Don't forget to replace {YOUR_CONNECTION_STRING} with the appropriate value
$ dotnet user-secrets set "DbConnectionString" "{YOUR_CONNECTION_STRING}" --project Starter.API
Successfully saved DbConnectionString = {YOUR_CONNECTION_STRING} to the secret store.
# Alternatively, move to project directory to avoid having to use `--project`
$ cd Starter.API/
$ dotnet user-secrets set "DbConnectionString" "{YOUR_CONNECTION_STRING}"
Successfully saved DbConnectionString = {YOUR_CONNECTION_STRING} to the secret store.
# Check that your value has been saved correctly
$ dotnet user-secrets list
DbConnectionString = {YOUR_CONNECTION_STRING}
```

## Migrations

This app is configured to use database migrations. You can read more about them [here](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/).

If this is the first time you're running the app, you probably won't have the database created. You can have EF create your database and create your schema from the migration files. This can be done via the following:

```sh
dotnet ef database update
```

If you make any changes to the database models, don't forget to update the migration scripts and re-sync your local database schema with the new schema:

```sh
# Don't forget to replace {YOUR_CHANGE_NAME} with a description of the changes you did to the model
# Note that we give migrations a descriptive name, to make it easier to understand the project history later
# You can use this like if it was a commit for your version control system
$ dotnet ef migrations add {YOUR_CHANGE_NAME}
# Check the migration files before applying any migrations, as they could cause data loss
# You can now apply your migration as before
$ dotnet ef database update
```

Whenever you create a new migration, EF Core will create the files needed to describe the migration. This files must be added to version control.

If you named your migration `SomeChanges`, three files will be added to your project under the Migrations directory:

- `XXXXXXXXXXXXXX_SomeChanges.cs` -- The main migrations file. Contains the operations necessary to apply the migration (in Up) and to revert it (in Down).
- `XXXXXXXXXXXXXX_SomeChanges.Designer.cs` -- The migrations metadata file. Contains information used by EF.
- `AppDbContextModelSnapshot.cs` -- A snapshot of your current model. Used to determine what changed when adding the next migration.

The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.

### 🚨 **ALWAYS CHECK THE MIGRATION FILE** 🚨

It's **super important** that you **always** check the migration file (`XXXXXXXXXXXXXX_SomeChanges.cs`), since EF Core might drop columns that were just renamed and **you'll lose all the data in them if you apply the migration**. For more information on how to customize the migration code generated by EF Core, read [here](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing#customize-migration-code).

For more advanced usage of migrations, check the documentation [here](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing).

## Development server

Start a development server by running the ISS Express configuration from your IDE. Don't forget to trust the generated SSL certificate.

If you want to customize the SSL certificate used for IIS, check the following Stack Overflow [answer](https://stackoverflow.com/a/43676994/15040387).

## Build

Run `dotnet build` to build the project. The build artifacts will be stored in the `bin/` directory.

## License

[MIT](LICENSE)
