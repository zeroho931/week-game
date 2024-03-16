<script>
  // 필요한 모듈 및 Firebase 설정 불러오기
  import { onMount } from "svelte";
  import { initializeApp } from "firebase/app";
  import { getDatabase, ref, get } from "firebase/database";
  import firebaseConfig from "./firebaseConfig.js";

  // Firebase 설정 적용
  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);

  // 게임 보드, 단어 목록, 선택된 단어 초기화
  let gameBoard = [];
  let wordList = [];
  let selectedWord = "";

  // 게임 보드와 단어 목록을 Firestore에서 가져와 변수에 할당
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

  // 단어 선택 함수
  function selectWord(word) {
    selectedWord = word;
  }

  // 게임 로직 및 관련 라이브러리 불러오기
  import WordSearchBoard from "./WordSearchBoard.svelte"; // 단어 찾기 보드 컴포넌트 불러오기

  // 게임에서 발견된 단어 목록
  let foundWords = [];

  // 게임 종료 시 실행되는 함수
  function gameOver(words) {
    foundWords = words;
  }

  // 컴포넌트가 마운트된 후 실행되는 함수
  onMount(() => {
    // 여기에 게임 초기화 로직 추가
    initializeGame();
  });

  // 게임 초기화 함수
  function initializeGame() {
    // 게임 보드 초기화 로직
    gameBoard = [
      // 게임 보드 배열 예시
      { letters: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"] },
      { letters: ["K", "L", "M", "N", "O", "P", "Q", "R", "S", "T"] },
      { letters: ["U", "V", "W", "X", "Y", "Z", "A", "B", "C", "D"] },
      // 나머지 보드 배열도 추가해주세요
    ];

    // 단어 목록 초기화 로직
    wordList = ["APPLE", "BANANA", "CHERRY"]; // 단어 목록 예시

    // 선택된 단어 초기화
    selectedWord = "";

    // 발견된 단어 목록 초기화
    foundWords = [];
  }
</script>

<h1>Word Search</h1>
<div class="game">
  <!-- 게임 화면 UI -->
  <h1>Word Search Game</h1>
  <WordSearchBoard on:gameOver={gameOver} />
  <!-- 게임 현황판 영역 -->
  <div class="game-status">
    <h2>Game Status</h2>
    <ul>
      <li>Found words:</li>
      {#each foundWords as word}
        <li>{word}</li>
      {/each}
    </ul>
  </div>
  <!-- 게임 보드 영역 -->
  <div class="game-board">
    {#each gameBoard as row}
      {#each row.letters as letter}
        <div class="cell">{letter}</div>
      {/each}
    {/each}
  </div>
</div>

<style>
  h1 {
    text-align: center;
    margin-bottom: 20px;
  }

  /* 게임 영역 스타일 */
  .game {
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

  /* 게임 현황판 스타일 */
  .game-status {
    width: 300px;
    margin-bottom: 20px;
  }
</style>
