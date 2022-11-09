<script>
    import Profile from '$lib/components/Profile.svelte';
    import UserStore from '$lib/stores/UserStore';
</script>

<div>
    {#if $UserStore}
        <Profile />
    {:else}
        <a href="/login">Please log in</a>
    {/if}
</div>
