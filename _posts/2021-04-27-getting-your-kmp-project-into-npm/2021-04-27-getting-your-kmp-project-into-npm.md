---
title: "Getting… your KMP project into npm"
date: 2021-03-29 00:00:00 +01:00
tags: [kmp, tutorial, npm]
image: "featured.jpeg"
---

<figure>
<img src="/getting-your-kmp-project-into-npm/featured.jpeg" alt="Getting… your KMP project into npm">
</figure>

# Getting… your KMP project into npm

I’ve recently had to configure my Kotlin Multiplatform shared module to work with npm. I’m more familiar with Gradle, so configuring everything to use **npm** had a couple of challenges.


## Using Nexus

I’m already using Nexus to store artifacts for the mobile builds. Nonetheless, those use maven, so in order to use **npm** you’ll need to create new repositories:
1. **npm-private** repository for your packages
2. **npm-proxy** to access the official registry
3. **npm-group** to access the above repositories under a single URL

The [blog.sonatype](https://blog.sonatype.com/using-nexus-3-as-your-repository-part-2-npm-packages) contains all the steps required to create these three repositories.


## Adding credentials

If your repository is behind credentials, you’ll need to define your username/password before publishing it.

### Creating the npm configuration file (.npmrc)

To do this, you can use the **.npmrc**. This is a configuration file for **npm** where you can define a set of variables. In this case, you’re going to define the registry along with the user authentication.

The easiest way to see where this config file is located is to run the command:

```shell
npm config ls -l
```

It will output a set of variables, one of them is the userconfig. Typically, it’s under:

```shell
Users/${whoami}/.npmrc
```

**Note:** If this file isn’t created, you can do it via the logic command:

```shell
npm login
```


### Configuring your credentials

Open the **.npmrc** file and add your repository and user configurations:
```shell
registry=https://<your_url>/repository/npm-group/
email=your@email.com
always-auth=true
_auth=<base64>
```

Looking at this configuration in more detail, you have:
- **registry**, corresponds, to the npm group repository that you’ve configured before
- **email**, your email
- **always-auth**, forces npm to always require authentication when accessing registry.
- **_auth**, authentication using base64.

In order to calculate the base64 value of your username and password, you can enter:
```shell
echo -n ‘<your_username>:<your_password’ | openssl base64
```


### Creating a test project

To test if everything is working, create a new package and publish it.
Start by creating an empty folder:

```shell
mkdir npm-test
```

Enter in it:

```shell
cd npm-test
```

Create a new **npm** package:

```shell
npm init -y
```

The flag `-y` is used to generate the **package.json** already pre-filled.

Add a **.js** file

```shell
touch index.js
```

Now that the project is created, update the **package.json** and add the `publishConfig` attribute properly configured:

```shell
{
 "name": "npm-test",
 "version": "1.0.0",
 "description": "",
 "main": "index.js",
 "publishConfig": {
   "registry": "https://<your_url>/repository/npm-private/"
 },
 "scripts": {
 "test": "echo \”Error: no test specified\” && exit 1"
 },
 "keywords": [],
 "author": "",
 "license": "ISC"
}
```

Remember that the `registry` URL corresponds to the private repo that you’ve created before.

Now that everything is configured, enter:

```shell
npm publish
```

You should see an output similar to this one:

```shell
npm notice
npm notice 📦 npm-test@1.0.0
npm notice === Tarball Contents ===
npm notice 0 index.js
npm notice 324B package.json
npm notice === Tarball Details ===
npm notice name: npm-app1
npm notice version: 1.0.0
npm notice package size: 333 B
npm notice unpacked size: 324 B
npm notice shasum: 1574f9b6d9cc94df4a5e3e56a2300b004dca229a
npm notice integrity: sha512-QW3/j4Ke3OyMr[…]gBgMfEvjrzmlw==
npm notice total files: 2
npm notice
```


## Publishing the KMP JS module

Now that you’ve confirmed that everything is working fine it’s time to publish the JS artifact that you’ve created from your shared module. So it can later be used from your web project.

Don’t forget that if you building for the web, you’ll need to add it as a platform. On **build.gradle.kts** add:

```gradle
js(LEGACY) {
    binaries.executable()
    browser()
}

sourceSets {
 val jsMain by getting {
   //add any dependency here
 }
}
```

Now that everything is set up, enter:

```shell
gradle build
```

To generate a build to all the platforms that you’ve defined.

After your build ends successfully, you should have a **build** folder with a **js** subfolder that contains:

- node_modules
- packages
- packages_imported
- package.json

Looking at all the files and folders, the **package.json** holds all the project metadata required by **npm**. Similar to what you’ve done in the previous section, you’ll need to add the `publishConfig` attribute to your (private) repository.

```shell
"publishConfig": {
  "registry": "https://<your_url>/repository/npm-private/"
}
```

Sometimes, you also need to define version.

An example of a correct **package.json** is:

```shell
{
  "name": "shared",
  "version": "1.0",
  "publishConfig": {
    "registry": "https://<your_url>/repository/npm-private/"
  },
  "workspaces": [
    "packages/shared"
  ],
  "resolutions": {},
  "devDependencies": {},
  "dependencies": {},
  "peerDependencies": {},
  "optionalDependencies": {},
  "bundledDependencies": []
}
```

Now that everything is set, just enter:

```shell
npm publish
```

And your package should be successfully deployed.

## Using your newly created package

Now that you’ve successfully published your package, you can use it in your project via:

```shell
npm install shared@1.0
```
That’s it!


Do you have a better approach? Something didn’t quite work with you?

Feel free to reply here or send me a message directly on [Twitter](https://twitter.com/cafonsomota) 🙂.
