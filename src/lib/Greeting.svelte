<script>

</script>

<main>
    <div class="greetingsWrap">
        <p style="color: var(--accent-red);">Hello Wordl!</p>
        <h1><span class="dim">I'm</span> Alexandru Gheorghe.</h1>
        <h2 style="color: var(--accent-green);">Software Developer.</h2>
    </div>
</main>

<style>
    @import url('https://fonts.googleapis.com/css2?family=DM+Mono:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap');
    main{
        height: 100vh;
        color: var(--text);
        font-family: 'DM Mono', monospace;
        position: relative;
    }
    .dim{
        color: var(--dim-text);
    }
    .greetingsWrap{
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-100%, -100%);
        background-color: #0000005e;
        backdrop-filter: blur(5px);
        padding: 20px;
        border-radius: 8px;
    }
</style>
