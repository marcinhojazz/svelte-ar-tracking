<script>
  import { Dialog, Button } from "smelte";
  import { writable } from "svelte/store";
  import { onMount } from "svelte";
  export let name;
  import createScene from './createScene';
  import createMarker from './createMarker';
  import { fly } from "svelte/transition";

  const ctx = createScene();

  let initialDialog = true;
  let showDialog = writable(false);

  const controls = [
    "plane",
    "lighthouse2",
    "bublik",
    "lift",
    "ship",
  ].map(title => createMarker(ctx, { title }, showDialog))


  animate();

  onMount(() => {
    document.getElementsByTagName("canvas")[0].style.zIndex = 100;
    document.getElementsByTagName("canvas")[0].style.pointerEvents = "none";
  })

  function openFullscreen() {
    var elem = document.documentElement;

    if (elem.requestFullscreen) {
      elem.requestFullscreen();
    } else if (elem.mozRequestFullScreen) {
      elem.mozRequestFullScreen();
    } else if (elem.webkitRequestFullscreen) {
      elem.webkitRequestFullscreen();
    } else if (elem.msRequestFullscreen) {
      elem.msRequestFullscreen();
    }
  }

  const titles = {
    plane: "Lorem Ipsum is simply dummy text of the printing",
    lighthouse2: "Lorem Ipsum is simply dummy text of the printing",
    bublik: "Lorem Ipsum is simply dummy text of the printing",
    lift: "Lorem Ipsum is simply dummy text of the printing",
    ship: "Lorem Ipsum is simply dummy text of the printing",
  };
  
  const descs = {
    plane: "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
    lighthouse2: "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
    bublik: "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
    lift: "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
    ship: "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
  }

  let desc, title;

  $: desc = descs[$showDialog.title];
  $: title = titles[$showDialog.title];

  function update() {
    if ($showDialog) return;

    ctx.updateScene();

    controls.forEach(c => c.update());
  }

  function animate() {
    requestAnimationFrame(animate);
    update();
    ctx.render();
    TWEEN.update();
  }

  function handleClick() {
    if (!$showDialog) {
      initialDialog = false;
      openFullscreen();
      return;
    }

    $showDialog.onClose();
  }
</script>

<style>
:global(body) {
  margin: 0px;
  overflow: hidden;
  cursor: pointer;
}

:global(.visible) {
	display: block;
	opacity: 1;
	transition: opacity 4s;
	transition-delay: 1s;
}

:global(.blurred) {
	filter: blur(10px) brightness(20%);
	transition: all 2s ease;
}

.dialog {
  width: 500px;
}

:global(z-200) {
  z-index: 200;
}
</style>

<svelte:window on:click={handleClick} on:touchend={handleClick} />


<div class="fixed top-0 left-0 h-full w-full z-50">
  <Dialog
    bind:value={$showDialog}
    opacity={0.8}
    scrimProps={{
      inProps:{ duration: 1000 },
      outProps:{ duration: 1000 },
    }}
    wrapperClasses="items-center z-10 rounded bg-primary-transDark text-white p-4 text-lg relative dialog py-10">
    <div class="px-10 max-w-2xl mx-auto z-200">
      <div transition:fly class="text-xl text-center">{title}</div>
      <hr class="mb-6">
      <div transition:fly class="font-light">
        <p>
          {desc}
        </p>
      </div>
    </div>
  </Dialog>

  <Dialog
    bind:value={initialDialog}
    opacity={0.8}
    scrimProps={{
      inProps:{ duration: 1000 },
      outProps:{ duration: 1000 },
    }}
    wrapperClasses="items-center z-10 rounded bg-transparent p-4 text-white text-lg relative">
    <Button
      outlined
      color="bg-red-500"
      remove="hover:bg-primary-trans"
      add="hover:bg-primary-transDark"
    >
      Aponte a camera para qualquer marca e clique na figura.
    </Button>
  </Dialog>

</div>