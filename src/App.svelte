<script>
  import { Router, Route } from "svelte-routing";
  import WordSearchMaker from "./WordSearchMaker.svelte";
  import WordSearchGame from "./WordSearchGame.svelte";
  import firebaseConfig from "./firebaseConfig.js"; // Firebase 설정 파일 불러오기
  import { initializeApp } from "firebase/app";

  // Firebase 초기화
  const app = initializeApp(firebaseConfig);
</script>

<Router>
  <!-- 각 페이지에 대한 라우트 정의 -->
  <Route path="/" component={WordSearchMaker} />
  <Route path="/game" component={WordSearchGame} />
</Router>
