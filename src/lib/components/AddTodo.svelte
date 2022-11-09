<script>
    import { initializeApp } from 'firebase/app';
    import { getFirestore } from 'firebase/firestore';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import { collection, addDoc } from 'firebase/firestore';
    import { getAuth } from 'firebase/auth';
    import { goto } from '$app/navigation';

    // initiallizes fs
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallizes fs auth
    const auth = getAuth();
    const user = auth.currentUser;

    // defines variables
    const colRef = collection(db, `users/${user.uid}`, 'todos');
    let task;

    // handles add todo
    async function addToDo() {
        try {
            const docRef = await addDoc(colRef, {
                task: task,
                completed: false,
                created: new Date(),
                detail: '',
            });

            console.log('Document written with ID: ', docRef.id);
            goto(`/todos/${docRef.id}`);
        } catch (e) {
            console.error('Error adding document: ', e);
        }
        task = '';
    }
</script>

<div class="flex flex-col">
    <form class="flex" on:submit|preventDefault={addToDo}>
        <input
            class="input-lg"
            type="text"
            placeholder="New ToDo"
            bind:value={task}
            on
        />
        <button
            class=" hover:bg-blue-200 bg-slate-200 h-10 rounded-lg hover:shadow-xl font-semibold border-2 border-blue-200 text-md font-mono px-5 mx-4"
            type="submit">Add</button
        >
    </form>
</div>
