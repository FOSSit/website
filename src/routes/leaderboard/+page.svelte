<script lang="ts">
    import supabase from '$lib/supabase';
    import Glass from '$lib/components/Glass.svelte';
    import Section from '$lib/components/Section.svelte';
    import { onMount } from 'svelte';
    import { base } from '$app/paths';
    import Loading from '$lib/components/Loading.svelte';

    let teams = true;
    let loading = true;

    async function loadTeams() {
        const { data, error } = await supabase.from('leaderboard').select('name, points');
        if (error) console.log('Error Fetching Teams:', error.message);
        if (data.length) leaderboard = data;
        loading = false;
    }

    async function loadIdeas() {
        loading = true;
        const { data, error } = await supabase.from('ideas').select('title, votes, url');
        if (error) console.log('Error Fetching Ideas:', error.message);
        if (data.length) leaderboard = data;
        loading = false;
    }

    onMount(async () => {
        loadTeams();
        resize();
    });

    let arr = [1, 0, 2];
    let leaderboard: any = [
        {
            name: '',
            points: 0
        },
        {
            name: '',
            points: 0
        },
        {
            name: '',
            points: 0
        }
    ];
    function resize() {
        arr = innerWidth > 768 ? [1, 0, 2] : [0, 1, 2];
    }
    async function toggle(e: Event) {
        teams = e.target.getAttribute('data-create') == 'false';
        if (!teams) {
            loadIdeas();
        } else {
            loadTeams();
        }
    }

    function sortLeaderboardDescending() {
        leaderboard.sort((a, b) => b.points - a.points);
    }

    sortLeaderboardDescending(); // Sort the leaderboard initially

</script>

<svelte:window on:resize={resize} />

<Loading {loading}>
    <!-- rest of your HTML code -->
</Loading>
