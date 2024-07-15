<script lang="ts">
	import { Chessground } from 'chessground';
	import type { Api } from 'chessground/api';
	import type { Config } from 'chessground/config';
	import type { Key, PiecesDiff, Piece, Drop, NumberPair, MouchEvent } from 'chessground/types';
    import type { DrawShape } from 'chessground/draw';
	import { onMount } from 'svelte';

	export let className: string = 'cg-default-style';

	// Props with direct export for reactivity.
	export let fen: string | undefined = undefined;
	export let orientation: 'white' | 'black' | undefined = undefined;
	export let turnColor: 'white' | 'black' | undefined = undefined;
	export let check: boolean | undefined = undefined;
	export let lastMove: Key[] | undefined = undefined;
	export let selected: Key | undefined = undefined;
	export let coordinates: boolean | undefined = true;
	export let autoCastle: boolean | undefined = true;
	export let viewOnly: boolean | undefined = false;
	export let disableContextMenu: boolean | undefined = true;
	export let addPieceZIndex: boolean | undefined = undefined;
	export let addDimensionsCssVarsTo: HTMLElement | undefined = undefined;
	export let blockTouchScroll: boolean | undefined = true;
    export let config = {};

	// Container for Chessground instance.
	let container: HTMLDivElement;

	// Chessground API instance.
	let chessground: Api;

	/**
	 * Initializes the Chessground instance when the component mounts and
	 * configures the chessboard based on provided properties.
	 */
	onMount(() => {
		const config: Config = {
			fen,
			orientation,
			turnColor,
			check,
			lastMove,
			selected,
			coordinates,
			autoCastle,
			viewOnly,
			disableContextMenu,
			addPieceZIndex,
			addDimensionsCssVarsTo,
			blockTouchScroll
		};

		Object.keys(config).forEach((key) => {
			if (config[key as keyof Config] === undefined) {
				delete config[key as keyof Config];
			}
		});

		chessground = Chessground(container, config);
	});

	/**
	 * Updates the configuration of the Chessground instance dynamically.
	 *
	 * @param {Partial<Config>} newConfig - New configuration settings to apply.
	 */
	function setConfig(newConfig: Partial<Config>): void {
		if (chessground) {
			chessground.set(newConfig);
		}
	}

    $: if (fen) setConfig({ fen });
    $: if (orientation) setConfig({ orientation })
    $: if (turnColor) setConfig({ turnColor })
    $: if (check) setConfig({ check })
    $: if (lastMove) setConfig({ lastMove })
    $: if (selected) setConfig({ selected })
    $: if (coordinates) setConfig({ coordinates })
    $: if (autoCastle) setConfig({ autoCastle })
    $: if (viewOnly) setConfig({ viewOnly })
    $: if (disableContextMenu) setConfig({ disableContextMenu })
    $: if (addPieceZIndex) setConfig({ addPieceZIndex })
    $: if (addDimensionsCssVarsTo) setConfig({ addDimensionsCssVarsTo })
    $: if (blockTouchScroll) setConfig({ blockTouchScroll })
    $: if (config) setConfig( config )

    /**
     * Helper methods that make use of native Chessground functions.
    */

    export function set(config: Config) {
        chessground.set(config);
    }

    export function getState() {
        return chessground.state;
    }

    export function getFen() {
        return chessground.getFen();
    }

    export function toggleOrientation() {
        return chessground.toggleOrientation();
    }

    export function move( orig: Key, dest: Key) {
        return chessground.move(orig, dest);
    }

    export function setPieces( pieces: PiecesDiff ) {
        return chessground.setPieces(pieces);
    }

    export function selectSquare(key: Key, force?: boolean) {
        return chessground.selectSquare(key, force);
    }

    export function newPiece(piece: Piece, key: Key) {
        return chessground.newPiece(piece, key);
    }

    export function playPremove() {
        return chessground.playPremove();
    }

    export function cancelPremove() {
        chessground.cancelPremove();
    }

    export function playPredrop(validate: (drop: Drop) => boolean): boolean {
        return chessground.playPredrop(validate)
    }

    export function cancelPredrop() {
        chessground.cancelPredrop();
    }

    export function cancelMove() {
        chessground.cancelMove();
    }

    export function stop() {
        chessground.stop();
    }

    export function explode(keys: Key[]) {
        chessground.explode(keys);
    }

    export function setShapes(shapes: DrawShape[]) {
        chessground.setShapes(shapes);
    }

    export function setAutoShapes(shapes: DrawShape[]) {
        chessground.setAutoShapes(shapes);
    }

    export function getKeyAtDomPos(pos: NumberPair) {
        return chessground.getKeyAtDomPos(pos)
    }

    export function redrawAll() {
        return chessground.redrawAll();
    }

    // type MouchEvent = Event & Partial<MouseEvent & TouchEvent> lol
    export function dragNewPiece(piece: Piece, event: MouchEvent, force?: boolean) {
        chessground.dragNewPiece(piece, event, force);
    }

    export function destroy() {
        return chessground.destroy();
    }
</script>

<div class="cg-wrap {className}" bind:this={container}></div>
