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

	cg-container {
		position: absolute;
		width: 100%;
		height: 100%;
		display: block;
		top: 0;
	}

	cg-board {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		-webkit-user-select: none;
		-moz-user-select: none;
		-ms-user-select: none;
		user-select: none;
		line-height: 0;
		background-size: cover;
		/**
        * Blue board default
        */
		background-image: url('https://github.com/lichess-org/lila/blob/master/public/images/board/svg/blue.svg?raw=true');
	}

	.cg-wrap.manipulable cg-board {
		cursor: pointer;
	}

	cg-board square {
		position: absolute;
		top: 0;
		left: 0;
		width: 12.5%;
		height: 12.5%;
		pointer-events: none;
	}

	cg-board square.move-dest {
		pointer-events: auto;
	}

	cg-board square.last-move {
		will-change: transform;
	}

	.cg-wrap piece {
		position: absolute;
		top: 0;
		left: 0;
		width: 12.5%;
		height: 12.5%;
		background-size: cover;
		z-index: 2;
		will-change: transform;
		pointer-events: none;
	}

	cg-board piece.dragging {
		cursor: move;
		/* !important to override z-index from 3D piece inline style */
		z-index: 11 !important;
	}

	piece.anim {
		z-index: 8;
	}

	piece.fading {
		z-index: 1;
		opacity: 0.5;
	}

	.cg-wrap piece.ghost {
		opacity: 0.3;
	}

	.cg-wrap piece svg {
		overflow: hidden;
		position: relative;
		top: 0px;
		left: 0px;
		width: 100%;
		height: 100%;
		pointer-events: none;
		z-index: 2;
		opacity: 0.6;
	}

	.cg-wrap cg-auto-pieces,
	.cg-wrap .cg-shapes,
	.cg-wrap .cg-custom-svgs {
		overflow: visible;
		position: absolute;
		top: 0px;
		left: 0px;
		width: 100%;
		height: 100%;
		pointer-events: none;
	}

	.cg-wrap cg-auto-pieces {
		z-index: 2;
	}

	.cg-wrap cg-auto-pieces piece {
		opacity: 0.3;
	}

	.cg-wrap .cg-shapes {
		overflow: hidden;
		opacity: 0.6;
		z-index: 2;
	}

	.cg-wrap .cg-custom-svgs {
		/* over piece.anim = 8, but under piece.dragging = 11 */
		z-index: 9;
	}

	.cg-wrap .cg-custom-svgs svg {
		overflow: visible;
	}

	.cg-wrap coords {
		position: absolute;
		display: flex;
		pointer-events: none;
		opacity: 0.8;
		font-family: sans-serif;
		font-size: 9px;
	}

	.cg-wrap coords.ranks {
		left: 4px;
		top: -20px;
		flex-flow: column-reverse;
		height: 100%;
		width: 12px;
	}

	.cg-wrap coords.ranks.black {
		flex-flow: column;
	}

	.cg-wrap coords.ranks.left {
		left: -15px;
		align-items: flex-end;
	}

	.cg-wrap coords.files {
		bottom: -4px;
		left: 24px;
		flex-flow: row;
		width: 100%;
		height: 16px;
		text-transform: uppercase;
		text-align: center;
	}

	.cg-wrap coords.files.black {
		flex-flow: row-reverse;
	}

	.cg-wrap coords coord {
		flex: 1 1 auto;
	}

	.cg-wrap coords.ranks coord {
		transform: translateY(39%);
	}

	.cg-wrap coords.squares {
		bottom: 0;
		left: 0;
		text-transform: uppercase;
		text-align: right;
		flex-flow: column-reverse;
		height: 100%;
		width: 12.5%;
	}

	.cg-wrap coords.squares.black {
		flex-flow: column;
	}

	.cg-wrap coords.squares.left {
		text-align: left;
	}

	.cg-wrap coords.squares coord {
		padding: 6% 4%;
	}

	.cg-wrap coords.squares.rank2 {
		transform: translateX(100%);
	}

	.cg-wrap coords.squares.rank3 {
		transform: translateX(200%);
	}

	.cg-wrap coords.squares.rank4 {
		transform: translateX(300%);
	}

	.cg-wrap coords.squares.rank5 {
		transform: translateX(400%);
	}

	.cg-wrap coords.squares.rank6 {
		transform: translateX(500%);
	}

	.cg-wrap coords.squares.rank7 {
		transform: translateX(600%);
	}

	.cg-wrap coords.squares.rank8 {
		transform: translateX(700%);
	}

	/**
    * Piece svgs from the Lichess alpha piece set.
    */
	.cg-wrap piece.white.pawn {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wP.svg?raw=true');
	}
	.cg-wrap piece.black.pawn {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bP.svg?raw=true');
	}
	.cg-wrap piece.white.rook {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wR.svg?raw=true');
	}
	.cg-wrap piece.black.rook {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bR.svg?raw=true');
	}
	.cg-wrap piece.white.bishop {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wB.svg?raw=true');
	}
	.cg-wrap piece.black.bishop {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bB.svg?raw=true');
	}
	.cg-wrap piece.white.knight {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wN.svg?raw=true');
	}
	.cg-wrap piece.black.knight {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bN.svg?raw=true');
	}
	.cg-wrap piece.white.queen {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wQ.svg?raw=true');
	}
	.cg-wrap piece.black.queen {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bQ.svg?raw=true');
	}
	.cg-wrap piece.white.king {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/wK.svg?raw=true');
	}
	.cg-wrap piece.black.king {
		background-image: url('https://github.com/lichess-org/lila/blob/5b25013539f22843e3eedd41f8413fdccaff1b7c/public/piece/alpha/bK.svg?raw=true');
	}
</style>
