<script context="module">
  import { page } from '$app/stores'
  import Head from '$components/head.svelte'
  import { client } from '$lib/graphql-client'
  import { postsQuery } from '$lib/graphql-queries'
  import {
    fetchSiteMetadata,
    siteMetadataStore,
  } from '$stores/site-metadata'
  import { marked } from 'marked'

  export const load = async () => {
    await fetchSiteMetadata()

    const { posts } = await client.request(postsQuery)

    return {
      props: {
        posts,
      },
    }
  }
</script>

<script>
  export let posts

  const {
    siteUrl,
    name: siteName,
    openGraphDefaultImage,
  } = $siteMetadataStore || []
</script>

<Head
  title={`Blog posts! · ${siteName}`}
  description={`A list of recent blog posts.`}
  image={openGraphDefaultImage.url}
  url={`${siteUrl}${$page.url.pathname}`}
/>

<h1 class="text-4xl mb-10 font-extrabold animate-bounce">Blog Posts</h1>

{#each posts as { title, slug, content, coverImage, tags }}
  <div class="card text-center shadow-2xl mb-20 hover:scale-105 transition duration-300">
    <figure class="">
      <img
        class=""
        src={coverImage.url}
        alt={`Cover image for ${title}`}
      />
    </figure>
    <div class="card-body prose">
      <h2 class="title">{title}</h2>
      {@html marked(content).slice(0, 150)}
      <div class="flex justify-center mt-5 space-x-2">
        {#each tags as tag}
          <span class="badge badge-primary">{tag}</span>
        {/each}
      </div>
      <div class="justify-center card-actions">
        <a href={`/posts/${slug}`} class="btn btn-outline btn-primary"
          >Read &rArr;</a
        >
      </div>
    </div>
  </div>
{/each}
