# Angular Project Setup

## 1. About

How to install and configure ESLint, Prettier, lint-staged and Husky in an Angular Application.

IMHO - many build systems require much configuration and a careful hand to maintain them. From my experience it's easy to tip over the edge where getting too technical only causes problems between different versions of Angular, plugins and third-party libraries. Keep it simple. In this setup we will use `ESLint`, `Prettier`, `lint-staged` and `Husky` to run pre-commit hook to lint and format our code.

---

## 2. Versions

The versions used when creating this document:

```
Angular 15.2.4
ESLint 8.33.0
Husky 8.0.3
lint-staged 13.2.0
Prettier 2.8.7
```

---

## 3. Setup process

Follow this setup process to help keep your codebase maintainable.

### 3.1. Clone empty GitLab repo

```
$ git clone https://github.com/<user>/<project-name>.git
```

### 3.2 Step into the repo

```
$ cd <project-name>
```

### 3.3. Create Angular project

```
$ ng new <project-name> --style=scss --routing=true --directory ./
```

### 3.4. Install ESLint

**Why:** Linting is performed to reduce errors and improve the overall quality of your codebase.

```
$ ng add @angular-eslint/schematics
```

The ESLint configuration is now done thanks to the `ng add` command that sets up all necessary config files. The following command can be run to check for linting errors.

```
$ ng lint
```

If the linter finds errors, the following command can be run to try and fix them automatically.

```
$ ng lint --fix
```

### 3.5. Install Prettier

**Why:** Prettier is used to format code. This make your codebase more readable and maintainable.

```
$ npm install prettier --save-dev
```

After the installation add a configuration file.

```
$ touch .prettierrc.json
```

Add the following config to the `.prettierrc.json` file.

```json
{
    "tabWidth": 4,
    "useTabs": false,
    "singleQuote": true,
    "semi": true,
    "bracketSpacing": true,
    "arrowParens": "avoid",
    "trailingComma": "es5",
    "bracketSameLine": true,
    "printWidth": 100
}
```

Change the following property in the `.editorconfig` file.

```
indent_size = 4
```

In Visual Studio Code add the following properties to the `settings.json` file. The file is located in the `.vscode` directory.

```json
{
    "editor.tabSize": 4,
    "editor.insertSpaces": true,
    "editor.detectIndentation": false
}
```

Create `.prettierignore` that is based on the `.gitignore` file.

```
$ cp .gitignore .prettierignore
```

Append this following config to the `.prettierignore` file. This will prevent formatting of files located in the assets directory such as json-files and third-party libraries.

```
# Project specific files
/src/assets/
```

The Prettier-configuration is now done. The following command can be run to format all files in the project.

```
$ npx prettier --write .
```

### 3.6. Install lint-staged

**Why:** Lint-Staged helps by only run linting and formatting on files that are ment to be commited. Without this the entire project will be targeted and unfinished code will block the commit.

```
$ npm install lint-staged --save-dev
```

Add a lint-staged section to the `package.json` file after the script section.

```json
"lint-staged": {
    "**/*.{js,ts,jsx,tsx}": [
        "eslint",
        "prettier --write"
    ],
    "**/*.{html,scss,css,json,xml}": [
        "prettier --write"
    ]
}
```

**Note:** Both of the globs run in parallel, this works with the above config beacuse they don't target the same files.

### 3.7. Install Hysky

**Why:** Husky makes working with Git Hooks easier. The pre-commit-hook will make shure that both linting and formatting have been done before a commit is allowed to be made.

```
$ npm install husky --save-dev
```

Add new scripts to the script section in the `package.json` file.

```json
"scripts": {
    "configure-husky": "npx husky install && npx husky add .husky/pre-commit \"npx lint-staged\""
}
```

Run the configure-husky script. This will make shure that Husky is installed and create the pre-commit hook.

```
$ npm run configure-husky
```

Thats it! Everything is now configured. When you do a `$ git commit -m "My changes"` both linting using ESLint and code formatting using Prettier will be applied to the project.

**Note:** If it becomes necessary to bypass this process, the `$ git commit -m "Important update" --no-verify` command can be used.
