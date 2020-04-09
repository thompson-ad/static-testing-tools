# Static Testing

1. esLint - `npm install --save-dev eslint`. If you look in the .bin directory
   of your node modules, eslint should be there and so you can now run
   `npx eslint` across your project. You will get an error saying "no eslint
   config found", so that's the first thing we need to do.

The default configuration for eslint is to parse over like it's ecmascript v5.

```JSON
{
  // add parser options
  "parserOptions": {
    //  modern js
    "ecmaVersion": 2019,
    // if you split into modules
    "sourceType": "module",
    // so that eslint can pass JSX
    "ecmaFeatures": {
      "jsx": true
    },
  },
  "rules": {
    // what eslint should check for
    // babel add 'use-strict' so we don't have to
    "strict": ["error", "never"],
    "valid-typeof": "error",
    "no-unsafe-negation": "error",
    "no-unused-vars": "error",
    "no-undef": "error",
  },
  "env": {
    // so that it knows what things do not need to be defined like console
    "browser": true
  }
}
```

many editors have an eslint plugin that you can install. This improves your
experience with eslint. This will give you red underlines where you break the
rules and so you don't have to run the script to see where the errors are.

`npx eslint . --fix` will fix errors across your code base (it can't fix
everything).

Eslint has a LOT of rules and it's a pain in the ass to configure them all. It
would be nice if someone could just give us a recommended esLint.

esLint comes with it's own recommended option. Then you don't need any of the
rules apart from strict which would then override whatever is in recommended.

```JSON
{
  // add parser options
  "parserOptions": {
    //  modern js
    "ecmaVersion": 2019,
    // if you split into modules
    "sourceType": "module",
    // so that eslint can pass JSX
    "ecmaFeatures": {
      "jsx": true
    },
  },
  //eslint comes with a recommended
  "extends": ["eslint:recommended"],
  "rules": {
    // what eslint should check for
    // babel add 'use-strict' so we don't have to
    "strict": ["error", "never"]
  },
  "env": {
    // so that it knows what things do not need to be defined like console
    "browser": true
  }
}
```

We want to add a lint script to the npm scripts but we don't want esLint to lint
the dist folder so we add the path to the script that tells it to ignore the
same stuff that the gitignore does.

```JSON
"scripts": {
    "build": "babel src --out-dir dist",
    "lint": "eslint --ignore-path .gitignore ."
},
```

2. Prettier - `npm install --save-dev prettier`

again, should be in nodemodules .bin and you can run `npx prettier --write` and
your code will be nicely formatted.

add a script and provide prettier with a glob that it can use to match files in
the code base. This glob will ensure it matches all files for .js and .json.
Also use the same ignore as the eslint.

```JSON
"scripts": {
    "build": "babel src --out-dir dist",
    "lint": "eslint --ignore-path .gitignore --ext .js,.ts,.tsx .",
    "prettier": "prettier --ignore-path .gitignore --write \"**/*.+(js|json)\""
}
```

add a `.prettierrc` to customise your configuration. you can set on up by using
the prettier.io playground.

now you can run `npm run format and it will pick up your code`.

BUT it would be great if you didn't have to run that script. VSCODE has a
prettier extension.

include it in your settings and turn on format on save.

SOMETIMES eslint and prettier can clash. Install
`npm install --save-dev eslint-config-prettier`.

add it to the extends:

```JSON
{
  // add parser options
  "parserOptions": {
    //  modern js
    "ecmaVersion": 2019,
    // if you split into modules
    "sourceType": "module",
    // so that eslint can pass JSX
    "ecmaFeatures": {
      "jsx": true
    },
  },
  //eslint comes with a recommended
  "extends": ["eslint:recommended", "eslint-config-prettier"],
  "rules": {
    // what eslint should check for
    // babel add 'use-strict' so we don't have to
    "strict": ["error", "never"]
  },
  "env": {
    // so that it knows what things do not need to be defined like console
    "browser": true
  }
}
```

3. TYPESCRIPT - `npm install --save-dev typescript`

change your js file ts one and you already get some benefit without configuring
anything.

go to your node modules .bin and you will see that you have `tsc`. This is the
command you will use too compile your typescript.

create a `tscnofig.json`.

```JSON
{
  "compilerOptions": {
    // because we're compiling with Babel instead of TypeScript
    // and we're only using TypeScript for type checking, we'll set "noEmit"
    // to true so running type checking doesn't generate any files.
    "noEmit": true,
    "baseUrl": "./src"
  }
}
```

By default, babel is not able to parse typescript
`npm install --save-dev @babel/preset-typescript`

this can then be added to the babel-rc

- add eslint support for typescript

`npm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser`

need to edit your eslintrc to use typescript parser and plugins.

4 HUSKY - making sure your validate script runs before code is committed.

`npm install --save-dev husky`

when you install husky it will set up a hidden `.git` directory in the project
`ls -a`

if you like inside this directory there will be a hooks directory. inside this
there will be a whole bunch of files which husky just created.

add a `.huskyrc`and define what happens pre-commit. We want to validate.

if you want to bypass it you can add `--no-verify` after the commit message.

5 Lint-staged:

Rather than failing when a developer has failed to format their files or run
linting on all the files in the project on every commit, it would be even better
to just automatically format the files on commit and only check the relevant
files with eslint. Let’s use lint-staged to run scripts on the files that are
going to be committed as part of our precommit hook.

The cool thing about lint-staged is that it's only running across the files that
are being changed. We've configured it to add files back if they're being
changed by prettier.

lint-staged is really cool because it can even manage patch changes. If you're
only committing part of the file, it will only update the part of the file
that's actually being changed

`npm install --save-dev lint-staged`

add an `.lintstagedrc`

```JSON
{
  "*.js": [
    "eslint"
  ],
  "*.+(js|json|ts)": [
    "prettier --write"
  ]
}
```

any files that are being processed in this commit, if they match the glob then
run the following script.

That improves the experience of everybody using the project so they don't
necessarily have to have prettier configured for their editor

6 npm-run-all

`npm install --save-dev npm-run-all`

add the parallel flag and provide it a list of scripts

it runs all the scripts at the same time and it's much faster.
