<script lang="ts">
  import axios from 'axios';
  import anime from 'animejs';

  let sourceText = 'felicidade', translatedText = '', timeoutID, translation: HTMLDivElement;

  async function translatePTtoEN(sourceText: string): Promise<string> {
    let result = await axios.get(
      'https://translate.googleapis.com/translate_a/single?client=gtx&sl=pt&tl=en&dt=t&q=' +
        encodeURI(sourceText)
    );
    console.log(result.data[0][0][0]);
    return result.data[0][0][0];
  }

  translatePTtoEN(sourceText).then(result => translatedText = result);
</script>

<svelte:head>
  <title>Topcoder Translator</title>
</svelte:head>
<main>
  <h1 class="title">topcoder translator</h1>
  <div class="card">
    <div class="labelInput">
      <b style="display: block;">Input (Portuguese)</b>
      <input type="text" bind:value={sourceText} on:input={ () => {
        if (timeoutID) clearTimeout(timeoutID);
        timeoutID = setTimeout(() => {
          translatePTtoEN(sourceText).then(result => {
            translatedText = result;
            window.requestAnimationFrame(() => {
              let animation = anime.timeline({ loop: false });
              animation.add({
                targets: translation.children,
                delay: anime.stagger(50),
                scale: [0, 1],
                rotate: [90, 0],
                duration: result.length * 50,
                easing: 'spring(1, 80, 10, 0)'
              });
            });
          }); 
        }, 1000);
  } } />  
  </div>
  <div class="labelOutput">
    <div class="translation" bind:this={translation}>
      {#each Array.from(translatedText) as letter}
        <span>{letter}</span>
      {/each}
    </div>
    <b style="display: block; margin-bottom: 2em;">
      Output (English)
    </b>
  </div>
</div>
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Titillium+Web:wght@300&display=swap');
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    background: rgb(46, 0, 73);
    padding: 0;
    margin: 0;
  }
  b {
    font-family: 'Titillium Web', sans-serif;
  }
  input {
    margin-bottom: 1em;
  }
  input:focus {
    outline: none;
  }
  .translation {
    background: black;
    color: white;
  }
  input,
  .translation {
    height: 2em;
    width: 15em;
    text-align: center;
  }
  .title {
    color: white;
    text-shadow: -3px 0 0 black;
  }
  .card {
    padding: 1em 1em 1em 1em;
    background: white;
    box-shadow: 2px 2px 2px black;
    border-radius: 0.5em;
  }
  .labelOutput, .labelInput {
    display: block;
    text-align: center;
  }
  .labelOutput {
    margin: 1em 0;
  }
  .labelInput {
    margin-top: 2em;
    margin-bottom: 1em;
  }
</style>
