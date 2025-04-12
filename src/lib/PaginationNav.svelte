<script lang="ts">
  import { createEventDispatcher } from 'svelte'
  import { generateNavigationOptions } from './generateNavigationOptions'
  import { SymbolType } from './types'
  import type { NavigationOption } from './types'

  const dispatch = createEventDispatcher()

  interface Props {
    totalItems?: number;
    pageSize?: number;
    currentPage?: number;
    limit?: number | undefined;
    showStepOptions?: boolean;
    number?: import('svelte').Snippet<[any]>;
    ellipsis?: import('svelte').Snippet;
    prev?: import('svelte').Snippet;
    next?: import('svelte').Snippet;
  }

  let {
    totalItems = 0,
    pageSize = 1,
    currentPage = 1,
    limit = undefined,
    showStepOptions = false,
    number,
    ellipsis,
    prev,
    next
  }: Props = $props();

  let options = $derived(generateNavigationOptions({
    totalItems,
    pageSize,
    currentPage,
    limit,
    showStepOptions
  }))

  let totalPages = $derived(Math.ceil(totalItems / pageSize))

  function handleOptionClick (option: NavigationOption) {
    dispatch('setPage', { page: option.value })
  }
</script>

<div class="pagination-nav">
  {#each options as option}
    <span
      class="option"
      class:number="{option.type === 'number'}"
      class:prev="{option.type === 'symbol' && option.symbol === SymbolType.PREVIOUS_PAGE}"
      class:next="{option.type === 'symbol' && option.symbol === SymbolType.NEXT_PAGE}"
      class:disabled="{
        (option.type === 'symbol' && option.symbol === SymbolType.NEXT_PAGE && currentPage >= totalPages) ||
        (option.type === 'symbol' && option.symbol === SymbolType.PREVIOUS_PAGE && currentPage <= 1)
      }"
      class:ellipsis="{option.type === 'symbol' && option.symbol === SymbolType.ELLIPSIS}"
      class:active="{option.type === 'number' && option.value === currentPage}"
      role="presentation"
      onclick={() => handleOptionClick(option)}
    >
      {#if option.type === 'number'}
        {#if number}{@render number({ value: option.value, })}{:else}
          <span>{option.value}</span>
        {/if}
      {:else if option.type === 'symbol' && option.symbol === SymbolType.ELLIPSIS}
        {#if ellipsis}{@render ellipsis()}{:else}
          <span>...</span>
        {/if}
      {:else if option.type === 'symbol' && option.symbol === SymbolType.PREVIOUS_PAGE}
        {#if prev}{@render prev()}{:else}
          <svg
            style="width:24px;height:24px"
            viewBox="0 0 24 24"
          >
            <path
              fill="#000000"
              d="M15.41,16.58L10.83,12L15.41,7.41L14,6L8,12L14,18L15.41,16.58Z"
            />
          </svg>
        {/if}
      {:else if option.type === 'symbol' && option.symbol === SymbolType.NEXT_PAGE}
        {#if next}{@render next()}{:else}
          <svg
            style="width:24px;height:24px"
            viewBox="0 0 24 24"
          >
            <path
              fill="#000000"
              d="M8.59,16.58L13.17,12L8.59,7.41L10,6L16,12L10,18L8.59,16.58Z"
            />
          </svg>
        {/if}
      {/if}
    </span>
  {/each}
</div>