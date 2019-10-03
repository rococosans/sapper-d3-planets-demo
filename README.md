# Sapper D3 Demo

<img src="https://raw.githubusercontent.com/luscan/sapper-d3-planets-demo/master/static/solor-system.PNG" />

Quick tour of Sapper and D3. I experienced some weird caching things?

## Things I noticed

1. `d3.json('data/planets.json')` had to be changed to `d3.json('..//data/planets.json')` 

* I'm not sure what this was, but the former seemed to return old data.

In some cases, changing `d3.json('..//data/planets.json')` seemed to initialize a new file reference.

Which allowed me to revert to `d3.json('data/planets.json')` and have the fresh data be served.

In my earliest attempts, switching between the two returned the two different data sets. 

1. At some point I deleted the service worker from all three of it's locations
    
```
    /__sapper__/service-worker.js
    /src/node_modules/service-worker.js
    /src/service-worker.js
```

How come files seem to be duplicated, but their content is mostly unique?

It appears that `/src/node_modules/service-worker.js` generates data that gets used by `/src/service-worker.js` and (I'm guessing) that gets compiled into `/__sapper__/service-worker.js`

1. What's up with `src/node_modules`?

1. `/static/global.css` has been inconsistent for me

The initial styles seem to take effect through the link in the `template.html` head.

Any updates to `/static/global.css` are inconsistent.

My work around is importing `import '../../static/global.css'` into `/src/routes/_layout.svelte`

1. Rendering D3 elements

```js
// /node_modules/svelte/internal/index.mjs
// line 1291

function init(component, options, instance, create_fragment, not_equal, prop_names) {
    const parent_component = current_component;
    set_current_component(component);
    const props = options.props || {};
    const $$ = component.$$ = {
        fragment: null,
        ctx: null,
        // state
        props: prop_names,
        update: noop,
        not_equal,
        bound: blank_object(),
        // lifecycle
        on_mount: [],
        on_destroy: [],
        before_update: [],
        after_update: [],
        context: new Map(parent_component ? parent_component.$$.context : []),
        // everything else
        callbacks: blank_object(),
        dirty: null
    };
    let ready = false;
    $$.ctx = instance
        ? instance(component, props, (key, ret, value = ret) => {
            if ($$.ctx && not_equal($$.ctx[key], $$.ctx[key] = value)) {
                if ($$.bound[key])
                    $$.bound[key](value);
                if (ready)
                    make_dirty(component, key);
            }
            return ret;
        })
        : props;
    $$.update();
    ready = true;
    run_all($$.before_update);
    $$.fragment = create_fragment($$.ctx);
    if (options.target) {
        if (options.hydrate) {
            // eslint-disable-next-line @typescript-eslint/no-non-null-assertion
            
            
            //! $$.fragment.l(children(options.target)); causes rendered d3 elements to disappear
            //! I was able to get the elements rendered by commenting that line out
            //! Aside from an error in the console, I didn't witness any negative side effects
            //! Then... after a few hours of messing around, it started working without commenting this out
            //! bananaz
            
            // $$.fragment.l(children(options.target));
        }
        else {
            // eslint-disable-next-line @typescript-eslint/no-non-null-assertion
            $$.fragment.c();
        }
        if (options.intro)
            transition_in(component.$$.fragment);
        mount_component(component, options.target, options.anchor);
        flush();
    }
    set_current_component(parent_component);
}
```
