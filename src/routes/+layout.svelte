<script>
	import { onMount } from 'svelte';
	import '../app.pcss';

	async function detectSWUpdate() {
		const registration = await navigator.serviceWorker.ready;

		registration.addEventListener('updatefound', () => {
			const newSW = registration.installing;
			newSW?.addEventListener('statechange', () => {
				if (newSW.state === 'installed') {
					if (confirm('Nova atualização disponível. Atualizar para atualizar?')) {
						newSW.postMessage({ type: 'SKIP_WAITING' });
						window.location.reload();
					}
				}
			});
		});
	}

	onMount(() => {
		detectSWUpdate();
	});
</script>

<div class="flex h-full flex-col justify-center p-4">
	<slot />
</div>
