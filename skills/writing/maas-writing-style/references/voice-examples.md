# Voice examples

Product, project, and client names are invented; the sentences model real
house style, organized by device.

## Standfirsts (the sentence under the title)

> Most of Fieldkit’s plugins are now built around a concept we call state
> composables. A simple idea that enables interacting with a component from
> anywhere inside the app.

> The story about why and how we ended up shipping a dozen [and counting]
> Nuxt modules in a single npm package.

> Fieldkit is a collection of ready-to-use plugins and composables for
> building modern web applications. We are pretty close to releasing a first
> stable version of it.

## Understatement

> We have tested the module in a variety of projects over the last two years
> and feel that our solution is pretty solid.

> Nuxt offers a Module Author Guide, a Starter Kit and the `@nuxt/kit`
> package, which made authoring the nested modules a fairly straightforward
> task.

## Dry humor

> When adding new plugins, we mostly copy paste some code, change a few
> variables and names and call it a day.

> While trying to come up with a fitting solution, we hit a road block – no
> straightforward way to install Vue plugins within a Nuxt module seemed to
> exist, which left us pondering.

## Opinionated but fair

> Pinia is the officially recommended state management library for Vue and
> Nuxt and we did consider making it a peer dependency for Fieldkit.
> However, we felt that relying on another big dependency besides VueUse,
> might unnecessarily bloat code bases and make it harder to maintain
> Fieldkit down the road. While Pinia definitely has its use cases, it might
> be a bit overkill for simply storing some boolean, string and number
> values. That doesn’t mean it should not be used for more complex state.

## Honest about detours and corrections

> To solve the problem, we initially implemented a solution that cloned and
> encapsulated state for each composable, keeping them in sync with a
> convoluted emitter system. While this worked at the time we later realised
> it was rather bloated and a bit of a performance bottleneck that did not
> scale well in more complex codebases.

> It should be mentioned that a maintainer recently pointed out that a
> solution for installing Vue plugins within Nuxt modules exists, and that
> authoring Nuxt modules should be the preferred solution in regards to DX.

## Grounding in real work

> We first encountered a similar problem when building an anniversary site
> for a fashion client. For the mobile version we needed to nest the video
> controls separate from the video, to position them above each section’s
> logo and info box.

## Cause before rule (mechanism-first explanation)

> What happens is that the variable is initialised once when the server
> process starts and then used and reused over and over again. Websites that
> see heavy traffic might leak state from one visitor to another, if two
> requests hit the server at the same time or in close proximity.

## Reader address and rhetorical pivot

> Fieldkit is our attempt to do just that, make our lives easier in the
> process, and hopefully by extension, yours too. So what is it?

> We aim to provide you with the possibility to bend each component to your
> will and use Fieldkit in creative ways beyond our own imagination.

## Credit and closing

> Finally I’d like to thank Jonas for always being available for a quick and
> insightful chat. Without him Fieldkit would definitely not be what it is
> today.

> By open-sourcing Fieldkit, we hope to give back to a community that has
> enabled us to earn a living for the past decade.

## Citing sources verbatim

> According to Vue’s documentation, a composable "is a function that
> leverages Vue’s Composition API to encapsulate and reuse stateful logic."
