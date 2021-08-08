<script lang="ts">
    import { openDB } from "idb";

    let subscriptor: string = "";

    // Credit https://gist.github.com/hyamamoto/fd435505d29ebfa3d9716fd2be8d42f0
    const hashCode = (str: string): number => {
        let h: number = 0;
        for (let i = 0; i < str.length; i++)
            h = (Math.imul(31, h) + str.charCodeAt(i)) | 0;

        return h;
    };

    const subscript = async (event: KeyboardEvent) => {
        if (event.key === "Enter") {
            const dbName = "Subscription";
            const storeName = "subscriptions";
            const version = 1;

            fetch("https://micro-podcast.herokuapp.com/rss", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                    rss: subscriptor,
                }),
            })
                .then((res) => res.json())
                .then(async (data) => {
                    const db = await openDB(dbName, version, {
                        upgrade(db) {
                            db.createObjectStore(storeName);
                        },
                    });

                    const transaction = db.transaction(storeName, "readwrite");
                    const store = transaction.objectStore(storeName);

                    await store.put(data, hashCode(data.title));
                });
        }
    };
</script>

<div class="box">
    <h1>Subscription</h1>
    <input type="text" on:keypress={subscript} bind:value={subscriptor} />
</div>

<style>
    .box {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        font-size: 30px;
    }
</style>
