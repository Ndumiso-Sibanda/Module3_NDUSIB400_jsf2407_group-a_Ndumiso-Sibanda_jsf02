<script>
 import Header from "./components/Header.svelte";
 import { Router, Route } from "svelte-routing";
 import Home from "./pages/Home.svelte";
 import ProductDetailView from "./pages/ProductDetailView.svelte";
</script>

<main>
<Header/>
<Router>
  <Route  path="/" component ={Home} />
  <Route  path="products/:id" component ={ProductDetailView} />
</Router>
</main>

<style>

</style>
