<script context="module" lang="ts">
    let onTop   //keeping track of which open modal is on top
    const modals = {}  //all modals get registered here for easy future access

    // 	returns an object for the modal specified by `id`, which contains the API functions (`open` and `close` )
    export function getModal(id = '') {
        return modals[id]
    }
</script>

<script lang="ts">
    import {onDestroy} from 'svelte'
    import {browser} from '$app/environment';

    let topDiv
    let visible = false
    let prevOnTop
    let closeCallback

    export let id = ''
    export let hideClose = false

    function keyPress(ev) {
        //only respond if the current modal is the top one
        if (ev.key == "Escape" && onTop == topDiv) close(null) //ESC
    }

    /**  API **/
    function open(callback) {
        closeCallback = callback
        if (visible) return
        prevOnTop = onTop
        onTop = topDiv
        window.addEventListener("keydown", keyPress)

        //this prevents scrolling of the main window on larger screens
        document.body.style.overflow = "hidden"

        visible = true
        //Move the modal in the DOM to be the last child of <BODY> so that it can be on top of everything
        document.body.appendChild(topDiv)
    }

    function close(retVal) {
        if (!visible) return
        if (browser)
            window.removeEventListener("keydown", keyPress)
        onTop = prevOnTop
        if (onTop == null) document.body.style.overflow = ""
        visible = false
        if (closeCallback) closeCallback(retVal)
    }

    //expose the API
    modals[id] = {open, close}

    onDestroy(() => {
        delete modals[id]
        if (browser)
            window.removeEventListener("keydown", keyPress)
    })

</script>

<div id="topModal" class:visible bind:this={topDiv} on:click={()=>close()}>
    <div id='modal' on:click|stopPropagation={()=>{null}}>
        {#if !hideClose}
            <svg id="close" on:click={()=>close()} viewBox="0 0 12 12">
                <circle cx=6 cy=6 r=6 />
                <line x1=3 y1=3 x2=9 y2=9 />
                <line x1=9 y1=3 x2=3 y2=9 />
            </svg>
        {/if}
        <div id='modal-content'>
            <slot></slot>
        </div>
    </div>
</div>

<style>

  #topModal {
    visibility: hidden;
    z-index: 9999;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    backdrop-filter: blur(2px) grayscale(100%);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  #modal {
    position: relative;
    border-radius: 6px;
    background: #1f1f1f;
    color: #ffffff;
    filter: drop-shadow(0px 0px 5px #85F35E);
    padding: 1em;
  }

  .visible {
    visibility: visible !important;
  }

  #close {
    position: absolute;
    top: -12px;
    right: -12px;
    width: 24px;
    height: 24px;
    cursor: pointer;
    fill: #85F35E;
    transition: transform 0.3s;
  }

  #close:hover {
    transform: scale(1.2);
  }

  #close line {
    stroke: #1f1f1f;
    stroke-width: 2;
  }

  #modal-content {
    max-width: calc(100vw - 20px);
    max-height: calc(100vh - 20px);
    overflow: auto;
  }
</style>