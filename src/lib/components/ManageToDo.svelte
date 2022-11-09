<script>
    import ToDoStore from '$lib/stores/ToDoStore.js';
    import { initializeApp } from 'firebase/app';
    import { getFirestore } from 'firebase/firestore';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import { collection, updateDoc, deleteDoc, doc } from 'firebase/firestore';
    import { getAuth } from 'firebase/auth';

    // initiallize fire store
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallize firebase auth
    const auth = getAuth();
    const user = auth.currentUser;

    //collection refrence
    const colRef = collection(db, `users/${user.uid}`, 'todos');

    // deletes todo from database
    async function deleteToDo(id) {
        const docRef = doc(colRef, id);
        try {
            await deleteDoc(docRef);
            console.log('Document deleted with ID:', id);
        } catch (e) {
            console.error('Error deleting document: ', e);
        }
    }

    // indicates completed todo in database
    async function completeToDo(id, status) {
        const docRef = doc(colRef, id);
        try {
            await updateDoc(docRef, { completed: !status });
        } catch (e) {
            console.error('Error updating document: ', e);
        }
    }
</script>

<ul class="mt-10">
    {#each $ToDoStore as todo}
        <li class=" flex justify-between w-full h-150 border-b-2">
            <div
                class=" w-full flex items-center font-mono text-xl font-extralight"
                class:completed={todo.completed}
            >
                <a href={`/todos/${todo.id}`}>{todo.task}</a>
            </div>
            <div class=" h-full">
                <button
                    class=" h-3/6 font-mono text-sm tracking-tighter hover:bg-red-500 px-2 py-0.5 rounded-md hover:font-bold"
                    on:click={deleteToDo(todo.id)}>Delete</button
                >
                <button
                    class=" h-3/6 font-mono text-sm tracking-tighter hover:bg-blue-400 px-2 py-0.5 rounded-md hover:font-bold"
                    on:click={completeToDo(todo.id, todo.completed)}
                    >Done</button
                >
            </div>
        </li>
    {/each}
</ul>

<style>
    .completed {
        text-decoration: line-through;
    }
</style>
