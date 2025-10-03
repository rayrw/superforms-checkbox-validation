<script lang="ts">
	import { z } from 'zod/v4';
	import { superForm, defaults } from 'sveltekit-superforms';
	import { zod4 } from 'sveltekit-superforms/adapters';

	const key = 'key1' as const;
	const defaultData = { [key]: [] };

	const schema = z.object({
		checkboxes: z.record(z.string(), z.array(z.string()).min(1)).default(defaultData)
	});

	const { form, errors, allErrors, enhance } = superForm(defaults(zod4(schema)), {
		SPA: true,
		dataType: 'json',
		resetForm: false,
		invalidateAll: false,
		validators: zod4(schema),
		onUpdate({ form }) {
			if (form.valid) {
				console.log('submitted');
			}
		}
	});

	const checkboxOptions = ['a', 'b', 'c', 'd'];
</script>

<h3>$errors is never updated if I don't submit the form without checking any checkboxes</h3>
<form use:enhance>
	{#each checkboxOptions as option (option)}
		<label>
			<input
				type="checkbox"
				value={option}
				onchange={(e) => {
					if (e.currentTarget.checked) {
						$form.checkboxes[key] = [...$form.checkboxes[key], e.currentTarget.value];
					} else {
						$form.checkboxes[key] = $form.checkboxes[key].filter(
							(checkedOption) => checkedOption !== e.currentTarget.value
						);
					}
				}}
			/>
			{option}
		</label>
	{/each}
	<button type="submit">Submit</button>
	<p>
		$form = {JSON.stringify($form)}
	</p>
	<p>
		$errors = {JSON.stringify($errors.checkboxes)}
	</p>
	<p>
		$allErrors = {JSON.stringify($allErrors)}
	</p>
</form>
