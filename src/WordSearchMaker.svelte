<!-- WordSearchMaker.svelte -->
<script>
  // 필요한 모듈 및 Firebase 설정 불러오기
  import { onMount } from "svelte";
  import { initializeApp } from "firebase/app";
  import { getDatabase, ref, get } from "firebase/database";
  import firebaseConfig from "./firebaseConfig.js";
  import { navigate } from "svelte-routing";

  // Firebase 설정 적용
  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);

  // 게임 보드와 단어 목록 초기화
  let gameBoard = [];
  let wordList = [];

  // Firestore에서 게임 보드와 단어 목록을 가져와 변수에 할당
  onMount(async () => {
    const gameBoardSnapshot = await get(ref(db, "gameBoard"));
    const wordListSnapshot = await get(ref(db, "wordList"));

    if (gameBoardSnapshot.exists()) {
      gameBoard = gameBoardSnapshot.val();
    }

    if (wordListSnapshot.exists()) {
      wordList = wordListSnapshot.val();
    }
  });

  // 게임 시작 버튼 클릭 시 게임 페이지로 이동하는 함수
  function startGame() {
    navigate("/game");
  }
</script>

<div class="game-maker">
  <!-- 게임 제작 기능 UI -->
  <h1>Word Search Maker</h1>
  <button on:click={startGame}>Start Game</button>

  <!-- 게임 보드 영역 -->
  <div class="game-board">
    <!-- 예시로 각 셀에 알파벳을 채워넣습니다 -->
    {#each gameBoard as row}
      {#each row.letters as letter}
        <div class="cell">{letter}</div>
      {/each}
    {/each}
  </div>

  <!-- 단어 목록 영역 -->
  <div class="word-list">
    <h2>Word List</h2>
    <ul>
      <!-- 단어 목록을 표시하기 위한 로직을 여기에 추가하세요 -->
      {#each wordList as word}
        <li>{word}</li>
      {/each}
    </ul>
  </div>
</div>

<!-- 게임 제작 기능을 추가 -->
<!-- WordSearchMaker.svelte -->
<style>
  /* 게임 제작 기능 영역 스타일 */
  .game-maker {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  /* 게임 보드 스타일 */
  .game-board {
    display: grid;
    grid-template-columns: repeat(10, 40px); /* 10x10 보드 */
    grid-template-rows: repeat(10, 40px);
    gap: 2px;
    border: 1px solid #000;
    margin-bottom: 20px;
  }

  /* 단어 목록 스타일 */
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

  /* 추가한 버튼 스타일 */
  button {
    margin-bottom: 20px;
  }
</style>
