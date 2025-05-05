# example-svelte

A demo to show you how you can deploy your first Svelte registry to jsrepo.com.

Within is an example of a big red button to help demonstrate how jsrepo works.

### Relevant Links

-   [jsrepo](https://github.com/jsrepojs/jsrepo)
-   [jsrepo.com](https://jsrepo.com)

### Prerequisites

-   Have an account on [jsrepo.com](https://jsrepo.com)

## Tutorial

In this tutorial we will cover how to build and publish a simple Svelte registry to [jsrepo.com](https://jsrepo.com).

### 1. Initialize a TypeScript project

Start by initializing a Svelte project (I will use pnpm for this)

```sh
pnpm dlx sv create
```

### 2. Create your components

Now lets create some components that we want to share. Add the following components in their respective locations.

`src/lib/components/ui/big-red-button.svelte`:

```svelte
<script lang="ts">
	import { cn } from '$lib/utils/utils.js';
	import type { HTMLButtonAttributes } from 'svelte/elements';

	let { class: className, children, ...rest }: HTMLButtonAttributes = $props();
</script>

<div class="group relative inline-block size-20 [--offset:0px] has-active:[--offset:4px]">
	<span class="absolute top-1 left-1 z-0 size-full scale-98 rounded-full bg-black transition-all"
	></span>
	<button
		{...rest}
		class={cn(
			'relative top-(--offset) left-(--offset) z-10 size-20 rounded-full border border-red-500 bg-red-500 text-white transition-all',
			className
		)}
	>
		{@render children?.()}
	</button>
</div>
```

`src/lib/utils/utils.ts`:

```ts
import { type ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
	return twMerge(clsx(inputs));
}
```

## 3. Prepare the registry for publish

Now that we have our components let's configure `jsrepo` to publish our registry to [jsrepo.com](https://jsrepo.com).

Start by running the init command:

```sh
pnpm dlx jsrepo init --registry
```

When asked _"Where are your blocks located?"_ answer `./src/lib` and then add another called `./src/lib/components` these directories are where our categories are. 

Answer yes to _"Configure to publish to jsrepo.com?"_ and then input the name of your registry.

> The name of the registry should be in the format of `@<scope>/<name>`. If you don't already have a scope you can claim one [here](https://jsrepo.com/account/scopes/new).

```plaintext
┌   jsrepo  v2.0.0
│
◇  Where are your blocks located?
│  ./src/lib
│
◇  Add another blocks directory?
│  Yes
│
◇  Where are your blocks located?
│  ./src/lib/components
│
◇  Add another blocks directory?
│  No
│
◇  Configure to publish to jsrepo.com?
│  Yes
│
◇  What's the name of your registry?
│  @example/svelte
│
◇  Added script to package.json
│
◇  Wrote config to `jsrepo-build-config.json`
│
◇  Would you like to install dependencies?
│  Yes
│
◆  Installed jsrepo
│
├ Next Steps ─────────────────────────────────────────────────────┐
│                                                                 │
│   1. Add categories to `./src/lib, ./src/lib/components`.       │
│   2. Run `pnpm run release:registry` to publish the registry.   │
│                                                                 │
├─────────────────────────────────────────────────────────────────┘
│
└  All done!
```

Next let's make sure we are only going to build the blocks that we want to include in our registry. We can do this with the keys in the `jsrepo-build-config.json`:

```jsonc
{
	"$schema": "https://unpkg.com/jsrepo@2.0.0/schemas/registry-config.json",
	"name": "@example/svelte",
	"version": "0.0.1",
	"readme": "README.md",
	"dirs": ["./src/lib", "./src/lib/components"],
	"doNotListBlocks": [],
	"doNotListCategories": [],
	"listBlocks": [],
	"listCategories": [],
	"excludeDeps": [],
	"includeBlocks": [],
	"includeCategories": ["ui", "utils"], // this way we only build ui and utils
	"excludeBlocks": [],
	"excludeCategories": [],
	"preview": false
}
```

Now your registry should be ready to publish!

## 4. Publish your registry

Now that your registry has been configured to publish to [jsrepo.com](https://jsrepo.com) let's authenticate to the jsrepo CLI.

```sh
jsrepo auth

# or

jsrepo auth --token <...>
```

Once you are authenticated let's do a dry run to make sure we got everything right:

```sh
jsrepo publish --dry-run
```

If all went well you should see:

```plaintext
◆  [jsrepo.com] Completed dry run!
```

See it? Great! Now let's do it for real!

```sh
jsrepo publish
```

And there you go you published your first registry to [jsrepo.com](https://jsrepo.com).

Now you can access your components through the `jsrepo` CLI.

```sh
jsrepo init @example/svelte

jsrepo add # list components

jsrepo add ui/big-red-button # add individual
```

and from the [jsrepo.com](https://jsrepo.com) page for your registry located at `https://jsrepo.com/@<scope>/<name>`.

## 5. Advanced usage

### Un-listing blocks

Now let's do a few things to improve our registry.

First of all when we run the `jsrepo add` command right now to list our components we see `big-red-button` and `utils`. Since `utils` is just an internal helper for `big-red-button` why don't we prevent it from coming up on this list.

We can do this with the `doNotListBlocks` key in our `jsrepo-build-config.json` file:

```jsonc
{
	// -- snip --
	"doNotListBlocks": ["utils"]
	// -- snip --
}
```

Now when we list our blocks only `big-red-button` will appear.

> Alternatively if we had more internal utils we could use `listBlocks` and only include `big-red-button` to prevent others from showing up here.

### Metadata

[jsrepo.com](https://jsrepo.com) uses metadata you provide in your `jsrepo-build-config.json` to display on your registries homepage.

```jsonc
{
	// -- snip --
	"meta": {
		"authors": ["Aidan Bleser"],
		"bugs": "https://github.com/jsrepojs/example-svelte",
		"description": "A demo to show you can you can deploy your first Svelte registry to jsrepo.com",
		"homepage": "https://github.com/jsrepojs/example-svelte",
		"repository": "https://github.com/jsrepojs/example-svelte",
		"tags": ["registry", "svelte", "example", "jsrepo"]
	},
	// -- snip --
}
```

### Changesets

Another thing you may want to setup if you are publishing a registry to [jsrepo.com](https://jsrepo.com) is [changesets](https://github.com/changesets/changesets).

Changesets are an awesome way to manage the versioning of your registry let's set them up here.

```sh
pnpm install @changesets/cli -D

pnpm changeset init
```

Now that you have changesets initialized let's make a small modification to the `.changeset/config.json` file:

```diff
{
  // -- snip --
+ "privatePackages": {
+   "tag": true,
+   "version": true
+ }
}
```

Let's also modify our `package.json` so that our release get's tagged when we publish a new version:

```diff
{
	// -- snip --
	"scripts": {
+		"release:registry": "jsrepo publish && changeset tag"
	}
	// -- snip --
}
```

And finally let's modify our `jsrepo-build-config.json` file to use the version from our `package.json`:

> You'll want to make sure that the version in your `package.json` matches the version in your `jsrepo-build-config.json` before continuing.

```diff
{
	// -- snip --
+	"version": "package",
	// -- snip --
}
```

Finally let's create a workflow so that we can publish a new version of our registry whenever there are changesets.

> If you are publishing from a workflow make sure to create a token [here](https://jsrepo.com/account/access-tokens/new) and add it with the name `JSREPO_TOKEN` under `Settings / Secrets and variables / Actions`

`.github/workflows/publish.yml`
```yaml
name: Publish

on:
    push:
        branches:
            - main

concurrency: ${{ github.workflow }}-${{ github.ref }}

jobs:
    release:
        name: Build & Publish Release
        runs-on: ubuntu-latest

        steps:
            - uses: actions/checkout@v4
            - uses: pnpm/action-setup@v4
            - uses: actions/setup-node@v4
              with:
                  node-version: "20"
                  cache: pnpm

            - name: Install dependencies
              run: pnpm install

            - name: Create Release Pull Request or Publish
              id: changesets
              uses: changesets/action@v1
              with:
                  commit: "chore(release): version package"
                  title: "chore(release): version package"
                  publish: pnpm release:registry
              env:
                  JSREPO_TOKEN: ${{ secrets.JSREPO_TOKEN }} # !! DON'T FORGET THIS !!
                  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
                  NODE_ENV: production
```

Now lets create a changeset:
```sh
pnpm changeset
```

Now once we commit our changeset to main changesets will automatically open a PR and version our package for us to create a release.
