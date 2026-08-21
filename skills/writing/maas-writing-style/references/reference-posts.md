# Reference posts

Three posts from maas.engineering, verbatim, for calibration. Read one before writing long-form prose; the cadence transfers better from a sample than from a rule. Sentences run 18 to 30 words with one subordinate clause, paragraphs hold one idea, headings are story beats, "we" carries the reasoning, and the only dash is a spaced en dash, about once per post.

## Vue Equipment: Module Inception

The story about why and how we ended up shipping 15 [and counting] Nuxt modules in a single npm package.

When we started working on Vue Equipment, we wanted to create a package that is compatible with any codebase written in Vue. 

Since most of our client-facing work over the past years has been implemented with Nuxt, we also wanted to provide first class support for the framework. Importing and using the various parts of Vue Equipment should feel as seamless as possible.

The goal was to provide an easily configurable Nuxt module, that would granularly import composables and plugins according to the given configuration and avoid bloating the bundle-size.

### Vue Equipment’s Architecture

Vue Equipment is internally split into three categories: composables, plugins and utils, with plugins containing most of the codebase.

Composables are standalone functions. According to Vue’s documentation, a composable “is a function that leverages Vue’s Composition API to encapsulate and reuse stateful logic.”

Utils contain various utility functions, TypeScript helpers and CSS – all of which can be imported directly as needed.

Plugins are usually made up from a combination of internal as well as exposed composables and components that depend on each other in some type of way. The majority of what’s included with Vue Equipment lives in here.

### The Problem

To create a seamless experience in Vue we opted to provide a Vue plugin for each of the plugins. The goal for all plugins was to auto import their components and expose all necessary composables, types and symbols.

We later set out to provide the same level of first class support for Nuxt by including a Nuxt module.

While trying to come up with a fitting solution, we hit a road block – no straightforward way to install Vue plugins within a Nuxt module seemed to exist, which left us pondering.

### The Solution

When we approached Daniel Roe, part of the Nuxt core team, with our problem, he proposed the idea to ship a Nuxt module that would auto install internal Nuxt modules, one for each included plugin. 

In return the internal Nuxt modules – much like the already authored Vue plugins – would auto install components and composables and make them globally available.

Nuxt offers a Module Author Guide, a Starter Kit and the `@nuxt/kit` package, which made authoring the nested modules a fairly straightforward task. 

When adding new plugins, we mostly copy paste some code, change a few variables and names and call it a day.

It should be mentioned, that Daniel Roe recently pointed out that a solution for installing Vue plugins within Nuxt modules exist. He also stated authoring Nuxt modules should be the preferred solution in regards to DX.

### In Conclusion

We have tested the Nuxt module in a variety of projects over the last two years and feel that our solution is pretty solid. Thanks to the module, Vue Equipment can be quickly implemented and configured with full type safety in any Nuxt project.

## Vue Equipment: Our Toolkit for Modern Frontends

Vue Equipment is a collection of ready-to-use plugins and composables for building modern web applications. We are pretty close to releasing a first stable version of it.

### The Current Landscape

Compared to a decade ago web development has become simultaneously increasingly complex as well as more accessible than ever. There are now plenty of Javascript Frameworks to choose from, most of which have been around for quite some time, with the majority of them having matured into stable offerings.

While our team has dabbled with React and Svelte in the past, we started favouring Vue early on, due to its simple syntax, familiar feel, the concept of single file components and its excellent documentation. When Nuxt was released in 2018, it not only validated our initial choice but also provided even greater confidence in the decision we had made.

### The Ecosystem

Vue and Nuxt undoubtedly excel in many areas, but we feel the ecosystem has always been lacking when compared to React, which has a larger user base, and comes with a lot more options to choose from when it comes to plugins, components and composables.

And so over the years, we have found ourselves facing a common problem: driven by the lack of options in the Vue ecosystem, we needed to implement solutions from the ground-up, over and over again. 

### Closing the Gap

While projects like VueUse and various Nuxt modules have started closing the gap over the past years, we feel that there is still some catching up to do.

Vue Equipment is our attempt to do just that, make our lives easier in the process, and hopefully by extension, yours too. So what is it?

Vue Equipment is a collection of ready-to-use essentials for building modern web applications. We have distilled what we’ve learned over several years from developing various custom solutions into an expansive package of opinionated yet flexible plugins and composables.

### The Details

The initial beta release of Vue Equipment will include a streaming-ready video player; a draggable drawer component; a scroll listener; a simple marquee; a draggable container; a cmd+k menu, and a cookie banner, just to name a few.

Vue Equipment can be installed as a standalone Vue plugin or a fully configurable Nuxt module. All components come basically unstyled, except for any essential CSS properties, which have been implemented as CSS variables and can easily be overwritten. All components and composables can be imported separately. In a Nuxt project, the module can quickly be configured to only auto-import what is needed.

The whole library is built around the principle of inversion of control. In order to produce stable, maintainable code, we’ve have slimmed down the plugins’ APIs as much as possible. We aim to provide you with the possibility to bend each component to your will and use Vue Equipment in creative ways beyond our own imagination.

### What’s Next?

In the last year, we’ve been busy dogfooding Vue Equipment for various projects, iterating, refactoring, and testing its mettle in real-world scenarios. We think we have finally arrived at a version that we are happy with. Our priority now is building out comprehensive documentation for the whole library, followed by a stable release candidate sometime in Q1 2025.

Vue Equipment was always intended to be a tool we use ourselves and we will continue to do so. This means you can expect on-going maintenance and further additions to the library for the foreseeable future. 

