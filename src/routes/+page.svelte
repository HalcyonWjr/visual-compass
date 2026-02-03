<svelte:head>
  <title>Visual Compass</title>
</svelte:head>

<section class="hero">
  <div class="content">
    <h1>Visual Compass</h1>
    <p>Navigate the collection of visualizations.</p>
    <a class="cta" href="/vis-collection">Vis Collection</a>
  </div>
</section>

<style>
  :global(body) {
    margin: 0;
    font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
    color: #111;
    background: #fafafa;
  }

  .hero {
    min-height: 100svh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 2rem;
  }

  .content {
    text-align: center;
  }

  h1 {
    margin: 0 0 0.5rem 0;
    font-size: 2.25rem;
    font-weight: 600;
  }

  p {
    margin: 0 0 1.5rem 0;
    color: #444;
  }

  .cta {
    display: inline-block;
    padding: 0.65rem 1.5rem;
    border: 1px solid #111;
    border-radius: 6px;
    text-decoration: none;
    color: #111;
  }

  .cta:hover {
    background: #111;
    color: #fff;
  }

  .cta:focus-visible {
    outline: 2px solid #111;
    outline-offset: 3px;
  }
</style>
