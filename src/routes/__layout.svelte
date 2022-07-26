<script type="ts">
    import '../app.css';
    import detectGamestopProvider from '@gamestopnft/detect-gamestop-provider';

    let address: string;

    const onConnect = async () => {
        const web3 = await detectGamestopProvider();
        const res = await web3.request({ method: 'eth_requestAccounts' });
        address = res[0];
    };
</script>

<div class="navbar bg-base-200 px-8">
    <div class="flex-1">
        <span class="btn btn-ghost text-xl normal-case">Interactive NFT Wallet</span>
    </div>
    <div class="flex-none gap-2">
        {#if address}
            {address}
        {:else}
            <button on:click="{onConnect}">Connect</button>
        {/if}
    </div>
</div>

<slot />
