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
  let gameStarted = false;
  let dataLoaded = false;

  // 선택한 글자의 시작 위치와 끝 위치를 저장하는 변수
  let startCell = null;
  let endCell = null;

  // 선택된 글자들의 텍스트를 저장하는 변수
  let selectedText = "";

  // 선택한 영역의 시작과 끝 위치를 저장하는 변수
  let selectionStart = null;
  let selectionEnd = null;

  // 마우스의 위치를 저장하는 변수
  let mousePosition = { row: null, col: null };

  // 배열을 Fisher-Yates 알고리즘을 사용하여 무작위로 섞는 함수 추가
  function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  // startGame 함수 수정
  function startGame() {
    gameBoard = shuffleArray(gameBoard); // 게임 시작 전에 게임 보드를 무작위로 섞음
    gameStarted = true;
  }

  // 클릭된 cell-box의 인덱스를 받아와서 처리하는 함수
  function handleClick(row, col) {
    if (!gameStarted) return; // 게임이 시작되지 않았으면 클릭 처리 중지

    // 이미 선택된 글자가 있다면 클릭을 무시
    if (startCell !== null && endCell !== null) return;

    if (startCell === null) {
      // 시작 위치를 설정
      startCell = { row, col };
      selectionStart = { row, col }; // 선택 시작 위치 설정
    } else {
      // 끝 위치를 설정
      endCell = { row, col };
      selectionEnd = { row, col }; // 선택 끝 위치 설정

      // 가로 또는 세로 방향으로 선택된 글자들의 텍스트를 저장
      selectedText = getSelectedText();

      // 선택된 셀이 가로 또는 세로 방향이 아니면 선택 초기화
      if (selectedText === "") {
        startCell = null;
        endCell = null;
        selectionStart = null;
        selectionEnd = null;
      }
    }
  }

  // 선택된 글자들의 텍스트를 가져오는 함수
  function getSelectedText() {
    let text = "";

    // 가로 방향으로 선택된 글자들의 텍스트를 저장
    if (startCell.row === endCell.row || startCell.col === endCell.col) {
      // 시작 위치와 끝 위치가 가로로 같은 행에 있는 경우
      if (startCell.row === endCell.row) {
        // 시작 위치의 열이 끝 위치의 열보다 작은 경우
        if (startCell.col < endCell.col) {
          for (let col = startCell.col; col <= endCell.col; col++) {
            text += gameBoard[startCell.row * 10 + col];
          }
        } else {
          // 시작 위치의 열이 끝 위치의 열보다 크거나 같은 경우
          for (let col = startCell.col; col >= endCell.col; col--) {
            text += gameBoard[startCell.row * 10 + col];
          }
        }
      } else {
        // 시작 위치와 끝 위치가 세로로 같은 열에 있는 경우
        // 시작 위치의 행이 끝 위치의 행보다 작은 경우
        if (startCell.row < endCell.row) {
          for (let row = startCell.row; row <= endCell.row; row++) {
            text += gameBoard[row * 10 + startCell.col];
          }
        } else {
          // 시작 위치의 행이 끝 위치의 행보다 크거나 같은 경우
          for (let row = startCell.row; row >= endCell.row; row--) {
            text += gameBoard[row * 10 + startCell.col];
          }
        }
      }
    }

    return text;
  }

  // 게임 데이터 불러오기
  async function fetchGameData() {
    try {
      const gameBoardSnapshot = await get(ref(db, "gameBoard"));
      if (gameBoardSnapshot.exists()) {
        gameBoard = Object.values(gameBoardSnapshot.val())[0].letters;
      } else {
        throw new Error("No game board data found");
      }

      const wordListSnapshot = await get(ref(db, "wordList"));
      if (wordListSnapshot.exists()) {
        wordList = Object.values(wordListSnapshot.val());
      } else {
        throw new Error("No word list data found");
      }

      // 데이터가 로드되었음을 표시합니다.
      dataLoaded = true;
    } catch (error) {
      console.error("Error fetching game board:", error.message);
      console.error("Error fetching word list:", error.message);
    }
  }

  function handleMouseMove(event) {
    if (startCell !== null) {
      const rect = event.target.getBoundingClientRect(); // 요소의 상대적인 위치를 가져옵니다.
      const x = event.clientX - rect.left; // 마우스의 X 좌표를 요소의 상대적인 X 좌표로 변환합니다.
      const y = event.clientY - rect.top; // 마우스의 Y 좌표를 요소의 상대적인 Y 좌표로 변환합니다.
      const row = Math.floor(y / 32); // 마우스의 Y 좌표로부터 행 계산
      const col = Math.floor(x / 32); // 마우스의 X 좌표로부터 열 계산
      mousePosition = { row, col }; // 마우스 위치 업데이트
    }
  }

  // 게임 데이터 불러오기
  onMount(fetchGameData);
