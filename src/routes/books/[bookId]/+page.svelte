<script lang="ts">
  import { page } from '$app/stores';
  import Icon from '@iconify/svelte';
  import { liveQuery } from 'dexie';
  import AnchorButton from '~/components/AnchorButton.svelte';
  import Paper from '~/components/Paper.svelte';
  import Speak from '~/components/Speak.svelte';
  import { db } from '~/stores/db';

  const bookId = $page.params.bookId;

  let phrases = liveQuery(() => db.phrases?.where({ bookId }).toArray());

  let book = liveQuery(() => db.books.where({ _id: bookId }).first());
</script>

<svelte:head>
  <title>{$book?.name || ''}</title>
</svelte:head>

{#if $phrases?.length}
  <div class="pharses">
    {#each $phrases as phrase (phrase._id)}
      <a class="pharse" href={`/books/${$page.params.bookId}/${phrase._id}`}>
        <Paper>
          <section>
            <h3 class="meaning">{phrase.meaning}</h3>
            <p class="phrase">{phrase?.pronounciation || phrase.phrase}</p>
            <p class="translation">{phrase?.translation}</p>
            <p class="description">{phrase?.description}</p>
            <div class="speak-section">
              <Speak translation={phrase?.translation} voice={$book?.voice} />
            </div>
          </section>
        </Paper>
      </a>
    {/each}
  </div>
{:else}
  <p>No phrases found</p>
{/if}

<div class="buttonHolder">
  <AnchorButton href={`/books/${bookId}/add-pharse`} variant="solid">
    <Icon icon="material-symbols:add" slot="before" /> Add Pharse
  </AnchorButton>
  <br />
  <br />
  <AnchorButton href={`/books/${bookId}/edit`}>Edit book</AnchorButton>
</div>

<style lang="scss">
  .pharse {
    display: block;
    width: 100%;
    text-decoration: none;
    margin-bottom: 16px;
    color: inherit;

    section {
      padding: 0 8px;
    }

    h3 {
      font-size: 1.05rem;
      margin-bottom: 8px;
    }

    p {
      font-size: 1.05rem;
      font-weight: 400;
      margin-top: 0;
      margin-bottom: 8px;

      &.description {
        font-size: 0.88rem;
      }

      &:empty {
        display: none;
      }
    }
  }

  .buttonHolder {
    padding-top: 16px;
  }

  .speak-section {
    &:empty {
      display: none;
    }

    padding-top: 8px;
    padding-bottom: 16px;
  }
</style>
