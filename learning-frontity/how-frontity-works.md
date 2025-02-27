# 1. Settings

The first thing you should do when you start a new **Frontity** project is to create a `frontity.settings.js`file. Let's see each concept you need to understand in order to use it properly.

## Site

A **site** is a set of packages and settings. For example, this is a site:

{% code-tabs %}
{% code-tabs-item title="frontity.settings.js" %}
```javascript
export default [
  {
    name: "my-site", // The name of your site.
    state: {
      frontity: {
        url: "https://my-site.com", // Some settings.
      }
    },
    packages: [
      "@frontity/mars-theme",  // And the packages of that site.
      "@frontity/tiny-router",
      "@frontity/wp-source"
    ]
  }
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Multiple Sites

One **Frontity** installation can serve content for multiple sites. This is useful if you have severals blogs and want to manage all the same with the same installation. Both the packages and settings of each site are independent. 

To distinguish between different sites, you must use a `match` setting. Each time a new request is received by **Frontity**, it tests the url against the `match` field to know which site it should load:

```javascript
export default [
  {
    name: "site-1",
    match: ["https://site-1.com"],
    packages: [...]
  },
  {
    name: "site-2",
    match: ["https://site-2.com"],
    packages: [...]
  }
]
```

For example, if the url is `https://site-1.com/my-post` the `"site-1"` settings are loaded and if the url is `https://site-2.com/category/some-cat` the `"site-2"` settings are loaded.

In development, you can access a specific site using the `?name=` query:

```text
https://localhost:3000/?name=site-2
```

More information about how to configure your `frontity.settings.js` file can be found here: [Settings API Reference](https://docs.frontity.org/api-reference-1/file-settings).

## Modes

The next concept we need to explore is the **mode**.

**Frontity** not only supports several sites, but it also supports different types of render. 

The default mode is called `default` and at this time, the only additional render supported is `amp`. But **Frontity** is prepared to easily support more in the future if necessary. 

If you want to use a **mode**, you just need to add a new site to your `frontity.settings.js` file and specify a mode. For example:

```javascript
export default [
  {
    name: "my-site",
    mode: "default", // <- this is optional, "default" is the default :)
    packages: [...]
  },
  {
    name: "my-site-amp",
    mode: "amp", // <- add the new mode here.
    match: ["\/amp$"],
    packages: [...]
  }
]
```

You can use  `match` to tell **Frontity** which set of urls should be loaded with this new mode. 

In this example, we are telling **Frontity** to use the `my-site-amp` settings each time it finds a url that ends with `/amp`. If the url is `https://site.com/my-post` the `"my-site"` settings are loaded and if the url is `https://site.com/my-post/amp` the `"my-site-amp"` settings are loaded.

For now let's leave it here. We'll talk later about how you can use this mode in your **Frontity** code to render different things.

## Packages

You can specify a different set of **packages** for each site. They can be either strings or objects:

{% code-tabs %}
{% code-tabs-item title="frontity.settings.js" %}
```javascript
export default [
  {
    packages: [
      "@frontity/mars-theme",
      "@frontity/tiny-router",
      {
        name: "@frontity/wp-source",
        active: true,
        state: {  // Some settings for this package.
          source: {
            api: "https://my-site.com/wp-json"
          }
        }
      }
    ]
  }
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

As you can see, they have an `active` prop. That means you can deactivate a package without having to delete it from your settings file.

In **Frontity**, all the code is contained in packages. In a sense it is more similar to WordPress, where all the code is contained in your theme and plugins, than to other javascript frameworks. This is obviously on purpose but we will explain the reasons later when we talk about packages and namespaces :\)

That's pretty much it about packages for now.

## State

The last thing you need to know to work with your `frontity.settings.js` file are the settings.

As you have probably already noticed, you don't use `settings`, we use `state`. That's on purpose as well. 

If you come from a WordPress background, you can think of **Frontity** `state`as the database of your application. And if you come from a React background, well... it's the `state` that you usually find in Redux or Mobx. That `state` is accessible by your packages at run time. 

You have the opportunity to modify the initial `state` of your site in the `frontity.settings.js` file. You can do it in a general `state` object or inside packages.

{% code-tabs %}
{% code-tabs-item title="frontity.settings.js" %}
```javascript
export default [
  {
    name: "my-site",
    state: {
      frontity: {
        url: "https://my-site.com", // Some settings of the site.
      }
    },
    packages: [
      "@frontity/mars-theme",
      "@frontity/tiny-router",
      {
        name: "@frontity/wp-source",
        state: {  // Some settings for this package.
          source: {
            api: "https://my-site.com/wp-json"
          }
        }
      }
    ]
  }
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In **Frontity**, the `state` is  organized in what we call **namespaces**. It means that each package uses a specific part of the state: a **namespace**. For example, our `wp-source` package uses of the `source` namespace to store its settings. 

And our `tiny-router` package uses the `router` namespace:

{% code-tabs %}
{% code-tabs-item title="frontity.settings.js" %}
```javascript
packages: [
  {
    name: "@frontity/tiny-router",
    state: {
      router: {
        autoFetch: true
      }
    }
  }
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

For now, let's leave it here. We will explain why the namespaces are important later.

The important takeaway here is: _In the settings file you have the opportunity to change the `state` of **Frontity**. Most of the time you will use this to configure the settings of each package._

