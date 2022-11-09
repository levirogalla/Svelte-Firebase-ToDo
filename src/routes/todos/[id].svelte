<script context="module">
    // gets page id to find show details for todo
    export async function load({ params }) {
        const id = params.id;
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth();
        const user = auth.currentUser;
        const docRef = doc(db, `users/${user.uid}`, 'todos', `${id}`);
        const docSnap = await getDoc(docRef);
        let todo = docSnap.data();
        return {
            props: {
                todo,
                id,
            },
        };
    }
</script>

<script>
    import { doc, getDoc, updateDoc } from 'firebase/firestore';
    import { getFirestore } from 'firebase/firestore';
    import { initializeApp } from 'firebase/app';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import { getAuth } from 'firebase/auth';

    // initiallize fs
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallize fb auth
    const auth = getAuth();
    const user = auth.currentUser;

    // gets the variable from the ssr for this page
    export let todo;
    export let id;

    let detail = todo.detail;

    // gets the todo details
    async function detailToDo() {
        const docRef = doc(db, `users/${user.uid}`, 'todos', `${id}`);
        try {
            await updateDoc(docRef, { detail: detail });
        } catch (e) {
            console.error('Error detailing document: ', e);
        }
    }
</script>

<div>
    <h2>{todo.task}</h2>
    {#if todo.completed === true}
        <p>This task has been completed</p>
    {:else}
        <p>This task has not been completed</p>
    {/if}
    <p>this todo was created at {todo.created}</p>

    <textarea cols="auto" rows="1" bind:value={detail} />
    <button type="submit" on:click={detailToDo}> update </button>
</div>
