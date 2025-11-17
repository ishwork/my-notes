# Code Quality Setup: ESLint, Prettier, and Husky

This guide provides comprehensive steps to configure ESLint, Prettier, and Husky in a single or multi-directory projects.

## 1. VS Code Settings and Extensions

To ensure a consistent development environment, create a `.vscode/settings.json` file in your project root with the following content:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "prettier.requireConfig": true
}
```

### Recommended VS Code Extensions

Install these extensions for best results:

- [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

```

## Install Dependencies

At the project root, install the required packages:

```sh
npm install --save-dev eslint @eslint/js eslint-config-prettier prettier husky lint-staged typescript typescript-eslint eslint-plugin-react-hooks eslint-plugin-react-refresh globals
```

- For TypeScript: `typescript`, `typescript-eslint`
- For React: `eslint-plugin-react-hooks`, `eslint-plugin-react-refresh`
- For monorepo: `globals`, `eslint-config-prettier`

---

## 2. Configure ESLint

Create `eslint.config.js` at the project root:

---

## Guide: Configuring ESLint for Single or Multi-Directory (Monorepo) Projects

---

1. **Monorepo (Two Directories at Root, e.g., `frontend` and `backend`)**
   - Use the provided `files` globs as-is:
     - `files: ['frontend/**/*.{ts,tsx}']` // for frontend React/TS
     - `files: ['backend/**/*.js']` // for backend Node.js
   - This allows different rules for each subproject.

2. **Single Directory Project (e.g., only `src` at root)**
   - Change the `files` globs to match the structure:
     - `files: ['src/**/*.{js,ts,tsx}']`
   - Remove or adjust the backend/frontend-specific overrides as needed.

3. **General**
   - Always keep the ignore patterns and Prettier config at the end.
   - Adjust rules/plugins as needed for stack.

---

```js
import { defineConfig } from 'eslint/config';
import js from '@eslint/js';
import globals from 'globals';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import tseslint from 'typescript-eslint';
import prettierConfig from 'eslint-config-prettier';

export default defineConfig([
  { ignores: ['**/dist', '**/node_modules', '**/build', 'eslint.config.js'] },
  js.configs.recommended,
  ...tseslint.configs.recommended,
  {
    files: ['frontend/**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 'latest',
      globals: globals.browser,
      parserOptions: {
        projectService: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      'react-refresh/only-export-components': [
        'warn',
        { allowConstantExport: true },
      ],
      'no-console': ['error', { allow: ['error', 'warn', 'info'] }],
    },
  },
  {
    files: ['backend/**/*.js'],
    languageOptions: {
      ecmaVersion: 'latest',
      sourceType: 'module',
      globals: globals.node,
    },
    rules: {
      'no-console': 'warn',
      'no-unused-vars': 'warn',
    },
  },
  prettierConfig,
]);
```

---

## 3. Configure Prettier

Create `.prettierrc` at the project root (example):

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100
}
```

Create `.prettierignore` to exclude files/folders:

```
node_modules
build
dist
```

---

## 4. Setup Husky and lint-staged

### Install Husky

Install Husky as a dev dependency if not already installed:

```sh
npm install --save-dev husky
```

Add to `package.json` scripts:

```json
"scripts": {
  "prepare": "husky"
}
```

### Add Pre-commit Hook

```sh
# Run this command only once to create the pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"
```

### Configure lint-staged in `package.json`:

```json
"lint-staged": {
  "*{js,jsx,ts,tsx}": [
    "prettier --write",
    "eslint --fix"
  ],
  "*.{json,css,md}": [
    "prettier --write"
  ]
}
```

---

## 5. Usage

- **Lint:** `npx eslint .`
- **Format:** `npx prettier --write .`
- **Pre-commit:** Staged files are auto-checked and fixed by Husky/lint-staged.

---

## 6. Troubleshooting

- If a commit is blocked, check the error output for ESLint/Prettier issues.
- For unused variables if needed in some places, use an inline comment in that file:
  ```js
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  app.use((err, _req, res, _next) => { ... });
  ```
- Adjust rules in `eslint.config.js` as needed for the workflow.

---

## 7. References

- [ESLint Flat Config Docs](https://eslint.org/docs/latest/use/configure/configuration-files-new)
- [Prettier Docs](https://prettier.io/docs/en/index.html)
- [Husky Docs](https://typicode.github.io/husky/#/)
- [lint-staged Docs](https://github.com/okonet/lint-staged)
