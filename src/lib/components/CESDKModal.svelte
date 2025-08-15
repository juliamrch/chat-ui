<!-- src/lib/components/CESDKModal.svelte -->
<script>
  import { onMount, onDestroy } from 'svelte';
  import { showCESDKModal } from '$lib/stores/cesdk';

  /**
	 * @type {string | HTMLDivElement}
	 */
  let container;
  /**
	 * @type {import("@cesdk/cesdk-js").default | null}
	 */
  let cesdk = null;

  const config = {
    license: 'YOUR_LICENSE_KEY',
    callbacks: { onUpload: 'local' }
  };

  onMount(async () => {
    const { default: CreativeEditorSDK } = await import('@cesdk/cesdk-js');
    // @ts-ignore
    cesdk = await CreativeEditorSDK.create(container, config);
    await Promise.all([
      cesdk.addDefaultAssetSources(),
      cesdk.addDemoAssetSources({ sceneMode: 'Design' })
    ]);
    await cesdk.createDesignScene();
  });

  onDestroy(() => {
    cesdk?.dispose?.();
  });

  function close() {
    showCESDKModal.set(false);
  }
</script>

<div class="modal-backdrop" on:click|self={close}>
  <div class="modal-content">
    <button class="close-btn" on:click={close}>✕</button>
    <div bind:this={container} class="editor"></div>
  </div>
</div>

<style>
  .modal-backdrop {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.6);
    z-index: 999;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .modal-content {
    background: white;
    position: relative;
    width: 90vw;
    height: 90vh;
    border-radius: 8px;
    overflow: hidden;
  }

  .close-btn {
    position: absolute;
    top: 8px;
    right: 8px;
    background: transparent;
    border: none;
    font-size: 1.5rem;
    z-index: 1;
    cursor: pointer;
  }

  .editor {
    width: 100%;
    height: 100%;
  }
</style>
