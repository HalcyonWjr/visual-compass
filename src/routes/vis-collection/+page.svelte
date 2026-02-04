<script>
  import { onMount } from "svelte";
  import { base } from "$app/paths";
  import * as d3 from "d3";
  import dataUrl from "$lib/assets/data.csv?url";

  let rows = [];
  let loading = true;
  let error = "";
  let view = "list";

  const authorKey = "Author (& affliation) /Year/...";
  const embedKey = "Embed Link";
  const liveKey = "Live/Public Facing Link(s)";
  const thumbnailKey = "Thumbnails";

  const thumbnails = import.meta.glob("$lib/assets/mini-fig/*", {
    eager: true,
    as: "url"
  });
  const thumbnailBase = "/src/lib/assets/mini-fig/";

  // const formatMeta = (row) => [row.Theme, row[authorKey]].filter(Boolean).join(" - ");
  const formatMeta = (row) => row[authorKey];
  
  const getThumbnailUrl = (row) => {
    const file = row[thumbnailKey];
    if (!file) return "";
    return thumbnails[`${thumbnailBase}${file}`] ?? "";
  };

  const extractLink = (value) => {
    if (!value) return "";
    const match = value.match(/https?:\/\/[^\s,;]+/);
    return match ? match[0] : "";
  };

  const getProjectLink = (row) => {
    const embed = extractLink(row[embedKey]);
    if (embed) return embed;
    return extractLink(row[liveKey]);
  };

  onMount(async () => {
    try {
      rows = await d3.csv(dataUrl);
    } catch (err) {
      error = err instanceof Error ? err.message : "Failed to load data.";
    } finally {
      loading = false;
    }
  });
</script>

<svelte:head>
  <title>PeaceRep Visualizations & Digital Tools</title>
</svelte:head>

<section class="collection">
  <div class="content">
    <header class="header">
      <div>
        <h1>PeaceRep Visualizations & Digital Tools</h1>
        <p class="subtitle">
          {#if loading}
            Loading data...
          {:else}
            {rows.length} items
          {/if}
        </p>
      </div>
      <a class="back" href={`${base}/`}>Back home</a>
    </header>

    <div class="view-toggle" role="group" aria-label="View options">
      <button
        type="button"
        class:active={view === "list"}
        on:click={() => (view = "list")}
      >
        List
      </button>
      <button
        type="button"
        class:active={view === "grid"}
        on:click={() => (view = "grid")}
      >
        Grid
      </button>
    </div>

    {#if error}
      <p class="error">{error}</p>
    {:else if loading}
      <p class="loading">Reading data.csv...</p>
    {:else}
      <ul class:grid={view === "grid"} class="list">
        {#each rows as row}
          <li class="card">
            {#if getThumbnailUrl(row)}
              <img class="thumb" src={getThumbnailUrl(row)} alt={row.Title || "Thumbnail"} />
            {/if}
            <div>
              <div class="title">{row.Title}</div>
              {#if formatMeta(row)}
                <div class="meta">{formatMeta(row)}</div>
              {/if}
              <!-- {#if row.Description}
                <p class="desc">{row.Description}</p>
              {/if} -->
              {#if getProjectLink(row)}
                <a
                  class="link"
                  href={getProjectLink(row)}
                  target="_blank"
                  rel="noreferrer"
                >
                  Open link
                </a>
              {/if}
            </div>
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</section>

<style>
  :global(body) {
    margin: 0;
    font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
    color: #111;
    background: #fafafa;
  }

  .collection {
    min-height: 100svh;
    padding: 2.5rem 1.5rem 3rem;
  }

  .content {
    max-width: 1300px;
    margin: 0 auto;
  }

  .header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 1rem;
    margin-bottom: 2rem;
  }

  h1 {
    margin: 0 0 0.4rem 0;
    font-size: 2rem;
    font-weight: 600;
  }

  .subtitle {
    margin: 0;
    color: #444;
  }

  .back {
    color: #111;
    text-decoration: none;
    border-bottom: 1px solid #111;
    padding-bottom: 2px;
  }

  .back:hover {
    color: #000;
  }

  .view-toggle {
    display: inline-flex;
    gap: 0.5rem;
    margin: 0 0 1.5rem 0;
  }

  .view-toggle button {
    border: 1px solid #111;
    background: transparent;
    color: #111;
    padding: 0.35rem 0.8rem;
    border-radius: 6px;
    cursor: pointer;
  }

  .view-toggle button.active {
    background: #111;
    color: #fff;
  }

  .list {
    list-style: none;
    margin: 0;
    padding: 0;
    border-top: 1px solid #e5e5e5;
  }

  .list.grid {
    border-top: none;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 0.9rem;
  }

  .card {
    padding: 1.5rem 0;
    border-bottom: 1px solid #e5e5e5;
    display: grid;
    grid-template-columns: 160px 1fr;
    gap: 1rem;
    align-items: start;
  }

  .list.grid .card {
    border: 1px solid #e5e5e5;
    border-radius: 10px;
    padding: 1rem;
    background: #fff;
    grid-template-columns: 1fr;
    gap: 0.75rem;
  }

  .title {
    font-weight: 600;
    margin-bottom: 0.35rem;
  }

  .meta {
    color: #555;
    font-size: 0.9rem;
    margin-bottom: 0.5rem;
  }

  .desc {
    margin: 0 0 0.6rem 0;
    color: #333;
  }

  .thumb {
    width: 160px;
    height: 120px;
    object-fit: cover;
    /* object-fit: fill; */
    /* border: 1px solid #e5e5e5; */
    background: #fff;
    border-radius: 6px;
  }

  .list.grid .thumb {
    width: 100%;
    height: 160px;
  }

  .link {
    color: #111;
    text-decoration: none;
    border-bottom: 1px solid #111;
    padding-bottom: 2px;
  }

  .link:hover {
    color: #000;
  }

  .error {
    color: #b00020;
  }

  .loading {
    color: #444;
  }

  @media (max-width: 640px) {
    .header {
      flex-direction: column;
      align-items: flex-start;
    }

    .card {
      grid-template-columns: 1fr;
    }

    .thumb {
      width: 100%;
      height: auto;
      max-height: 180px;
    }
  }
</style>
