<script>
import { onMount } from 'svelte'
import { dev } from '$app/env'

let bid

let urlBase = 'https://pleasehelpme.'
let tlds = 'bid date email farm golf party quest rest run shop surf trade work'.split(' ')
let domains = []
let receivedNonces = new Set()

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
    if (receivedNonces.has(event.data.nonce)) return

    console.log(event.data.message)

    receivedNonces.add(event.data.nonce)

    // @ts-ignore
    event.source.postMessage('hello', event.origin)
  }, false)
})
</script>

<p>please help me</p>

<div on:click={ () => { openBid(0) } }>test</div>