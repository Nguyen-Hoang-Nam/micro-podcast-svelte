<script lang="ts">
    /* import { link } from "svelte-spa-router"; */
    import { openDB } from "idb";

    let subscriptors: object = {};

    (async () => {
        const dbName = "Subscription";
        const storeName = "subscriptions";
        const version = 1;

        const db = await openDB(dbName, version, {
            upgrade(db) {
                db.createObjectStore(storeName);
            },
        });

        const transaction = db.transaction(storeName, "readwrite");
        const store = transaction.objectStore(storeName);

        subscriptors = await store.getAll();
    })();

    const isEmpty = (obj: object): boolean => {
        return Object.keys(obj).length === 0;
    };
</script>

{#if isEmpty(subscriptors)}
    <div class="not-subscription">
        <p>You have not subscripted yet.</p>
        <a href="#/subscription" class="subscription-button">Try one</a>
    </div>
{:else}
    <div class="container">
        <h1>Your subscriptions</h1>
        <div class="grid-container">
            {#each Object.values(subscriptors) as subscriptor}
                <a
                    href={"#/podcast/" + subscriptor.title}
                    class="podcast-container"
                >
                    <img
                        class="podcast-image"
                        src={subscriptor.image.url}
                        alt={subscriptor.image.title}
                    />
                    <p class="line-clamp">{subscriptor.title}</p>
                </a>
            {/each}
        </div>
    </div>
{/if}

<style>
    .not-subscription {
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 30px;
    }

    .subscription-button {
        display: block;
        width: 150px;
        height: 40px;
        border: 1px solid black;
        border-radius: 5px;
        line-height: 40px;
        text-align: center;
        margin-left: 10px;
    }

    .container {
        width: 580px;
        margin: auto;
    }

    .grid-container {
        display: grid;
        grid-template-columns: 100px 100px 100px 100px 100px;
        grid-template-rows: 150px 150px 150px 150px 150px;
        column-gap: 20px;
        row-gap: 10px;
    }

    .podcast-image {
        width: 100px;
        height: 100px;
        border-radius: 5px;
    }

    .podcast-container {
        width: 100px;
        display: block;
    }

    .line-clamp {
        margin: 0;
        padding-top: 5px;
        font-size: 15px;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }
</style>
