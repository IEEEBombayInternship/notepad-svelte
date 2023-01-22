<!-- This script makes a quill js instance and binds it to a div
Edit this script to make changes to the editor window -->

<script >
// @ts-nocheck

	import {  onMount } from 'svelte'
	import Quill from "quill";
  //v2
  import HandleUser from './handleUsers.svelte';
  let userHandler;
    
	let quill = null;
  let docTitle ='';
    // @ts-ignore
  export let container = null;

  var toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'],        // toggled buttons
  ['blockquote', 'code-block'],

  [{ 'header': 1 }, { 'header': 2 }],               // custom button values
  [{ 'list': 'ordered'}, { 'list': 'bullet' }],
  [{ 'script':'sub'}, { 'script': 'super' }],      // superscript/subscript
  [{ 'indent': '-1'}, { 'indent': '+1' }],          // outdent/indent
  [{ 'direction': 'rtl' }],                         // text direction

  [{ 'size': ['small', false, 'large', 'huge'] }],  // custom dropdown
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],

  [{ 'color': [] }, { 'background': [] }],          // dropdown with defaults from theme
  [{ 'font': [] }],
  [{ 'align': [] }],

  ['clean'],                                         // remove formatting button
  ['link', 'image', 'video'],
  ['spanblock']
];


  //v2
  function handleSave(){


    let text = quill.getText();
    userHandler.addData(docTitle, text);

   
  }
	
  onMount( () => { 
		// @ts-ignore
		quill = new Quill(container, {
      modules: {
    toolbar: toolbarOptions
  },
  theme: 'snow',
    placeholder: "Type something..."
  });
	
  
  })



  export function setEditorText(text){
    quill.setText(text)
  }

  export function updateTitle(title){
    docTitle = title
  }
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }


  
  #editor{
   height: 80vh;  
   width: 50wh; 
   }

   .title{
    border: none; 
  font-size: xx-large;
  background-color: #D9D9D9;
  color: #000000;
  font-family:Arial, Helvetica, sans-serif;
  border-radius: 10px;
  margin-left: 10px;
   }
 


   .save{
  background-color: #0066FF;
  color: #ffffff;
  border: none;
  font-size: 1.3em;
  font-weight: 300;
  position: relative;
  outline: none;
  border-radius: 10px;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  height: 30px;
  width: 70px;
}

.save:hover{
color: #000000;
  background-color: #ffffff;
  text-decoration: none;
}

</style>

<svelte:head>
	<link href="//cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">
</svelte:head>
<div style="background-color: #D9D9D9; border-radius:10px">
  <input type="text" bind:value={docTitle} class="title" placeholder="Untitled Document"/>
  <button class="save" on:click={handleSave}>save</button>
</div>
<br>
<div id="editor" bind:this={container}/>
<!-- 
v2 -->

<HandleUser bind:this={userHandler}></HandleUser>
