# **How To Create Your Own React Component Library**

To get started clone the react-npm-package-boilerplate github repo.


```bash
git clone https://github.com/TDtarbo/react-npm-package-boilerplate.git
```

<br>

install dependencies

```bash
cd react-npm-package-boilerplate
npm i
```

<br>

# **Folder & File Breakdown**
```
/my-npm-package
│
│── /src                # Source code of the package
│   ├── index.js        # Entry point for the package
│   │       
│   ├── /components     # Contains reusable components
│       │  
│       ├── index.js    # Exports all components
│       ├── styles.css  # Global styles
│       │
│       ├── /ComponentOne
│       │   ├── index.js          # Barrel file for easy imports
│       │   ├── ComponentOne.jsx  # ComponentOne implementation
│       │
│       ├── /ComponentTwo
│           ├── index.js          # Barrel file for easy imports
│           ├── ComponentTwo.jsx  # ComponentTwo implementation
│
│── rollup.config.mjs   # Configuration file for Rollup.js
│── package.json        # Package metadata and dependencies
```

---

<br>

### 1. **``/src`` - Source Folder**
This folder contains all the source code for the package.

sample ```/src/index.js``` would look like:

```js
export * from "./components"
```

--- 

<br>

### 2. **``/components`` - Components Folder**
Each component has its own folder for modular development.

sample ```/src/components/index.js``` would look like:

```js
export { default as ComponentOne } from "./ComponentOne"
export { default as ComponentTwo } from "./ComponentTwo"
```

--- 

<br>

### 3. **```/components/{your_component_name}``` - Individual Components**

Here where you place your own **React Components**.

<br>

sample ```/src/components/MyComponent.jsx``` would look like:

```jsx
const sayHi = () => {
    window.alert("Hi👋");
};

const Button = () => {

    return <button onClick={sayHi}>Click Me</button>
};

export default Button;
```

<br>

sample ```/src/components/{your_component_name}/index.js``` would look like:

```js
export { default } from "./Button.jsx"
```

<br>
<br>

# **Configuration**

<br>

## 1. Rollup Configuration Breakdown 
````rollup.config.mjs````

This file configures Rollup to bundle the NPM package with support for **ES Modules (ESM)** and **CommonJS (CJS)**.


### Importing Required Plugins
```js
import nodeResolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";
import postcss from "rollup-plugin-postcss";
import { readFileSync } from "fs";
import path from "path";
import { fileURLToPath } from "url";
```

<br>

`@rollup/plugin-node-resolve` → Resolves imports of third-party dependencies.

`@rollup/plugin-commonjs` → Converts CommonJS modules to ES6.

`@rollup/plugin-babel` → Transpiles JSX/ES6+ to compatible JavaScript.

`rollup-plugin-postcss` → Processes CSS files.

---

<br>

### Getting Package Metadata
```js
const __dirname = path.dirname(fileURLToPath(import.meta.url));
const packageJson = JSON.parse(
  readFileSync(path.resolve(__dirname, "package.json"), "utf-8")
);
```

<br>

Reads `package.json` to dynamically set output filenames for CJS and ESM builds.

---

<br>

### Defining Rollup Configuration
```js
export default [
  {
    input: "./src/index.js", // Entry point of the package
    output: [
      {
        file: packageJson.main,   // CommonJS output (defined in package.json)
        format: "cjs",
        sourcemap: true,
      },
      {
        file: packageJson.module, // ES Module output (defined in package.json)
        format: "esm",
        sourcemap: true,
      },
    ],
```

<br>

**Bundles the package into two formats**:

CommonJS (`cjs`) → Used by Node.js.

ES Module (`esm`) → Used by modern JavaScript tools.

---

<br>

### Adding Plugins
```js
    plugins: [
      nodeResolve({ extensions: [".js", ".jsx"] }),
      commonjs(),
      babel({
        babelHelpers: "bundled",
        presets: ["@babel/preset-react"],
        extensions: [".js", ".jsx"],
      }),
      postcss()
    ],
```
<br>

1. `nodeResolve({ extensions: [".js", ".jsx"] })`  
   - Allows importing `.js` and `.jsx` files without specifying extensions.

2. `commonjs()`  
   - Converts CommonJS dependencies into ES modules.

3. `babel({ babelHelpers: "bundled", presets: ["@babel/preset-react"], extensions: [".js", ".jsx"] })`  
   - Uses Babel to transpile modern JavaScript and JSX.

4. `postcss()`  
   - Processes CSS files inside the package.

---

<br>

## 2. Package Configuration Breakdown 
````package.json````

Defines the metadata, dependencies, and configurations for a **React Component Library**.


