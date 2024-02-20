<script>
    let message = '';

    async function sendMessage() {
        const response = await fetch('http://kosign.iptime.org:65535/vks/review', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ content: message }),
        });

        if (response.ok) {
            console.log("Message sent successfully.!");
            message = ''; // Clear the message input after sending
        } else {
            console.error("Failed to send message.");
        }
    }
</script>

<textarea bind:value={message}></textarea>
<button on:click={sendMessage}>Send Message</button>
