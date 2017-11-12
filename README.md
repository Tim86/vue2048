# Vue 2048

> Playing with vue while making a simple game.

Based on the vue-cli simple webpack boilerplate. The currently latest playable version can be tested on 2048.scharinger.se. Development is not complete.

### Features

Dynamic colors using calculated hue and saturation. Could be used to let the user set a color scheme.

New game can be started with `[alt gr]` + `[n]`.


## Dependencies

https://github.com/vuejs/vue-touch


## TODO

Begin partly from start with the **tile approach** which keeps track of all tiles in a simple array, where each track has cords and value. The old **grid approach** makes it hard to make the game behave like the original.

```javascript
tiles = [{x:0,y:0,v:2},{x:1,y:0,v:2} ... ]
```
1. Use vue watchers to make transition effects on value changes.
1. Keep track of swype movements, keep in state.
    1. Use that info to add slide effects on tiles that are "updated". 
    1. Change slide method names to be more logical, true to real life.
1. Finish the movement function, each dir is diffrent, use the best. 
1. Make the game over state more visible to the user.

[Game rules](./src/readme.md) needed to be implemented better.

## Caveats

Using `overflow-y:hidden` to prevent the chrome android reload.

## Development
### Dev mode
Makes some info visible in the UI that was used to help debugging the app.
### Turn it on
1. get the chrome vue plugin
1. use component view
1. activate the app component
1. run `$vm0.$data.debug = true` in the console

## Deploy to surge.sh

To deploy to my site.
``` bash
npm run deploy
```
same as:
``` bash
npm run build
surge C:\Users\Tim\Documents\Repos\2048\ 2048.scharinger.se
```

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
