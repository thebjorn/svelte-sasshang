# svelte-package hangs forever

Steps to reproduce:
```
sv create svelte-sasshang
cd svelte-sasshang
pnpm up

```
Add `src/lib/Foo.svelte` (note the `lang="scss"`)
```
<script>
    let { foo } = $props();
</script>

<div> {foo} </div>

<style lang="scss">
    div { color: red; }
</style>
```
run 
```
pnpm build
```
build gives a helpful error messag:
```
> Preprocessor dependency "sass-embedded" not found. Did you install it? Try `pnpm add -D sass-embedded`.
    at loadPreprocessorPath (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:48754:13)
    at loadSassPackage (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:48769:19)
    at process (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:49034:27)
    at compileCSSPreprocessors (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:48168:34)
    at compileCSS (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:48226:38)
    at async preprocessCSS (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/vite@6.0.3/node_modules/vite/dist/node/chunks/dep-yUJfKD1i.js:48451:10)
    at async style (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/@sveltejs+vite-plugin-svelte@5.0.1_svelte@5.9.0_vite@6.0.3/node_modules/@sveltejs/vite-plugin-svelte/src/preprocess.js:77:31)
    at async process_single_tag (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/svelte@5.9.0/node_modules/svelte/src/compiler/preprocess/index.js:283:21)
    at async Promise.all (index 0)
    at async replace_in_code (file:///C:/srv/work/svelte-sasshang/node_modules/.pnpm/svelte@5.9.0/node_modules/svelte/src/compiler/preprocess/replace_in_code.js:70:23)
 ELIFECYCLE  Command failed with exit code 1.
```
add `sass-embedded`:
```
pnpm add -D sass-embedded
```
run `pnpm build` again.
`svelte-package` hangs forever.
```
> svelte-sasshang@0.0.1 package
> svelte-kit sync && svelte-package && publint

If bundling, conditions should include development or production. If not bundling, conditions or NODE_ENV should include development or production. See https://www.npmjs.com/package/esm-env for tips on setting conditions in popular bundlers and runtimes.
src\lib -> dist
```

The problem is that you need to change svelte.config.js:
```diff
import adapter from '@sveltejs/adapter-auto';
- import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';
+ import { sveltePreprocess } from 'svelte-preprocess';

/** @type {import('@sveltejs/kit').Config} */
const config = {
	// Consult https://svelte.dev/docs/kit/integrations
	// for more information about preprocessors
- 	preprocess: vitePreprocess(),
+ 	preprocess: sveltePreprocess({scss: true}),
    ...
```
