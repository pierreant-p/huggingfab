
<script>
 import { onMount } from 'svelte';
 import { fade } from 'svelte/transition';

 export let modelUid = "abf5788c354f4d4b9ff5ea1fd10c6496";

 let viewer;
 let loadingProgress = 0;
 $: viewerIsReady = loadingProgress >= 1;

 let client;

 let loadViewer = false;
 loadViewer = true;

 let query = "";
 let animations;

 const sanitizeQuery = (q) => {
     return q.trim().toLowerCase();
 }

 const getAnimationCandidates = () => {
     return animations.map((animation) => animation[1].replaceAll('_', ' '));
 };


 async function handleQuery() {

     const response = await fetch(
         "https://api-inference.huggingface.co/models/facebook/bart-large-mnli",
         {
             method: "POST",
             body: JSON.stringify({
                 inputs: sanitizeQuery(query),
                 parameters: {
                     candidate_labels: getAnimationCandidates()
                 },
             }),
         },
     );
     const result = await response.json();

     let match = result.labels[0];
     for (const animation of animations) {
         if (animation[1].replaceAll('_', ' ').toLowerCase() === match.trim().toLowerCase()) {
             const animationUid = animation[0];
             client.setCurrentAnimationByUID(animationUid);
         }
     }
     query = "";

 };



 onMount(() => {
     if (loadViewer) {
         client = new Sketchfab(viewer);
         client.init(modelUid, {
             success: (apiClient) => {
             client = apiClient;
                 client.start();

                 client.addEventListener('modelLoadProgress', function(factor) {
                     loadingProgress = factor.progress;
                 });

                 client.addEventListener("viewerready", function () {
                     client.getAnimations(function(err, results) {
                         // Filter out animations that don't look good
                         animations = results.filter(animation => {
                             const name = animation[1];
                             return  name !== 'Walk' && name !== 'Roll' && name !== 'Run' && name !== 'Land' && name !== 'Idle_2';
                         });
                     });
                 });
             },
         });
     }

 });

</script>

<div class=container>
    {#if !viewerIsReady}
        <div
            transition:fade
            class=loader
            class:loading={!viewerIsReady}
        >
            <div class=loading-logo-grayscale></div>
            <div class=loading-logo style="height: {100 * loadingProgress}px"></div>
            <div>Loading {Math.floor(100 * loadingProgress)}%</div>
        </div>
    {/if}

    <iframe
        bind:this={viewer}
        class:viewer
        class:started={viewerIsReady}
        title="Forest Animal: Fox"
        frameborder="0"
        allowfullscreen="true"
        mozallowfullscreen="true"
        webkitallowfullscreen="true"
        allow="autoplay; fullscreen; xr-spatial-tracking"
        xr-spatial-tracking="true"
        execution-while-out-of-viewport="true"
        execution-while-not-rendered="true"
        web-share="true"
        src=""
    />

    {#if viewerIsReady}
        <form class=form
              transition:fade={{duration: 300}}
              on:submit|preventDefault={handleQuery}>
            <input
                class=query
                bind:value={query}
                placeholder="Tell me what to do and press enter !"
                autocomplete
                autofocus
            />
         </form>
    {/if}

</div>

<style>
 .container {
     display: flex;
     align-items: center;
     justify-content: center;
     width: 100%;
     height: 100%;

 }

 .viewer {
     width: 100%;
     height: 100%;
     opacity: 0;
     visibility: hidden;
 }

 .started {
     visibility: visible;
     opacity: 1;
     transition: opacity 2s;
 }

 .loader {
     display: none;
     position: absolute;
     width: 180px;
     margin-left: 20px;
     padding: 60px;
     border-radius: 10px;
     text-align: center;
     font-size: 24px;
     font-family: 'Courier New', Courier, monospace;
 }

 .loading-logo, .loading-logo-grayscale  {
     background: url(../huggingface.svg) center center no-repeat;
     margin-left: 30px;
     height: 120px;
     width: 120px;
 }

 .loading-logo {
     position: absolute;
     bottom: 103px;
     height: 0px;
     background-position-y: bottom;
     transition: height 0.3s;
 }

 .loading-logo-grayscale {
     filter: grayscale(1);
 }

 .loading {
     display: block;
 }

 .form {
     position: absolute;
     bottom: 10%;
     left: 25%;
     width: 50%;
     margin: 0;
     padding: 0;
 }

 .query {
     font-size: 25px;
     margin: 0;
     width: 100%;
     box-shadow: #666 0px 0px 10px;
     border: 1px solid #1CAAD9;
     border-radius: 4px;
     color: #333;
 }
</style>
