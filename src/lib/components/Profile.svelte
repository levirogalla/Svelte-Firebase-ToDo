<script>
    import { doc, setDoc, getFirestore, deleteDoc } from 'firebase/firestore';
    import { firebaseConfig } from '$lib/firebaseConfig.js';
    import { initializeApp } from 'firebase/app';
    import { getAuth, updateProfile, deleteUser, signOut } from 'firebase/auth';
    import { goto } from '$app/navigation';

    // initiallize firestore
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // initiallize firebase auth
    const auth = getAuth();
    const user = auth.currentUser;

    // define variables
    let name = user.displayName;
    let emailVerified = user.emailVerified;
    let email = user.email;

    //updates user info
    async function updateUser() {
        updateProfile(user, {
            displayName: name,
            email: email,
        })
            .then(() => {
                console.log('user updated', user);
                alert('profile updated');
            })
            .catch((error) => {
                console.log('there was and error', error);
            });
    }

    // deletes account from db and fb auth
    async function deleteAllAccounts() {
        try {
            deleteUser(user)
                .then(() => {
                    console.log('User has been deleted from auth system');
                    alert('all user data has been deleted');
                })
                .catch((e) => {
                    console.log('Error removing user from auth system', e);
                });

            deleteDoc(doc(db, 'users', user.uid));

            signOut(auth).then(() => {});
        } catch (e) {
            console.log('There was an error deleting account', e);
        }
    }
</script>

<!-- add password reset later -->

<div>
    <form
        class="flex flex-col"
        action="Submit"
        on:submit|preventDefault={updateUser}
    >
        <div class=" flex h-12 mb-8 items-center justify-between">
            <label for="name" class="text-md font-mono px-3">Name:</label>
            <input class="input-lg" type="text" name="name" bind:value={name} />
        </div>
        <div>
            <div class=" flex h-12 items-center justify-between">
                <label for="email" class="text-md font-mono px-3">Email:</label>
                <input
                    class=" input-lg"
                    type="text"
                    name="email"
                    bind:value={email}
                />
            </div>
            <div
                class="font-light text-xs font-mono tracking-tighter float-right"
            >
                {#if emailVerified}
                    <p>Your email is verified</p>
                {:else}
                    <p>
                        Please verify you email address <a
                            class=" underline text-blue-600 "
                            href="#">here</a
                        >
                    </p>
                {/if}
            </div>
        </div>

        <button
            class=" my-4 hover:bg-blue-200 bg-slate-200 h-10 rounded-lg hover:shadow-xl font-mono font-semibold border-2 border-blue-200 "
            type="submit">Update Information</button
        >
    </form>
    <button
        class=" font-light text-xs font-mono tracking-tighter hover:bg-red-500 px-2 py-0.5 rounded-md hover:font-bold"
        on:click={deleteAllAccounts}>Delete account</button
    >
</div>
