<!-- Router.svelte -->
<script>
  import { Router, Link } from "svelte-routing";
  import WordSearchMaker from "./WordSearchMaker.svelte";
  import AnotherPage from "./AnotherPage.svelte";
</script>

<Router>
  <WordSearchMaker path="/" />
  <AnotherPage path="/another-page" />
</Router>
