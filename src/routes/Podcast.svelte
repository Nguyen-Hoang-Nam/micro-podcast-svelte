<script lang="ts">
    import { openDB } from "idb";

    let subscriptor: {
        title: string;
        description: string;
        authors: { name: string }[];
        image: {
            url: string;
            title: string;
        };
        items: object[];
    } = {
        title: "",
        description: "",
        authors: [],
        image: { url: "", title: "" },
        items: [],
    };

    let load = false;

    (async () => {
        if (params.title !== "") {
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

            store.get(params.title).then((data) => {
                load = true;
                subscriptor = data;
            });
        }
    })();

    export let params: { title: string } = { title: "" };
</script>

{#if params.title === ""}
    <h1>Podcast not found</h1>
{:else}
    <h1>{subscriptor.title}</h1>
    <h2>
        {#each subscriptor.authors as author}
            {author.name + " "}
        {/each}
    </h2>
    <p>{@html subscriptor.description}</p>
    <img
        class={"podcast-image" + (load ? "" : " none")}
        src={subscriptor.image.url}
        alt={subscriptor.image.title}
    />
{/if}

<style>
    .podcast-image {
        width: 50px;
        height: 50px;
    }
</style>
