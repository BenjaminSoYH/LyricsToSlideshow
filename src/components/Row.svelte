<script lang="ts">
  export let style: string = "";
</script>

<div
  style={`display: flex; flex-direction: row; align-items: flex-start; ${style}`}
>
  <slot />
</div>