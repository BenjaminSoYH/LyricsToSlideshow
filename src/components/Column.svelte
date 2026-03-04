<script lang="ts">
  export let style: string = "";
</script>

<div
  style={`display: flex; flex-direction: column; align-items: flex-start; ${style}`}
>
  <slot />
</div>