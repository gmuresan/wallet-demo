<script type="ts">
    import { browser } from '$app/env';
    import { onMount, onDestroy } from 'svelte';
    import detectGamestopProvider from '@gamestopnft/detect-gamestop-provider';

    let iframe: HTMLIFrameElement;
    let channel: MessageChannel = new MessageChannel();
    const port: MessagePort = channel.port1;
    let loaded = false;

    onMount(() => {
        console.log('add parent event listener a');
        window.addEventListener('message', initWeb3);
    });

    onDestroy(() => {
        console.log('on destroy');
        if (browser) {
            console.log('ON DESTROY WINDOW');
            window.removeEventListener('message', initWeb3);
        }
    });

    const initWeb3 = (e) => {
        if (e.data === 'initWeb3') {
            console.log('parent message', e);
            window.removeEventListener('message', initWeb3);
            onLoad();
        }
    };

    const onLoad = async () => {
        if (loaded) return;
        console.log('parent onload');
        port.onmessage = onMessage;
        iframe.contentWindow?.postMessage('initWeb3', '*', [channel.port2]);
        loaded = true;
        console.log('parent init sent');
    };

    const onMessage = async (msg) => {
        console.log('PARENT MSG', msg);
        const web3 = await detectGamestopProvider();
        const { data } = msg;
        const res = await web3.request({ method: data.method });
        console.log(res);
        port.postMessage({ jsonrpc: data.jsonrpc, id: data.id, result: res });
    };
</script>

<div class="m-16 grid grid-cols-3">
    <div class="h-96 w-80 rounded-xl border-2 border-gray-300 bg-white p-4 shadow-2xl">
        <iframe src="http://localhost:5174" title="app1" bind:this="{iframe}"></iframe>
    </div>
</div>
