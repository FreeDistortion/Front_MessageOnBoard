<script>
    // 사용자로부터 입력받은 메시지를 저장할 반응형 변수
    let message = '';

    // 메시지를 백엔드로 전송하는 함수
    async function sendMessage() {
        try {
            const response = await fetch('http://kosign.iptime.org:65535/public/rcvmsg', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ content: message }),
            });

            if (!response.ok) {
                throw new Error('Server response was not ok.');
            }
            // 메시지 전송 후 입력 필드 초기화
            message = '';
            console.log('Message sent successfully');
        } catch (error) {
            console.error('Failed to send message:', error);
        }
    }
</script>

<svelte:head>
	<title>Send Your Message</title>
	<meta name="description" content="KoreaSign MOB Project Page." />
</svelte:head>

<!-- 메시지 입력 필드. 'bind:value'를 사용하여 'message' 변수와 양방향 데이터 바인딩 -->
<textarea bind:value={message} ></textarea>

<!-- 'sendMessage' 함수를 호출하여 메시지를 전송하는 버튼 -->
<button on:click={sendMessage}>Send Message</button>