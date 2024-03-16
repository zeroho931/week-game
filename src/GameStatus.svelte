<!-- GameStatus.svelte -->
<script>
  export let foundWords = [];

  // 현재 찾은 단어 목록을 보여줄 배열
</script>

<div class="game-status">
  <h2>Game Status</h2>
  <ul>
    {#if foundWords.length > 0}
      {#each foundWords as word}
        <li>{word}</li>
      {/each}
    {:else}
      <li>No words found yet.</li>
    {/if}
  </ul>
</div>

<style>
  .game-status {
    width: 300px;
    margin-bottom: 20px;
  }

  .game-status h2 {
    text-align: center;
    margin-bottom: 10px;
  }

  .game-status ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }

  .game-status li {
    margin-bottom: 5px;
  }
</style>
