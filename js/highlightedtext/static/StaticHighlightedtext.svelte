<script lang="ts">
	import type { Gradio, SelectData } from "@gradio/utils";
	import HighlightedText from "./Highlightedtext.svelte";
	import { Block, BlockLabel, Empty } from "@gradio/atoms";
	import { TextHighlight } from "@gradio/icons";
	import { StatusTracker } from "@gradio/statustracker";
	import type { LoadingStatus } from "@gradio/statustracker";
	import { _ } from "svelte-i18n";

	export let elem_id = "";
	export let elem_classes: string[] = [];
	export let visible = true;
	export let value: [string, string | number][];
	let old_value: [string, string | number][];
	export let show_legend: boolean;
	export let color_map: Record<string, string> = {};
	export let label = $_("highlighted_text.highlighted_text");
	export let container = true;
	export let scale: number | null = null;
	export let min_width: number | undefined = undefined;
	export let selectable = false;
	export let gradio: Gradio<{
		select: SelectData;
		change: never;
	}>;

	$: if (!color_map && Object.keys(color_map).length) {
		color_map = color_map;
	}

	export let loading_status: LoadingStatus;

	$: {
		if (value !== old_value) {
			old_value = value;
			gradio.dispatch("change");
		}
	}
</script>

<Block
	test_id="highlighted-text"
	{visible}
	{elem_id}
	{elem_classes}
	padding={false}
	{container}
	{scale}
	{min_width}
>
	<StatusTracker {...loading_status} />
	{#if label}
		<BlockLabel
			Icon={TextHighlight}
			{label}
			float={false}
			disable={container === false}
		/>
	{/if}

	{#if value}
		<HighlightedText
			on:select={({ detail }) => gradio.dispatch("select", detail)}
			{selectable}
			{value}
			{show_legend}
			{color_map}
		/>
	{:else}
		<Empty>
			<TextHighlight />
		</Empty>
	{/if}
</Block>