### **Basic Information**
```json
{
  "name": "",
  "version": "1.0.0",
  "description": "React alert library",
  "author": "",
  "license": "MIT",
  "keywords": ["alert"]
}
```

<br>

`name`→ The package name (to be set before publishing).

`version`→ The package version (`1.0.0`).

`description`→ Short description of the package.

`author`→ Author name (to be set).

`license` → Open-source **MIT** license.

`keywords` → Helps in searching for the package on npm.

---

<br>

### **Scripts**

```json
"scripts": {
  "rollup": "rollup -c"
}
```

<br>

`rollup` → Runs Rollup using the config file (`rollup.config.mjs`) for bundling.

---

<br>

### **Dependencies**

```json
"devDependencies": {
  "@babel/preset-react": "^7.26.3",
  "@rollup/plugin-babel": "^6.0.4",
  "@rollup/plugin-commonjs": "^28.0.3",
  "@rollup/plugin-node-resolve": "^16.0.1",
  "rollup": "^4.35.0",
  "rollup-plugin-postcss": "^4.0.2"
}
```
<br>


`@babel/preset-react` → Enables React JSX transformation.

`@rollup/plugin-babel` → Transpiles modern JavaScript with Babel.

`@rollup/plugin-commonjs` → Converts CommonJS modules to ES6.

`@rollup/plugin-node-resolve` → Resolves third-party modules.

`rollup` → The bundler for this package.

`rollup-plugin-postcss` → Allows importing and processing CSS.

---

<br>

### **Output & Distribution**

```json
"main": "dist/cjs/index.js",
"module": "dist/esm/index.js",
"files": ["dist"]
```

<br>

`main` → Specifies the **CommonJS** build entry (`dist/cjs/index.js`).

`module` → Specifies the **ESM** build entry (`dist/esm/index.js`).

`files` → Ensures that only the `dist/` folder is included in the published package.

<br>

<br>

# Build Up The Bundle

Once the configuration is set up, you can build your package by running:

```sh
npm run rollup
```


### The command :

- **Runs Rollup** using the configuration in ``rollup.config.mjs``.

- **Bundles the package** into different formats (**CommonJS & ESM**).

- **Processes JSX and CSS** using Babel and PostCSS.

- **Outputs the final bundle** to the ``/dist`` directory.

### Default Bundle Output:

- **CommonJS build:** `dist/cjs/index.js`

- **ES Module build:** `dist/esm/index.js`

<br>

<br>

# Linking Your Package Locally

If you want to test your package locally before publishing, you can use npm link to create a symbolic link between your package and another project.

<br>

### 1. Run this command inside your package directory:

```sh
npm link
```

This will create a global symbolic link to your package.

---
<br>

### 2. Navigate to the project where you want to use the package and run:

```sh
npm link your-package-name
```

Now, you can import and use it like a normal installed dependency.

---
<br>

### 3. Unlink/Remove:

If you want to remove the linked package:

```sh
npm unlink your-package-name
```

Or to completely remove (Removes the global symbolic link that was created using npm link) the link:

```sh
npm unlink --global your-package-name
```

<br>
<br>

# Publish

Before publishing your package, you need to configure the `.npmrc` file. This file manages authentication, registry settings, and other configurations for npm.

<br>

### 1. Create ```.npmrc``` (if not already exists) in your home directory:


- Linux/macOS: ```~/.npmrc```

- Windows: ```C:\Users\YourUsername\.npmrc```

---

<br>

### 2. Configure ```.npmrc``` :

Example configuration :

```sh
registry=https://registry.npmjs.org/
@{org}:registry=https://npm.pkg.github.com/ 
//npm.pkg.github.com/:_authToken={token}
```

<br>

```registry=https://registry.npmjs.org/``` → Uses npm's public registry for publishing.

```@{org}:registry=https://npm.pkg.github.com/``` → Uses GitHub Packages for scoped packages.

```//npm.pkg.github.com/:_authToken={token}``` → Stores authentication token for GitHub publishing.

<br>

Replace `{token}` with your GitHub token [(personal access token (classic) from GitHub settings with write:packages enabled)](https://github.com/settings/tokens).

Replace `{org}` with your actual GitHub username or organization name. 

{org} should be the same in both `package.json` and `.npmrc`

Always use lowercase for the organization/username in .npmrc and in package.json.

```bash
{
  "name": "@janedoe/MyPackage"
}
```
```sh
@janedoe:registry=https://npm.pkg.github.com/
```
---

<br>

### 3. Publish

Once .npmrc is set up, you can publish your package by running:

```sh
npm publish
```

You should be able to see your package in [Github Packages Tab](https://github.com/)

