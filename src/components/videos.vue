<template>
  <video autoplay loop :src="src"></video>
</template>

<script>
export default {
  name: "Videos",
  props: {
    src: String,
  },
};
</script>