</script>

<div class="game-maker">
  <!-- 게임 제작 기능 UI -->
  <h1>Word Search Game</h1>
  <button on:click={startGame}>Start Game</button>

  <!-- 게임 보드 영역 -->
  <div class="game-board" on:mousemove={(event) => handleMouseMove(event)}>
    {#if gameStarted}
      <!-- 게임이 시작되었을 때만 게임 보드를 표시 -->
      {#each gameBoard as letter, i}
        <div
          class="cell-box"
          on:click={() => handleClick(Math.floor(i / 10), i % 10)}
        >
          <div class="cell">{letter}</div>
        </div>
      {/each}
    {:else}
      <p>'Start Game' 버튼을 눌러주세요</p>
    {/if}
    {#if selectionStart !== null && mousePosition !== null}
      {#if mousePosition.col !== null}
        <div
          class="selection"
          style="
            left: {selectionStart.col * 32 + 2}px;
            top: {selectionStart.row * 32 + 2}px;
            width: {(mousePosition.col - selectionStart.col + 1) * 32 - 4}px;
            height: {(mousePosition.row - selectionStart.row + 1) * 32 - 4}px;
          "
        />
      {:else}
        <div
          class="selection"
          style="
            left: {selectionStart.col * 32 + 2}px;
            top: {selectionStart.row * 32 + 2}px;
            width: 0px; /* 마우스가 아직 이동하지 않은 경우 너비를 0으로 설정 */
            height: 0px; /* 마우스가 아직 이동하지 않은 경우 높이를 0으로 설정 */
          "
        />
      {/if}
    {/if}
  </div>

  <!-- 선택된 글자들의 텍스트 표시 -->
  {#if selectedText !== ""}
    <p>Selected Text: {selectedText}</p>
  {/if}

  <!-- 단어 목록 영역 -->
  <div class="word-list">
    <h2>Word List</h2>
    <ul>
      {#each wordList as { id, word }}
        <li key={id}>{word}</li>
      {/each}
    </ul>
  </div>
</div>

<style>
  /* 게임 제작 기능 영역 스타일 */
  .game-maker {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  /* 게임 보드 스타일 */
  .game-board {
    display: flex;
    flex-wrap: wrap;
    border: 1px solid #ccc;
    margin-bottom: 20px;
    width: 400px;
    position: relative;
  }

  .cell-box {
    display: inline-block;
    width: 30px;
    height: 30px;
    border: 1px solid #ccc;
    text-align: center;
    line-height: 30px;
    margin: 2px;
    cursor: pointer; /* 마우스 포인터를 손가락 모양으로 변경 */
  }

  .selection {
    position: absolute;
    background-color: yellow;
    border: 2px solid orange; /* 테두리 추가 */
    opacity: 0.5; /* 투명도 조정 */
    pointer-events: none; /* 선택 영역 위에 마우스 이벤트가 발생하지 않도록 설정 */
  }

  /* 선택된 글자들의 텍스트 표시를 위한 스타일 */
  p {
    margin: 5px 0;
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
