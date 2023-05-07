# This is a preline integration for svelte

These steps are described in this [blog post](https://dev.to/zhamdi/how-i-integrated-preline-to-work-in-sveltekit-16kj)

## Steps
[Check this post](https://dev.to/zhamdi/how-i-integrated-preline-to-work-in-sveltekit-16kj)

Configured tailwind
#### tailwind.config.js
```const config = {
content: [
"./src/**/*.{html,js,svelte,ts}",
"./node_modules/flowbite-svelte/**/*.{html,js,svelte,ts}",
"./node_modules/preline/dist/*.js",
],

	theme: {
		extend: {},
	},

	plugins: [
		require('flowbite/plugin'),
		require('preline/plugin')
	],
	darkMode: 'class',
};

module.exports = config;
```

## Adapting js files
Instead of having the js load at `document.on('load')` ex:
`document.addEventListener('load', window.HSAccordion.init());`

We make it load lazily when a component displays for its first time.

####Files were copied from preline
 ./core/component.js 
And copied [Accordion](https://github.com/htmlstreamofficial/preline/blob/main/src/components/hs-accordion/index.js) to src/components/hs-accordion/Accordion.js next to its Svelte components

#Skipping js loading when in SSR

## Accordion
When index.js was loaded, it attached components' js to window like foloows:
```
window.HSAccordion = new HSAccordion();
document.addEventListener('load', window.HSAccordion.init());
```
I moved that init code to the AccordionItem component, checking i'm in the browser before initializing like follows:

```
<script>
	import Accordion from './Accordion';

	if(typeof window !== 'undefined') {
		console.log(  "initializing accordion" )
		window.HSAccordion = new Accordion();
		window.HSAccordion.init();
	}
	export let name;
</script>
```
