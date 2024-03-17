<!-- App.svelte -->
<script>
  import { Router, Link } from "svelte-routing";
  import WordSearchMaker from "./WordSearchMaker.svelte";

  // 새로운 상태 변수 추가
  let gameStarted = false;
</script>

<Router>
  <WordSearchMaker path="/" />
</Router>

<style>
  /* 추가한 CSS 스타일 */
  nav {
    text-align: center;
    margin-bottom: 20px;
  }

  nav a {
    margin-right: 10px;
  }
</style>
