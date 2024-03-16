<!-- WordList.svelte -->
<script>
  export let wordList = [];

  // 단어 목록을 받아올 배열
</script>

<div class="word-list">
  <h2>Word List</h2>
  <ul>
    {#if wordList.length > 0}
      {#each wordList as word}
        <li>{word}</li>
      {/each}
    {:else}
      <li>No words to display.</li>
    {/if}
  </ul>
</div>

<style>
  .word-list {
    width: 300px;
    margin-bottom: 20px;
  }

  .word-list h2 {
    text-align: center;
    margin-bottom: 10px;
  }

  .word-list ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }

  .word-list li {
    margin-bottom: 5px;
  }
</style>
