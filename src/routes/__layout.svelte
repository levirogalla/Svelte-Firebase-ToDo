<script>
    import { initializeApp } from 'firebase/app';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import { getAuth, onAuthStateChanged, signOut } from 'firebase/auth';
    import { getFirestore, collection, onSnapshot } from 'firebase/firestore';
    import UserStore from '../lib/stores/UserStore';
    import '../app.css';
    import { goto } from '$app/navigation';
    import ToDoStore from '$lib/stores/ToDoStore.js';

    // initiallizes fs
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallize fb auth
    const auth = getAuth();

    //checks to see if user is signed in
    onAuthStateChanged(auth, (user) => {
        if (user) {
            // adds user id to the data store
            const id = auth.currentUser.uid;
            UserStore.update((currentValue) => {
                currentValue = id;
                return currentValue;
            });
            let todos;
            const colRef = collection(db, `users/${user.uid}`, 'todos');

            // conects realtiime db to local todo store
            onSnapshot(colRef, (snapshot) => {
                todos = [];
                snapshot.docs.forEach((doc) => {
                    todos.push({ ...doc.data(), id: doc.id });
                });

                ToDoStore.update((currentTodos) => {
                    currentTodos = todos;
                    return currentTodos;
                });
            });
        } else {
            // defines data as false to prevent the rendering of certain pages
            UserStore.update((currentValue) => {
                currentValue = false;
                return currentValue;
            });
            ToDoStore.update((currentValue) => {
                currentValue = [];
                return currentValue;
            });
        }
    });

    // signs user out
    async function signOutUser() {
        signOut(auth)
            .then(() => {
                goto('/user/login');
                alert('sign out succesful');
            })
            .catch((error) => {
                console.log(error);
            });
    }
</script>

<div>
    <nav
        class="flex w-full justify-between bg-blue-100 p-3 h-15 drop-shadow-lg"
    >
        <div>
            <a class="btn-nav" href="/">Home</a>
            <a class="btn-nav" href="/about">About us</a>
        </div>

        <div>
            {#if $UserStore}
                <a class="btn-nav" href="/user/profile">Profile</a>
                <button class="btn-nav" on:click={signOutUser}>Sign Out</button>
            {:else}
                <a class="btn-nav" href="/user/login">Login</a>
                <a class="btn-nav" href="/user/signup">Sign Up</a>
            {/if}
        </div>
    </nav>

    <div
        class="border-2 w-4/5 mx-auto max-w-xl min-h-64 drop-shadow bg-slate-50 p-5 my-16"
    >
        <slot />
    </div>
</div>
