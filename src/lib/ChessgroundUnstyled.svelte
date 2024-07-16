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
	$: if (orientation) setConfig({ orientation });
	$: if (turnColor) setConfig({ turnColor });
	$: if (check) setConfig({ check });
	$: if (lastMove) setConfig({ lastMove });
	$: if (selected) setConfig({ selected });
	$: if (coordinates) setConfig({ coordinates });
	$: if (autoCastle) setConfig({ autoCastle });
	$: if (viewOnly) setConfig({ viewOnly });
	$: if (disableContextMenu) setConfig({ disableContextMenu });
	$: if (addPieceZIndex) setConfig({ addPieceZIndex });
	$: if (addDimensionsCssVarsTo) setConfig({ addDimensionsCssVarsTo });
	$: if (blockTouchScroll) setConfig({ blockTouchScroll });
	$: if (config) setConfig(config);

	/**
	 * Helper methods that make use of native Chessground functions.
	 */

	// Reconfigure the instance. Accepts all config options except for
	// viewOnly & drawable.visible. Board will be animated accordingly
	// if animations are available.
	export function set(config: Config) {
		chessground.set(config);
	}

	// Read chessground state. Writing is not advised.
	export function getState() {
		return chessground.state;
	}

	// Get the position as an FEN string (only contains pieces, no flags).
	export function getFen() {
		return chessground.getFen();
	}

	// Change the view angle.
	export function toggleOrientation() {
		return chessground.toggleOrientation();
	}

	// Perform move programatically.
	export function move(orig: Key, dest: Key) {
		return chessground.move(orig, dest);
	}

	// Add and/or remove arbitrary pieces on the board.
	export function setPieces(pieces: PiecesDiff) {
		return chessground.setPieces(pieces);
	}

	// Click a square programatically.
	export function selectSquare(key: Key, force?: boolean) {
		return chessground.selectSquare(key, force);
	}

	// Put a new piece on the board.
	export function newPiece(piece: Piece, key: Key) {
		return chessground.newPiece(piece, key);
	}

	// Play the current premove, if any. Returns true if premove was played.
	export function playPremove(): boolean {
		return chessground.playPremove();
	}

	// Cancel the current premove, if any.
	export function cancelPremove() {
		chessground.cancelPremove();
	}

	// Play the current predrop, if any. Returns true if predrop was played.
	export function playPredrop(validate: (drop: Drop) => boolean): boolean {
		return chessground.playPredrop(validate);
	}

	// Cancel the current predrop, if any.
	export function cancelPredrop() {
		chessground.cancelPredrop();
	}

	// Cancel the current move being made.
	export function cancelMove() {
		chessground.cancelMove();
	}

	// Cancel current move and prevent further ones.
	export function stop() {
		chessground.stop();
	}

	// Made squares explore (atomic chess)
	export function explode(keys: Key[]) {
		chessground.explode(keys);
	}

	// Programmatically draw user shapes.
	export function setShapes(shapes: DrawShape[]) {
		chessground.setShapes(shapes);
	}

	// Programmatically draw auto shapes.
	export function setAutoShapes(shapes: DrawShape[]) {
		chessground.setAutoShapes(shapes);
	}

	// Square name at DOM position (like "e4")
	export function getKeyAtDomPos(pos: NumberPair): Key | undefined {
		return chessground.getKeyAtDomPos(pos);
	}

	// Only useful when CSS changes the board width/height ratio (for 3D)
	export function redrawAll() {
		return chessground.redrawAll();
	}

	// For crazyhouse and board editors.
	// type MouchEvent = Event & Partial<MouseEvent & TouchEvent>
	export function dragNewPiece(piece: Piece, event: MouchEvent, force?: boolean) {
		chessground.dragNewPiece(piece, event, force);
	}

	// Unbinds all events.
	// Important for document-wide events like scroll and mousemove.
	export function destroy() {
		return chessground.destroy();
	}
</script>

<div class="cg-wrap {className}" bind:this={container}></div>

<style>
	.cg-wrap {
		box-sizing: content-box;
		position: relative;
		display: block;
	}
</style>