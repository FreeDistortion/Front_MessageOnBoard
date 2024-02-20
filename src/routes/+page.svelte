<script>
	import { onMount } from 'svelte';
	import QRCode from 'qrcode';
	import { writable } from 'svelte/store';

	// 메시지를 저장할 writable 스토어 생성
	const messages = writable([]);

	function generateQRCode(elementId, text) {
		const canvas = document.createElement('canvas');
		document.getElementById(elementId).appendChild(canvas);

		QRCode.toCanvas(canvas, text, {
			width: 128,
			height: 128,
		}).catch(console.error);
	}
	
	// websocket을 사용하여 구현하면 오래 걸리므로 주기적으로 request 보내는 거로...
  
	function startPolling(interval) {
         // 초기 데이터 로드
		fetchData();

		 // 주기적으로 데이터 요청
        return setInterval(fetchData, interval);
    }

    onMount(() => {
        const intervalId = startPolling(60*1000);

        return () => {
			 // 컴포넌트가 언마운트될 때 폴링 중지
            clearInterval(intervalId);
        };
    });

	async function fetchData() {
        try {
            const response = await fetch('http://kosign.iptime.org:65535/vks/message');
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            const data = await response.json();
			 // JSON 전체를 스토어에 저장
            messages.set(data);
        } catch (error) {
            console.error('There has been a problem with your fetch operation:', error);
        }
    }



	onMount(() => {
		generateQRCode('qrcode-left', 'http://kosign.iptime.org:65534/msg');
		generateQRCode('qrcode-right', 'http://kosign.iptime.org:65534/msg');
		fetchData();
	});
</script>

<svelte:head>
	<title>Welcome To KoreaSign</title>
	<meta name="description" content="KoreaSign MOB Project Page." />
</svelte:head>

<div class="grid-container">
<div id="qrcode-left" class="qr-code"></div>
	<div class="message-recieved">
		{#each $messages as {id, content, createdBy, createdAt}}
		<p>{content}</p>
	{/each}
	</div>
<div id="qrcode-right" class="qr-code"></div>
</div>

<style>
.grid-container {
	display: grid;
	grid-template-columns: auto 1fr auto;
	align-items: center;
	gap: 20px;
	padding: 0 20px;
	height: 100vh;
  }

  .qr-code {
      width: 128px;
      height: 128px;
      display: flex;
      justify-content: center;
      align-items: center;
  }

  .message-recieved {
      resize: horizontal;
  }
</style>