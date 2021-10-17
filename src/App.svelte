<script>
 import Hello from './Hello.svelte';
 import Viewer from './Viewer.svelte';

 let introDone = false;
</script>

{#if introDone }
    <Viewer />
{:else }
    <Hello bind:introDone={introDone} />
{/if}
