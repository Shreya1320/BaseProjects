<template>
  <article class="result">
    <a v-if="image" :href="link" title="Link to wiki" target="_blank">
      <img :src="image" :alt="title" class="image">
    </a>
      <a :href="link" title="Link to wiki" target="_blank" class="title">
        <h2 class="title">{{ title }}</h2>
      </a>
      <p class="extract">{{ extract }}</p>
      <a class="wikiLink" :href="link">Read more on Wikipedia about {{ title }}</a>.
  </article>
</template>

<script>
export default {
  name: 'Results',
  props: {
    title   : String,
    extract : String,
    link    : String,
    image   : String,
  }
}
</script>

<style scoped>

</style>
