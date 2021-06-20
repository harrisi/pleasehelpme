<script>
import { onMount } from 'svelte'
let bid
let url = 'https://pleasehelpme.bid'

function openBid() {
  bid = window.open(url, '_blank')

  setTimeout(() => {
    bid.postMessage('test', url)
  }, 3000)
}

onMount(() => {
  window.addEventListener('message', (event) => {
    if (event.origin !== url) return

    console.log({ event })
  }, false)
})
</script>

<p>please help me</p>

<div on:click={ openBid }>test</div>
