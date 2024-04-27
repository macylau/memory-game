<script>
	import '../app.postcss';
	import { AppShell, AppBar } from '@skeletonlabs/skeleton';

</script>

<!-- App Shell -->
<AppShell>
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar>
			<svelte:fragment slot="lead">
				<a href="/" class="text-xl font-bold">Gotta Flip'em All!</a>
			</svelte:fragment>
			
		</AppBar>
	</svelte:fragment>
	<slot />
</AppShell>
