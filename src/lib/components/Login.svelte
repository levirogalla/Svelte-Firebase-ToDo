<script>
    import {
        getAuth,
        createUserWithEmailAndPassword,
        signInWithEmailAndPassword,
    } from 'firebase/auth';
    import { initializeApp } from 'firebase/app';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import UserStore from '../stores/UserStore.js';
    import { getFirestore } from 'firebase/firestore';
    import { setDoc, doc, getDoc } from 'firebase/firestore';
    import { goto } from '$app/navigation';

    // initiallize fs
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallize fb auth
    const auth = getAuth();

    // allow title variable to be difined by the current page
    export let title = '';
    let email;
    let password;
    let wasError = false;

    // adds user to the db when created
    async function creatUserData(id) {
        try {
            await setDoc(doc(db, 'users', id), {});

            console.log('User added to detabase');
        } catch (e) {
            console.error('Could not add user', e);
        }
    }

    // stores the signed in users id
    async function fetchUser(id) {
        const docRef = doc(db, 'users', id);
        const docSnap = await getDoc(docRef);

        UserStore.update((currentUser) => {
            currentUser = docSnap.data();
            return currentUser;
        });
    }

    // handels sign up
    async function signUp() {
        createUserWithEmailAndPassword(auth, email, password)
            .then((userCredential) => {
                const user = userCredential.user;
                console.log('User successfully created');
                // adds user data to the db
                creatUserData(user.uid);
            })
            .catch((error) => {
                const errorCode = error.code;
                const errorMessage = error.message;
                console.log(error);
                wasError = true;
            });
    }

    // handels log in
    async function logIn() {
        return signInWithEmailAndPassword(auth, email, password)
            .then((userCredential) => {
                const user = userCredential.user;
                fetchUser(user.uid);
                console.log('User successfully logged in');
                goto('/');
            })
            .catch((error) => {
                const errorCode = error.code;
                const errorMessage = error.message;
                console.log(error);
                wasError = true;
            });
    }
</script>

<div>
    {#if title === 'Sign Up'}
        <form
            action="submit"
            on:submit|preventDefault={signUp}
            class="flex flex-col"
        >
            <div class=" flex h-12 mb-8 items-center justify-between">
                <label
                    class:error={wasError}
                    for="email"
                    class="text-md font-mono px-3">Email:</label
                >
                <input
                    class=" w-9/12 h-10 shadow-md p-2"
                    type="text"
                    name="email"
                    bind:value={email}
                />
            </div>

            <div class="h-12 flex mb-8 items-center justify-between">
                <label
                    class:error={wasError}
                    class=" text-md font-mono px-3"
                    for="password">Password:</label
                >
                <input
                    class="w-9/12 h-10 shadow-md p-2"
                    type="text"
                    name="password"
                    bind:value={password}
                />
            </div>

            <button
                class=" hover:bg-blue-200 bg-slate-200 h-10 rounded-lg hover:shadow-xl font-mono font-semibold border-2 border-blue-200"
                type="submit">{title}</button
            >
        </form>
    {/if}
    {#if title === 'Log In'}
        <form
            action="submit"
            on:submit|preventDefault={logIn}
            class="flex flex-col"
        >
            <div class=" flex h-12 mb-8 items-center justify-between">
                <label
                    class:error={wasError}
                    for="email"
                    class="text-md font-mono px-3">Email:</label
                >
                <input
                    class="input-lg"
                    type="text"
                    name="email"
                    bind:value={email}
                />
            </div>

            <div class="h-12 flex mb-8 items-center justify-between">
                <label
                    class:error={wasError}
                    class=" text-md font-mono px-3"
                    for="password">Password:</label
                >
                <input
                    class="input-lg"
                    type="text"
                    name="password"
                    bind:value={password}
                />
            </div>

            <button
                class=" hover:bg-blue-200 bg-slate-200 h-10 rounded-lg hover:shadow-xl font-mono font-semibold border-2 border-blue-200 "
                type="submit">{title}</button
            >
            <div
                class:show={!wasError}
                class=" font-light text-xs font-mono mt-3  tracking-tighter"
            >
                <p>
                    Don't have an accout? <a
                        class=" underline text-blue-500"
                        href="/user/signup">Sign Up</a
                    >
                </p>
            </div>
        </form>
    {/if}
</div>

<style>
    .error {
        color: red;
        text-decoration: underline;
    }
    .show {
        display: none;
    }
</style>