Even though we mostly work with Vue, we’ve kept an eye on the React ecosystem and have been inspired by projects such as Sonner; Vaul; ⌘K; Radix; shadcn/ui, and the likes. By open-sourcing Vue Equipment, we hope to give back to a community that has enabled us to earn a living for the past decade.

## Vue Equipment: Decoupling with State Composables

Most of Vue Equipment’s plugins are now built around a concept we call state composables. A simple idea that enables interacting with a component from anywhere inside the app.

When building websites we often face interdependency challenges. Menus need to listen to the state of drawers and vice versa; keypresses need to be ignored if certain conditions are met; videos need to be controlled from an entirely different component tree.

In the past, we faced similar challenges over and over again, often solving them with somewhat convoluted workarounds which involved custom event listeners, complex Pinia stores or hacky DOM manipulations.

While we didn’t build Vue Equipment to solve these problems exactly, they quickly became a central topic of the library’s internal architecture.

### Component Tree Shortcomings

Libraries like Vue, Svelte and React are all built around the concept of the component tree. Essentially, components can be grouped and nested within other components, much like small branches of a tree are grouped and nested under bigger branches of the same tree.

While this creates clear hierarchies and an easy to follow mental model for an app’s architecture, it creates its own set of challenges. 

A components state or functionality can only be accessed from within itself, its parent or its (nested) child components. All of a sudden, rendering a video as part of a section on a page, while wanting to mute and unmute the same exact video from a button nested inside the app’s menu bar appears to be impossible, simply because these two components existing on entirely different branches.

You’d have to set up globally shared state with something like Pinia, wrap the video player in a custom component to access its mute and unmute functionality, and listen to state changes for both the player and your Pinia store, all while making sure the state is constantly in sync. A lot of boilerplate and complexity for an essentially basic task, with quite a few opportunities to introduce bugs.

### Running into Problems

We first encountered a similar problem to the one described above, when building the 20th anniversary site for SSENSE. For the mobile version we needed to nest the video controls separate from the video to be able to position them above each section’s company logo and info box. We also wanted to manage mute/unmute state for all videos at the same time.

With the release of the Composition API, Vue 3 introduced Composables – a concept Vue Equipment heavily relies on to structure, divide and encapsulate complex logic throughout its’ plugins. 

To solve the problem, we initially implemented a solution that cloned and encapsulated state for each composable, keeping them in sync with a convoluted emitter system. While this worked at the time we later realised it was rather bloated and a bit of a performance bottleneck that did not scale well in more complex codebases.

### Finding a Solution

While searching for another approach, we realised that we had already used an appropriate pattern elsewhere – something we called state composables. 

State is registered as a reactive object inside a ref or shallowRef array. This array exists outside the useState function, which means it’s only initialised once when executing the code and therefore reused and shared anytime useState() is called. By making sure, the provided id can only be used once inside the state array, state and id are tightly coupled and unique.

### SSR Considerations

For applications levering server side rendering (SSR), the Vue docs state that “the above pattern can lead to issues due to the store being a singleton shared across multiple requests”. What happens is that the variable is initialised once when the server process starts and then used and reused over and over again. Websites that see heavy traffic might leak state from one visitor to another, if two requests hit the server at the same time or in close proximity. 

Additionally the pattern might lead to memory leaks in an SSR context, since the state can hold DOM references and is essentially being kept alive as long as the server is up and running. This would mean that elements, listeners, etc. might not be properly garbage collected, because the server thinks they might still be used later.

To combat these problems, Nuxt includes a useState composable. In a standalone Nuxt library, the solution would have been to simply replace the stateStore variable entirely.

Since Vue Equipment aims to be framework-agnostic, intended to be used with all sorts of Vue applications, we needed to come up with our own solution. Daniel Roe from the Nuxt team suggested we attach state directly to the Vue instance by utilising Vue’s `getCurrentInstance()`. Based on his idea we implemented a small utility function exposed through Vue Equipment’s (undocumented) utils.

Additionally we also made sure that the state composables clean up after themselves with Vue’s onScopeDispose(). Inside the composable we count how many times the state has been initialised and decrement it once the scope is disposed. If the count hits zero, we delete the state entirely from our `stateStore`.

To ring-fence and expose this internal state and to provide a user-friendly API to interact with, a second public composable is needed. To circumvent the risk of breaking the state, it is important to never expose state.id from within this public composable.

### Component

The component itself gets an id passed as prop, uses it with the composable to register itself and derives its internal state from the return.

This enables us to interact with the state and the component from anywhere by simply calling the public composable with the same id we passed as prop to the component.

### What about Pinia?

Pinia is the officially recommended state management library for Vue and Nuxt and we did consider making it a peer dependency for Vue Equipment. However, we felt that relying on another big dependency besides VueUse, might unnecessarily bloat code bases and make it harder to maintain Vue Equipment down the road. While Pinia definitely has its use cases, it might be a bit overkill for simply storing some boolean, string and number values. That doesn’t mean it should not be used for more complex state.

### Final Implementation

Almost all Vue Equipment plugins now follow this pattern and expose a composable to access the shared state from anywhere inside the app. While the code above is the bare minimum needed to illustrate the concept, a state composable can also house more intricate state.

### Auto Imports in Nuxt

All Vue Equipment plugins come with a Nuxt Module that automatically import and expose the useMagic... composables globally. Simply add @maas/vue-equipment/nuxt to your modules inside your nuxt.config and add the plugin to your module configuration.

### In Conclusion

State composables offer a solid solution to a wide-spread and sometimes complex problem. Aside from implementing them throughout Vue Equipment, we have also replaced all Pinia stores in our codebase with a similar pattern and often use the concept when implementing features of various complexity.

Finally I’d like to thank Daniel Roe for always being available for a quick and insightful chat. Without him Vue Equipment would definitely not be what it is today.
