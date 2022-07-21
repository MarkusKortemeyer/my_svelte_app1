<script>
	import { readable } from 'svelte/store';
	import Profile from './Profile.svelte';
	import ProfileCell from './ProfileCell.svelte';
	import ProfileCell2 from './ProfileCell2.svelte';
	import { createTable, Subscribe, Render, createRender } from 'svelte-headless-table';

	import { createSamples } from './daten';

	const data = readable(createSamples(300));
	const table = createTable(data);

	let listeDayCol = [];
	for (let index = 0; index < 100; index++) {
		const dayColumn = table.column({
			header: 'Day' + index,
			accessor: 'Day' + index
		});
		listeDayCol.push(dayColumn);
		// console.log(dayColumn);
	}

	const columns = table.createColumns([
		table.column({
			header: 'Name',
			accessor: 'lastName'
		}),
		table.column({
			header: 'firstName',
			accessor: 'firstName'
		}),
		table.column({
			header: 'visits',
			accessor: 'visits'
		}),
		table.column({
			header: 'progress',
			accessor: 'progress'
		}),
		table.column({
			header: 'age',
			id: 'age',
			accessor: (item) => item, //übergibt das gesamte Objekt je Zeile
			//  header: 'Age',
			//  accessor: 'age',
			cell: ({ value }) =>
				createRender(ProfileCell, {
					age: value.age,
					progress: value.progress,
					name: `${value.lastName}`
				})
		}),
		table.column({
			header: 'Index',
			accessor: 'index'
		})
	]);

	listeDayCol.forEach((element) => {
		columns.push(element);
	});

	//console.log(columns);

	const { headerRows, rows, tableAttrs, tableBodyAttrs } = table.createViewModel(columns);

	const profile = createRender(Profile, {
		name: 'Alan',
		alter: '12'
	});
</script>

<div>
	<h1>Tabelle 1</h1>
	<div class="tableContainer">
		<table {...$tableAttrs} class="table">
			<thead>
				{#each $headerRows as headerRow (headerRow.id)}
					<Subscribe rowAttrs={headerRow.attrs()} let:rowAttrs>
						<tr {...rowAttrs}>
							{#each headerRow.cells as cell (cell.id)}
								<Subscribe attrs={cell.attrs()} let:attrs>
									<th {...attrs}>
										<Render of={cell.render()} />
									</th>
								</Subscribe>
							{/each}
						</tr>
					</Subscribe>
				{/each}
			</thead>
			<tbody {...$tableBodyAttrs}>
				{#each $rows as row (row.id)}
					<Subscribe rowAttrs={row.attrs()} let:rowAttrs>
						<tr {...rowAttrs}>
							{#each row.cells as cell (cell.id)}
								<Subscribe attrs={cell.attrs()} let:attrs>
									<td {...attrs}>
										<Render of={cell.render()} />
									</td>
								</Subscribe>
							{/each}
						</tr>
					</Subscribe>
				{/each}
			</tbody>
		</table>
	</div>
</div>

<style>
	.tableContainer {
		height: 400px;
		width: 800px;
		overflow: hidden;
	}
	/*	.table {
		position: sticky;
		top: 0;
		width: 100%;
		display: block;
		overflow-x: auto;
		white-space: nowrap;
	} */
	table {
		display: block;
		width: 100%;
		height: 100%;
		overflow: scroll; /* Scrollbar are always visible */
	}

	th,
	td {
		border-bottom: 1px solid black;
		border-right: 1px solid black;
		padding: 0.5rem;
	}
</style>
