<script>
  import {
    Card,
    Button,
    Breadcrumb,
    BreadcrumbItem
  } from 'flowbite-svelte';
  import { invalidateAll } from '$app/navigation';
  import { page } from '$app/stores';
  import RecordUpsertPanel from '$lib/components/records/RecordUpsertPanel.svelte';

  /** @type {import('./$types').PageData} */
  export let data;

  let collection = $page?.data?.tables?.find((t)=> t?.name=="blog");
  let collectionUpsert = $page?.data?.tables?.find((t)=> t?.name=="blog");
  let recordUpsertPanel;

  $: categories = collection?.schema?.find(field => field?.name === 'category')?.options?.values ?? [];
</script>

<Breadcrumb class="pt-20 py-8">
  <BreadcrumbItem href="/" home>Home</BreadcrumbItem>
  <BreadcrumbItem>Articles</BreadcrumbItem>
</Breadcrumb>

<div>
  {#if $page?.data?.user && $page?.data?.user.roles?.includes('blogger')}
    <button type="button" class="btn btn-expanded" on:click={() => recordUpsertPanel?.show()}>
      <i class="ri-add-line" />
      <span class="txt">New record</span>
    </button>
  {/if}

  <ul class="flex flex-wrap gap-2 m-4">
    {#each categories as category (category)}
      <li>
        <a href={`/blog?category=${encodeURIComponent(category)}`} class="bg-blue-500 text-white px-4 py-2 m-2 rounded hover:bg-blue-600 transition">
          {category}
        </a>
      </li>
    {/each}
  </ul>

  <div class="grid relative md:grid-cols-3 gap-6">
    {#each data?.articles?.items ?? [] as article (article?.id)}
      <Card>
        <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">
          {article?.title}
        </h5>
        <p class="mb-3 font-normal text-gray-700 dark:text-gray-400 leading-tight">
          {article?.content}
        </p>
        <Button href={`/blog/${article?.id}`} class="w-fit">
          Read more
        </Button>
      </Card>
    {/each}
  </div>
</div>
{JSON.stringify(data)}

<RecordUpsertPanel
  bind:this={recordUpsertPanel}
  collection={collectionUpsert}
  on:hide={() => {
    recordUpsertPanel?.hide()
  }}
  on:save={(e) => {
    invalidateAll();
  }}
  on:delete={(e) => {
    invalidateAll();
  }}
/>
