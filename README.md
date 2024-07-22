# Svelte Chessground UI
Yet another Svelte chessboard component. Svelte-Chessground-UI is a wrapper around [chessground](https://github.com/lichess-org/chessground), the open source chess UI used by Lichess. This package is compatible with projects that use **^4.0.0** versions of Svelte. 

## Usage
To install: \
`npm install svelte-chessground-ui`

To display the default chessboard:
```typescript
<script>
    import {Chessground} from 'svelte-chessground-ui';
</script>

<Chessground />
```

### Styling

Chessground can be completely restyled through CSS. The component imports default stylesheets. To apply your own, you have two options:

1. Override specific CSS commands with a scoped `:global` and `!important`:
```typescript
<div class="override_background">
    <Chessground />
</div>
<style>
    div.override_background :global(.cg-wrap cg-board) {
        background-image:url("/my-board.jpg") !important; /* replace chessboard image */
    }
</style>
```
2. Apply your own full chessground stylesheet instead of the defaults by setting the `class` prop and importing your own stylesheet. By changing the class name from the default, none of the default stylesheets will apply, not even the piece SVGs. Additionally, you can use the provided `ChessgroundUnstyled` component, which is completely unstyled. If using a `ChessgroundUnstyled` instance, you have several options.  

#### Styling `ChessgroundUnstyled`
You can link your component with `app.html` (assuming your app only has one chessboard to worry about).
```html
<head>
    <link rel="stylesheet" href="/globals.css"/>
</head>
```

You can import your own `.css` file, for example in a `+layout.svelte` or `+page.svelte`.  
```typescript
<script lang="ts">
    import {ChessgroundUnstyled} from 'svelte-chessground';
    import '$lib/my-chessboard.css';
</script>
<ChessgroundUnstyled class="my-chessboard" />
```
Or you can add your own styling within `<style>`. You will probably have to make the styles global like what was done in this package for the defaults in `<Chessground/>`.  

You can get an idea of some of these approaches in the [original custom styles examples](https://github.com/gtim/svelte-chessground-examples/blob/main/src/routes/style/%2Bpage.svelte).