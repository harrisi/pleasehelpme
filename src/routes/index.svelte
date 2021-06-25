<script>
import { onMount } from 'svelte'
import { dev } from '$app/env'

let bid

let urlBase = 'https://pleasehelpme.'
let tlds = 'bid date email farm golf party quest rest run shop surf trade work'.split(' ')
let domains = []
let receivedNonces = new Set()
let received = []
let sent = []

if (dev) {
  domains = [...tlds.keys()].map(e => 'http://localhost:'.concat((e + 3001).toString(10)))
} else {
  domains = tlds.map(tld => urlBase.concat(tld))
}

function openBid(which) {
  bid = window.open(domains[which], '_blank')
}

onMount(() => {
  window.addEventListener('message', (event) => {
    if (!domains.includes(event.origin)) return
    if (receivedNonces.has(`${event.origin}${event.data.nonce}`)) return
    // ignore if no nonce so it doesn't loop forever
    if (event.data && !event.data.nonce) return

    received = [...received, event.data]

    receivedNonces.add(event.data.nonce)

    let msg = {
      message: 'hello from games',
      to: event.origin,
    }
    // @ts-ignore
    event.source.postMessage(msg.message, msg.to)
    sent = [...sent, msg]
  }, false)
})
</script>

<p>please help me</p>

<div on:click={ () => { openBid(0) } }>test</div>

{#each domains as domain}
<span class="square">
<iframe id={domain} title="please help me {domain.split('.').pop()}" src={domain}></iframe>
</span>
{/each}

<h3>received</h3>
<ul>
  {#each received as receive}
    <li>{receive.message}: {receive.nonce}</li>
  {/each}
</ul>

<h3>sent</h3>
<ul>
  {#each sent as send}
    <li>{send.message}: {send.to}</li>
  {/each}
</ul>

<style>
iframe {
  width: 400px;
  height: 400px;
}

@media screen and (max-width: 600px) {
  iframe {
    width: 100vw;
    height: 100vw;
  }
}
</style>
