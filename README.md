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
If using a `ChessgroundUnstyled` instance, link your component with a `styles.css` or add your own styling with `<script>`. You will probably have to make the styles global like what was done here.