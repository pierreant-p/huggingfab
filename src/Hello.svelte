<script>
 export let introDone = false;

 import { fade, fly } from 'svelte/transition';


 function handleContinue() {
     introDone = true;
 }
</script>

<div class=container>
    <div class=popup>
        <img class=hf src="./huggingface.svg" /> <span class=plus>+</span> <img class=sketchfab src="./sketchfab-logo.svg" />
        <p class=subtitle>A <span class=sketchfab>Sketchfab</span> + <span class=huggingface>Huggingface</span> experiment</p>
        <p class=desc>This demo uses the Huggingface inference API to trigger animations on a Sketchfab model.</p>
        <button class=button on:click|preventDefault={handleContinue}>Let's Go !</button>
    </div>
</div>

<style>
 .container {
     display: flex;
     align-items: center;
     justify-content: center;
     width: 100%;
     height: 100%;
     text-align: center;
 }

 .popup {
     background: #FFF;
     max-width: 75%;
     padding: 30px 60px;
 }

 h1 {
     margin: 0;
     padding: 0;
     font-family: 'Courier New', Courier, monospace;
 }

 .hf {
     width: 65px;
     margin-right: 8px;
 }

 .sketchfab {
     margin-left: 8px;
     width: 60px;
 }

 .plus {
     font-size: 48px;
     position: relative;
     top: -10px;

 }

 p {
     font-family: 'Courier New', Courier, monospace;
 }

 .subtitle {
     font-size: 24px;
 }

 .desc {
     font-size: 18px;
 }

 .button {
     margin-top: 20px;
     padding: 16px;
 }

 .button:hover {
     background-color: #1CAAD9;
     color: #F2F2F2;
     border: 1px solid #1CAAD9;
 }

 .button:active {
     background-color: orange;
     border: 1px solid orange;
 }
</style>
