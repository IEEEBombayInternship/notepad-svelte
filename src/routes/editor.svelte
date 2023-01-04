<script>
	import {  onMount } from 'svelte'
	import Quill from "quill";
    
	let quill = null;
    // @ts-ignore
    export let container = null;
	
  onMount( () => { 
		// @ts-ignore
		quill = new Quill(container, {
    modules: {
      toolbar: [
        [{ header: [1, 2, 3, false] }],
        ["bold", "italic", "underline", "strike"],
        ["link", "code-block"]
      ]
    },
    placeholder: "Type something...",
    theme: "snow" // or 'bubble'
  });
	})
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  #editor {
    height: 350px;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<svelte:head>
	<link href="//cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">
</svelte:head>

<div id="editor" bind:this={container}/>
