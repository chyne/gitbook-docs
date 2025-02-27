# 2. Packages

{% hint style="info" %}
This "Learning Frontity" guide is intended to be read in order so please start from the [first section](how-frontity-works.md) if you haven't done so already.
{% endhint %}

All the **Frontity** code lives inside packages. There is no "app code", like in other frameworks.

For those of you coming from WordPress, that's no surprise. All the WordPress code, _except WordPress itself obviously_, is contained in either the theme or the plugins.

If you come from React this may be less intuitive but it only means that the files of your project are always inside a **package**, either your theme or any other extension. 

## Project Folder Structure

This is the folder structure of a **Frontity** project:

```text
my-frontity-project/
|__ node_modules/
|__ package.json
|__ frontity.settings.js
|__ packages/
    |__ my-theme/
    |__ my-custom-extension/
```

* `frontity.settings.js` is explained in [the previous section](how-frontity-works.md).
* `package.json` is the file used for configuration in any Node project. There are many great articles about it like [this one](https://medium.com/beginners-guide-to-mobile-web-development/why-package-json-npm-basics-cab3e8cd150), [this one](https://flaviocopes.com/package-json/) or [this one](https://alligator.io/nodejs/package-json/).
* `node_modules` is the folder where all your dependencies are installed. For example, the core of Frontity \(`@frontity/core`\) is installed there. If you install external packages like `@frontity/tiny-router` or `@frontity/wp-source` they will be there as well.
* `packages` is the folder where your **local packages** live.

## Local Packages

As we have already explained, this is the place where you will add code and functionality to your sites.

When you do a `npx frontity create`, we install three packages for you:

* `@frontity/tiny-router` as an external package. It ends up in the `node_modules` folder.
* `@frontity/wp-source` as an external package. It ends up in the `node_modules` folder.
* `@frontity/mars-theme` as a **local package**. It ends up in the `packages` folder.

We do this because the most likely situation is that you want to modify the theme, but you don't want to modify the router or source packages. 

Once we move a package from `node_modules` to `packages` it becomes a **local package** and you can change it at will. If you use `git`, its code is also included in your project and you can commit any change. If you want to use that package in other projects or you want to contribute to the community you can publish it to npm using `npm publish`.

Be aware, you should not change anything inside `node_modules` because that folder is not committed to git and it is thrown away each time you move, reinstall or deploy your project.

Finally, it's worth noting that **Frontity** doesn't know which packages are local and which are external. The only difference between them is the way the are installed in the `package.json`. When they are local, they are referenced by folder and when they are external, by the version number:

{% code-tabs %}
{% code-tabs-item title="package.json" %}
```javascript
"dependencies": {
  "frontity": "^0.2.11", // installed in node_modules
  "@frontity/core": "^0.3.7", // installed in node_modules
  "@frontity/wp-source": "^0.1.7", // installed in node_modules
  "@frontity/tiny-router": "^0.3.4", // installed in node_modules
  "@frontity/mars-theme": "./packages/mars-theme" // installed in packages
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Package Folder Structure

Packages have their own `package.json` file. Its code is inside the `/src` folder.

```text
my-frontity-project/
|__ ...
|__ packages/
    |__ my-theme/
        |__ package.json
        |__ src/
            |__index.js
```

Let's review this in detail.

### Package.json

The `package.json` file is where you can write the info \(name, description, author, repository, version...\) of the package. It's just a regular `package.json` file, so nothing fancy here.

It also lists the npm `dependencies` of the package. The `"dependencies"` field gets automatically populated when you do run `npm install some-npm-package` in the package folder.

```text
cd packages/my-awesome-theme
npm install some-npm-package
```

{% code-tabs %}
{% code-tabs-item title="/packages/my-awesome-theme/package.json" %}
```javascript
{
  "name": "my-awesome-theme",
  "description": "An awesome theme for Frontity",
  ...
  "dependencies": {
    "some-npm-package": "^2.2.6"
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Packages need their own `package.json` file because:

* **Frontity** treats them like any other npm package found in `node_modules`.
* They can be published to npm independently  🚀
* They have their own name, version, authors and license.

### Modes and Entry Points

By default only one file is needed: `/src/index.js`.

In some cases you may want to use different code in your client than in your server. Then you can use two files, `/src/client.js` and `/src/server.js`.

In other cases, you may want to use different code for different modes. Then you can create a folder with the name of the mode. For example `/src/amp/index.js`.

Guess what? You can also use client and server files inside modes like this: `/src/amp/client.js` and `/src/amp/server.js` 🤗

## Publishing Local Packages

One thing we wanted to make sure in **Frontity** is that publishing packages was really easy. 

For that reason, **Frontity** packages don't need to be transpiled. They can be written in either JavaScript or TypeScript. _So, look ma, no build step!_

They can be published to npm directly from the `/packages` folder of your **Frontity** project:

```text
cd packages/my-awesome-theme
npm publish
```

 Now `my-theme` is available in npm! Any other **Frontity** user can install it using:

```text
npm install my-awesome-theme
```

And then including it in their `frontity.settings.js` file:

```javascript
{
  name: "my-site",
  packages: [
    "@frontity/my-awesome-theme",
    "@frontity/tiny-router",
    "@frontity/wp-source"
  ]
}
```

Yes, it is **that simple** :\)

## Package exports

Packages can export any of these elements:

* **Roots:** React components that will be included in the app.
* **Fills**: React components that will be included in the app, but injected after the roots.
* **State:** A javascript object containing all the state exposed by your package.
* **Actions:** A set of actions that your package need to work or exposes for other packages. 
* **Libraries:** Any additional tools that your package exposes for other packages.

For example, a simple theme could be like this:

{% code-tabs %}
{% code-tabs-item title="/packages/my-awesome-theme/src/index.js" %}
```javascript
import Theme from "./components";

export default {
  roots: {
    theme: Theme // <- This is the root component of your theme.
  },
  state: {
    theme: {
      menu: [
        ["Home", "/"],
        ["About", "/about"]
      ],
      isMenuOpen: false,
      featuredImage: {
        showOnList: true,
        showOnPost: false
      }
    }
  },
  actions: {
    theme: {
      openMenu: ({ state }) => {
        state.theme.isMenuOpen = true;
      },
      closeMenu: ({ state }) => {
        state.theme.isMenuOpen = false;
      }
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

By the way, it's probably good to point out here that in Frontity all packages are equal. Frontity doesn't which one represents a `theme` or which one represents a `source`. It treats all of them equally.

Let's explore the **Roots** and **Fills** in the next section.

