<!-- WordSearchBoard.svelte -->
<script>
  // 필요한 라이브러리를 불러옵니다.
  import { onMount } from "svelte";

  // 게임 보드에 표시할 단어들
  let words = ["APPLE", "BANANA", "CHERRY"];

  // 게임 보드의 크기
  const gridSize = 10; // 10x10 보드

  // 게임 보드를 초기화하는 함수
  function initializeBoard() {
    // 게임 보드 초기화 로직을 여기에 작성합니다.
    const board = [];
    for (let i = 0; i < gridSize; i++) {
      const row = [];
      for (let j = 0; j < gridSize; j++) {
        // 빈 문자열로 초기화
        row.push("");
      }
      board.push(row);
    }

    // 단어를 보드에 배치
    words.forEach((word) => {
      // 단어를 보드에 배치하는 로직을 작성합니다.
      // 생략...
    });

    return board; // 보드 반환
  }

  // 게임 보드 초기화
  let board = initializeBoard();

  // 컴포넌트가 마운트되었을 때 실행되는 함수
  onMount(() => {
    // 이곳에서는 보드를 다시 초기화할 필요가 없습니다.
  });
</script>

<div class="board">
  <!-- 게임 보드를 표시하는 UI -->
  {#each board as row, rowIndex}
    <div class="row" key={rowIndex}>
      {#each row as cell, colIndex}
        <span class="cell" key={colIndex}>{cell}</span>
      {/each}
    </div>
  {/each}
</div>

<style>
  /* 게임 보드의 스타일링 */
  .board {
    display: flex; /* 수정된 부분 */
    flex-wrap: wrap;
    width: 300px; /* 예시로 너비 지정 */
  }

  .row {
    display: flex;
    flex-wrap: wrap;
    width: 100%; /* 각 행의 너비는 보드 전체 너비 */
  }

  .cell {
    width: 30px;
    height: 30px;
    border: 1px solid #ccc;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
