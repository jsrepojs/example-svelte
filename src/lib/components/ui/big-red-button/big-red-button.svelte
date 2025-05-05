<script lang="ts">
	import { cn } from '$lib/utils/utils.js';
	import type { HTMLButtonAttributes } from 'svelte/elements';

	let { class: className, children, ...rest }: HTMLButtonAttributes = $props();
</script>

<div class="group relative inline-block size-20 [--offset:0px] has-active:[--offset:4px]">
	<span class="absolute top-1 left-1 z-0 size-full scale-98 rounded-full bg-black transition-all"
	></span>
	<button
		{...rest}
		class={cn(
			'relative top-(--offset) left-(--offset) z-10 size-20 rounded-full border border-red-500 bg-red-500 text-white transition-all',
			className
		)}
	>
		{@render children?.()}
	</button>
</div>
