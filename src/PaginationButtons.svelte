<script>
    import { options } from './stores/options.js';
    import { rowCount, pageNumber, datatableWidth } from './stores/state.js';
    import { columns } from './stores/columns.js';
    export let ref = '';
    export let classList = '';
    $: pageCount = Array.from(
        Array(Math.ceil($rowCount / $options.rowPerPage)).keys()
    );
    const slice = (arr, page) => {
        if (page < 5) {
            return arr.slice(0, 5);
        } else if (page > arr.length - 4) {
            return arr.slice(arr.length - 5, arr.length);
        }
        return arr.slice(page - 2, page + 1);
    };
    $: buttons = slice(pageCount, $pageNumber);
    const setPage = (number) => {
        pageNumber.set(number);
        columns.redraw();
    };
</script>

<style>
    section {
        display: flex;
        flex-direction: row;
    }

    .active {
        background-color: var(--color-secondary);
        color: white;
    }

    ul {
        flex: 1;
        float: right;
        list-style: none;
    }

    li {
        float: left;
    }

    button {
        background: transparent;
        border: 1px solid #ccc;
        padding: 5px 10px;
        margin-left: 3px;
        float: left;
        cursor: pointer;

        font-size: var(--font-size);
        color: var(--color-darkGrey);
        font-weight: normal;
        line-height: 1;
        text-transform: none;
    }
</style>

{#if $datatableWidth > 600}
    <section
        class="dt-pagination-buttons {classList}"
        ref="{ref}"
        class:css="{$options.css}">
        <button
            class="text"
            class:disabled="{$pageNumber === 1}"
            on:click="{() => setPage($pageNumber - 1)}">
            {@html $options.labels.previous}
        </button>
        <button
            class:active="{$pageNumber === 1}"
            on:click="{() => setPage(1)}">
            1
        </button>
        {#if pageCount.length > 6 && $pageNumber >= 5}
            <button class="ellipse">...</button>
        {/if}

        {#each buttons as n}
            {#if n > 0 && n < pageCount.length - 1}
                <button
                    class:active="{$pageNumber === n + 1}"
                    on:click="{() => setPage(n + 1)}">
                    {n + 1}
                </button>
            {/if}
        {/each}

        {#if pageCount.length > 6 && $pageNumber <= pageCount.length - 3}
            <button class="ellipse">...</button>
        {/if}

        {#if pageCount.length > 1}
            <button
                class:active="{$pageNumber === pageCount.length}"
                on:click="{() => setPage(pageCount.length)}">
                {pageCount.length}
            </button>
        {/if}

        <button
            class="text"
            class:disabled="{$pageNumber === pageCount.length}"
            on:click="{() => setPage($pageNumber + 1)}">
            {@html $options.labels.next}
        </button>
    </section>
{:else}
    <section
        class="dt-pagination-buttons mobile {classList}"
        class:css="{$options.css}">
        <button
            class:disabled="{$pageNumber === 1}"
            on:click="{() => setPage(1)}">&#10092;&#10092;</button>
        <button
            class:disabled="{$pageNumber === 1}"
            on:click="{() => setPage($pageNumber - 1)}">&#10094;</button>
        <button
            class:disabled="{$pageNumber === pageCount.length}"
            on:click="{() => setPage($pageNumber + 1)}">&#10095;</button>
        <button
            class:disabled="{$pageNumber === pageCount.length}"
            on:click="{() => setPage(pageCount.length)}"
            >&#10093;&#10093;</button>
    </section>
{/if}
