<script lang="ts">
	import { Calendar as CalendarPrimitive } from 'bits-ui';
	import {
		DateFormatter,
		getLocalTimeZone,
		today,
		CalendarDateTime
	} from '@internationalized/date';
	import * as Calendar from '$lib/components/ui/calendar/index.js';
	import * as Select from '$lib/components/ui/select/index.js';
	import { cn } from '$lib/utils.js';
	import { Button } from '$lib/components/ui/button';

	type $$Props = CalendarPrimitive.Props;
	type $$Events = CalendarPrimitive.Events;

	export let value: $$Props['value'] = undefined;
	let placeholder = today(getLocalTimeZone());
	export let weekdayFormat: $$Props['weekdayFormat'] = 'short';

	const monthOptions = [
		'Janeiro',
		'Fevereiro',
		'Março',
		'Abril',
		'Maio',
		'Junho',
		'Julho',
		'Agosto',
		'Setembro',
		'Outubro',
		'Novembro',
		'Dezembro'
	].map((month, i) => ({ value: i + 1, label: month }));

	const monthFmt = new DateFormatter('pt-BR', {
		month: 'long'
	});

	$: defaultYear = placeholder ? placeholder.year : undefined;

	$: defaultMonth = placeholder
		? {
				value: placeholder.month,
				label: monthFmt.format(placeholder.toDate(getLocalTimeZone()))
			}
		: undefined;

	$: console.log(placeholder, defaultYear);
</script>

<CalendarPrimitive.Root
	{weekdayFormat}
	class="h-max w-max p-3"
	{...$$restProps}
	on:keydown
	let:months
	let:weekdays
	bind:value
	bind:placeholder
	fixedWeeks
	locale="pt-BR"
>
	<Calendar.Header>
		<Calendar.Heading class="flex w-full items-center justify-between gap-8">
			<Select.Root
				selected={defaultMonth}
				items={monthOptions}
				onSelectedChange={(v) => {
					if (!v || !placeholder) return;
					if (v.value === placeholder?.month) return;
					placeholder = placeholder.set({ month: v.value });
				}}
			>
				<Select.Trigger
					aria-label="Select month"
					class="min-w-36 capitalize"
				>
					<Select.Value placeholder="Select month" />
				</Select.Trigger>
				<Select.Content class="max-h-[200px] overflow-y-auto">
					{#each monthOptions as { value, label }}
						<Select.Item
							{value}
							{label}
						>
							{label}
						</Select.Item>
					{/each}
				</Select.Content>
			</Select.Root>
			<div class="border-input flex items-center rounded-md border pl-2">
				<div class="flex w-full items-center gap-4">
					{placeholder.year}
					<div class="flex items-center">
						<Button
							size="icon"
							variant="ghost"
							on:click={() => {
								if (!placeholder) return;
								placeholder = placeholder.add({ years: 1 });
							}}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"><path d="M5 12h14" /><path d="M12 5v14" /></svg
							>
						</Button>
						<Button
							size="icon"
							variant="ghost"
							on:click={() => {
								if (!placeholder) return;
								placeholder = placeholder.subtract({ years: 1 });
							}}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"><path d="M5 12h14" /></svg
							>
						</Button>
					</div>
				</div>
			</div>
		</Calendar.Heading>
	</Calendar.Header>
	<Calendar.Months>
		{#each months as month}
			<Calendar.Grid>
				<Calendar.GridHead>
					<Calendar.GridRow class="flex gap-6">
						{#each weekdays as weekday}
							<Calendar.HeadCell class="capitalize">
								{weekday.slice(0, 3)}
							</Calendar.HeadCell>
						{/each}
					</Calendar.GridRow>
				</Calendar.GridHead>
				<Calendar.GridBody>
					{#each month.weeks as weekDates}
						<Calendar.GridRow class="mt-2 gap-6">
							{#each weekDates as date}
								<Calendar.Cell {date}>
									<Calendar.Day
										{date}
										month={month.value}
									/>
								</Calendar.Cell>
							{/each}
						</Calendar.GridRow>
					{/each}
				</Calendar.GridBody>
			</Calendar.Grid>
		{/each}
	</Calendar.Months>
</CalendarPrimitive.Root>
