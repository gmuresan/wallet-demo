<script type="ts">
    import { browser, dev } from '$app/env';
    import { onMount, onDestroy } from 'svelte';
    import detectGamestopProvider from '@gamestopnft/detect-gamestop-provider';
    import { web3Address } from '../stores/web3';

    let iframe: HTMLIFrameElement;
    let iframe2: HTMLIFrameElement;
    let iframe3: HTMLIFrameElement;
    let iframe4: HTMLIFrameElement;

    $: {
        if ($web3Address) {
            window.addEventListener('message', initWeb3);
            iframe?.contentWindow?.postMessage('web3Handshake', '*');
            iframe2?.contentWindow?.postMessage('web3Handshake', '*');
            iframe3?.contentWindow?.postMessage('web3Handshake', '*');
            iframe4?.contentWindow?.postMessage('web3Handshake', '*');
        }
    }

    onMount(() => {
        console.log('add parent event listener a');
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
            onLoad(e);
        }
    };

    const onLoad = async (e) => {
        console.log('parent onload');
        const channel = new MessageChannel();
        const port = channel.port1;
        port.onmessage = async (msg) => {
            console.log('PARENT MSG', msg);
            const web3 = await detectGamestopProvider();
            const { data } = msg;
            const res = await web3.request({ method: data.method, params: data.params });
            console.log(res);
            port.postMessage({ jsonrpc: data.jsonrpc, id: data.id, result: res });
        };
        e.source.postMessage('initWeb3', '*', [channel.port2]);
        console.log('parent init sent');
    };

    const urls = dev
        ? [
              'http://audio:5177',
              'http://collection:5174',
              'http://voting:5175',
              'http://richnft:5176',
          ]
        : [
              'https://audio-nft.on.fleek.co/',
              'https://interactive-nft-demo.vercel.app/',
              'https://voting-e3two3esy-gmuresan.vercel.app/',
              'https://rich-nft-six.vercel.app/',
          ];
</script>

<div class="m-16 grid grid-cols-3 gap-4">
    <div class="h-96 w-80 rounded-xl border-2 border-gray-300 bg-white p-1 shadow-2xl">
        <iframe
            src="{urls[0]}"
            class="h-full w-full rounded-lg"
            title="app1"
            bind:this="{iframe}"
        ></iframe>
    </div>
    <div class="h-96 w-80 rounded-xl border-2 border-gray-300 bg-white p-1 shadow-2xl">
        <iframe
            src="{urls[1]}"
            class="h-full w-full rounded-lg"
            title="app1"
            bind:this="{iframe2}"
        ></iframe>
    </div>

    <div class="h-96 w-80 rounded-xl border-2 border-gray-300 bg-white p-1 shadow-2xl">
        <iframe
            src="{urls[2]}"
            class="h-full w-full rounded-lg"
            title="app1"
            bind:this="{iframe3}"
        ></iframe>
    </div>

    <div class="h-96 w-80 rounded-xl border-2 border-gray-300 bg-white p-1 shadow-2xl">
        <iframe
            src="{urls[3]}"
            class="h-full w-full rounded-lg"
            title="app1"
            bind:this="{iframe4}"
        ></iframe>
    </div>
</div>
