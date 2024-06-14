<script lang="ts">
	import Calendar from '$lib/components/Calendar.svelte';
	import { getLocalTimeZone, today } from '@internationalized/date';

	let value = today(getLocalTimeZone());
</script>

<Calendar
	{value}
	class="w-full"
/>
