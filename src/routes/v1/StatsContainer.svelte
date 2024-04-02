<script>
    export let header;
    export let value;
</script>

<div class="container">
    <div class="header">
        {header}
    </div>

    <div class="value">
        {value}
    </div>
</div>

<style>
    .container {
        display: flex;
        flex-direction: column;
        border: 1px solid black;
    }

    .header, .value {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .header {
        height: 25%;
        margin: 4px 4px;
    }

    .value {
        height: 75%;
        border-top: 1px solid grey;
        padding: 20px;
    }
</style>