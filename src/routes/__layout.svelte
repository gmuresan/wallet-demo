<script type="ts">
    import '../app.css';
    import detectGamestopProvider from '@gamestopnft/detect-gamestop-provider';
    import { web3Address } from '../stores/web3';

    const onConnect = async () => {
        const web3 = await detectGamestopProvider();
        const res = await web3.request({ method: 'eth_requestAccounts' });
        const address = res[0];
        web3Address.set(address);
    };
</script>

<div class="navbar bg-base-200 px-8">
    <div class="flex-1">
        <span class="btn btn-ghost text-xl normal-case">Interactive NFT Wallet</span>
    </div>
    <div class="flex-none gap-2">
        {#if $web3Address}
            {$web3Address}
        {:else}
            <button on:click="{onConnect}">Connect</button>
        {/if}
    </div>
</div>

<slot />
